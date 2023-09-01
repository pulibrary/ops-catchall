## Don't Panic!

### Overview

We have [nginx](https://www.nginx.com) running on [OpenBSD](https://www.openbsd.org) as a load balancer. This allows us to mirror -to a degree- what we have on-Prem. There are a few subtle differences to Nginx and NginxPlus that this will enlighten in addition to the Operating System that this document will try to articulate.

#### Build and Deploy

 * We have machine images on [Google Cloud Platform](https://console.cloud.google.com/compute/instancesAdd?project=pul-gcdc&creationFlow=fromMachineImage) that you can create from. From the Google Cloud Console you can select a new instance from machine image and select the latest snapshot.

 * Install nginx can be done using the following command:
   ```bash
   doas pkg_add -viD"snap" nginx nginx-lua
   ```
 * Enable nginx to start on boot of the Operating system with the following command:
   ````bash
   doas rcctl enable nginx
   ````
 * The configuration file, located at `/etc/nginx/nginx.conf` will need at least the following blocks in the examples below.
   * an upstream block defining the block of an upstream server
   ````conf
    upstream example { 
     server 10.0.20.3:81;
    }
    ````
   * a redirecting port 80 server block
   ````conf
    server {
     listen 80;
     server_name example-dev.princeton.edu;

     location / {
         return 301 https://$server_name$request_uri;
        }
    }
    ````
   * a TLS server block (this is not a working example)
    ````conf
     server {
     listen 443 ssl;
     server_name example-dev.princeton.edu;

     ssl_certificate   /etc/ssl/example-dev.chained.pem;
     ssl_certificate_key /etc/ssl/private/example-dev.key;
     ssl_session_cache          shared:SSL:1m;
     ssl_prefer_server_ciphers  on;
     ssl_protocols TLSv1.2 TLSv1.3;

     location / {
        proxy_pass http://example-dev;
        proxy_buffering on;
        proxy_busy_buffers_size   512k;
        proxy_buffers   4 512k;
        proxy_buffer_size   256k;
        proxy_http_version 1.1;
        proxy_headers_hash_bucket_size 128;
        proxy_headers_hash_max_size 1024;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Proto $scheme;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        }
     }
     ````

* Follow the instructions at `/usr/local/share/doc/pkg-readmes/nginx` and add logrotation. The involve removing `httpd(8)` from `/etc/newsyslog.conf` and replacing them with:
     ````conf
     /var/www/logs/access.log		644  4     *    $W0   Z /var/run/nginx.pid SIGUSR1
     /var/www/logs/error.log			644  7     250  *     Z /var/run/nginx.pid SIGUSR1
     ````
#### Common Tasks

  * To update the OS run the following:
    ````bash
    doas sysupgrade -s
    ````
    this will reboot the Operating System
    ````bash
    doas sysmerge
    doas pkg_add -vuiD"snap"
    ````

#### Make Attached disk visible

In the rare case that we have a full disk and have [attached a disk](https://cloud.google.com/compute/docs/disks/add-persistent-disk), the steps below will make it available to the Operating System

  * To list all our available disks:
    ````bash
    dmesg | grep -E '(sd[0-9]|wd[0-9])'
    ````
    output will look something like
    ````bash
    sd0 at scsibus1 targ 1 lun 0: <Google, PersistentDisk, 1> serial.Google_PersistentDisk_
    sd0: 30720MB, 512 bytes/sector, 62914560 sectors, thin
    sd1 at scsibus1 targ 2 lun 0: <Google, PersistentDisk, 1> serial.Google_PersistentDisk_
    sd1: 102400MB, 512 bytes/sector, 209715200 sectors, thin
    root on sd0a (3b7d099be243012b.a) swap on sd0b dump on sd0b
    ````
*WARNING*: Double check your commands and make certain you are editing the correct disk. You must substitute the disk in the example for the one you actually want to format. Typing the wrong disk by accident could destroy all your data forever. In the example below we have attached `sd1` and `sd0` has the running Operating System.

  * Create a new partition with:
    ````bash
    doas fdisk -iy sd1
    ````
    This automatically creates an fdisk partition that spans the entire disk
  * We will use [disklabel](https://man.openbsd.org/disklabel) to manage the OpenBSD filesystem partitions (these disklabel partitions exist inside the fdisk partition from the previous step)
    ````bash
    doas disklabel -E sd1
    ````
    output will look something like this
    ````bash
    Label editor (enter '?' for help at any prompt)
    sd1>
    ````
  * We want to add the `a` partition. We use the defaults for offset and filesystem type
    ````bash
    sd1> a a
    offset: [64] 
    size: [209715136] 
    FS type: [4.2BSD]
    ````
  * Write the changes to disk and exit
    ````bash
    sd1*> w
    sd1> q
    No label changes.
    ````
  * We will now format the disk
    ````bash
    doas newfs sd1a
    ````
  * We now have sd1a formatted, but we need to mount it. Let's look at where our system's partitions are currently mounted:
    ````bash
    mount
    ````
    The output will look something like this
    ````bash
    /dev/sd0a on / type ffs (local)
    /dev/sd0g on /home type ffs (local, nodev, nosuid)
    /dev/sd0d on /tmp type ffs (local, nodev, nosuid)
    /dev/sd0f on /usr type ffs (local, nodev, wxallowed)
    /dev/sd0e on /var type ffs (local, nodev, nosuid)
    ````
  * We can look at the human readable size of the partitions with
    ````bash
    Filesystem     Size    Used   Avail Capacity  Mounted on
    /dev/sd0a      1.9G    119M    1.7G     7%    /
    /dev/sd0g      7.7G    509M    6.8G     7%    /home
    /dev/sd0d      1.9G   10.0K    1.8G     1%    /tmp
    /dev/sd0f      7.1G    2.2G    4.6G    33%    /usr
    /dev/sd0e      8.4G   18.6M    8.0G     1%    /var
    ````
  * For this example, `/var` is too small for our server, so we're going to copy all the data from `sd0e` to `sd1`, then mount `sd1a` as the new `/var`.
    ````bash
    doas su
    mount /dev/sd1a /mnt
    cd /mnt
    ````
  * Then we [dump](https://man.openbsd.org/dump) and [restore](https://man.openbsd.org/restore) with:
    ````bash
    dump -0 -a -f - /home | restore -rf -
    ````
  * Verify this worked with:
    ````bash
    ls -a /mnt/
    ````
  * unmount the copied drive with the following:
    ````bash
    cd ~
    umount /mnt/
    ````
  * To mount this partition at boot we will need to add it to `/etc/fstab` Our example `/etc/fstab` looks like this:
    ````bash
    3b7d099be243012b.b none swap sw
    3b7d099be243012b.a / ffs rw 1 1
    3b7d099be243012b.g /home ffs rw,nodev,nosuid 1 2
    3b7d099be243012b.d /tmp ffs rw,nodev,nosuid 1 2
    3b7d099be243012b.f /usr ffs rw,wxallowed,nodev 1 2
    3b7d099be243012b.e /var ffs rw,nodev,nosuid 1 2
    ````
    The string `3b7d099be243012b` is called the disklabel unique identifier (DUID). Each disk partition has its own unique DUID. Let's find the DUID of our new disk partition:
    ````bash
    sysctl hw.disknames
    ````
    We get the following output
    ````bash
    hw.disknames=sd0:3b7d099be243012b,sd1:f38dc631b8064930
    ````
    The DUID for sd1 is `f38dc631b8064930`. So, we need to replace `/var`'s partition `3b7d099be243012b`.e with `f38dc631b8064930`.a (since we are trying to mount sd1a):
    ````bash
    3b7d099be243012b.b none swap sw
    3b7d099be243012b.a / ffs rw 1 1
    3b7d099be243012b.g /home ffs rw,nodev,nosuid 1 2
    3b7d099be243012b.d /tmp ffs rw,nodev,nosuid 1 2
    3b7d099be243012b.f /usr ffs rw,wxallowed,nodev 1 2
    f38dc631b8064930.a /var ffs rw,nodev,nosuid 1 2
    ````
  * Then, reboot
    ````bash
    doas shutdown -r now
    ````
