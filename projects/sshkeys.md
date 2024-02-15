## Create An SSH Key

An SSH key uniquely identifies you (and your computer) when your computer is communicating with other computers. Think of an SSH key as a fancy password.

You'll need one of these to connect to your sandbox and Github accounts.

### Do you have a preexisting SSH key for some other reason?

Maybe you went to a previous RailsBridge workshop, or generated an SSH key to push some code to GitHub? You can check with the following command:

```zsh
ls ~/.ssh/id_rsa
```

If you see the message No such file or directory, you don't have an SSH key yet, Move to the next step. If you already have keys, move on to the Transfer your keys step

###  Generate an SSH key

Type this in the terminal:

```zsh
ssh-keygen -C yourpreferred@email.addr -t rsa
```

Enter a real passphrase. Twice. Press enter to accept the default key save location.

After key generation is complete, you'll have output that looks like this.

Expected result:

```zsh
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/student/.ssh/id_rsa):
Created directory '/Users/student/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/student/.ssh/id_rsa.
Your public key has been saved in /Users/student/.ssh/id_rsa.pub.
The key fingerprint is:
88:54:ab:77:fe:5c:c3:7s:14:37:28:8c:1d:ef:2a:8d yourpreferred@email.addr
```

### Transfer your keys

If you haven't yet. Copy your key to your sandbox by typing the following in your terminal:

```zsh
ssh-copy-id pulsys@sandbox
```

Transfer your keys to [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

Move on to [Creating your Rails](rails_app.md)