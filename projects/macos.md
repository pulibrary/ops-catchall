## macOS

### Step 1

Install  [iTerm.app](https://iterm2.com).

Add it to your dock; you'll be using it a lot. (To add it to the dock, click and hold the dock icon once Terminal is open. Select options -> keep in dock.)

### Step 2: Install A Compiler (XCode or GCC)

Go on to [Install Xcode](https://developer.apple.com/xcode/)

### Step 3: Install Homebrew

Type this in the terminal:

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

You may have to press 'ENTER' when prompted and type in your password.

**Verify**

Type this in the terminal:

```zsh
brew -v
```

Approximate expected result:

```
Homebrew 3.6.2-26-g4441452
Homebrew/homebrew-core (git revision 35dfb4426a6; last commit 2022-09-23)
Homebrew/homebrew-cask (git revision 79a5a21463; last commit 2022-09-23)
```

The resulting text may differ and is not important.

### Step 4: Install Git

Next we'll install Git, the most popular version control system in the Ruby world:

Type this in the terminal:

```zsh
brew install git
```

**Verify**

Type this in the terminal:

```zsh
git --version
```

Approximate expected result:

```
git version 2.37.3
```

The resulting text may differ and is not important.

### Step 5: Install asdf, the multiple runtime versions manager

[ASDF](https://asdf-vm.com) Manages each of your project runtimes with a single CLI tool and command interface.

#### Step 5.1: Install asdf

```zsh
brew install asdf
```

This will download and install asdf and it's dependencies.


#### Step 5.2: Configure your shell

Every time you open a new terminal window, rvm will be active inside it. Close your terminal window and open a new one.

```zsh
echo -e "\n. $(brew --prefix asdf)/libexec/asdf.sh" >> ${ZDOTDIR:-~}/.zshrc
```


### Step 6: Configure asdf to add ruby plugins

Type this in the terminal:

```zsh
brew install gpg gawk
```

### Step 7: Install Ruby

Switch to the directory you created by typing the following:

```zsh
cd ~/Documents/railsbridge
```

Type the following in the terminal:

```zsh
asdf plugin add ruby
asdf plugin-update ruby
asdf install ruby 3.1.2
```

This downloads and compiles Ruby, which takes a while.

Type this in the terminal:

```zsh
asdf local ruby 3.1.2
```

The command will write a file .tool-versions file in the current directory containing a Ruby version number.


**Verify**

Type this in the terminal:

```zsh
ruby -v
```

Approximate expected result:

```
ruby 3.1.2p20 (2022-04-12 revision 4491bb740a) [x86_64-darwin21]
```

The resulting text may differ and is not important.

### Step 8: Install Rails

Type this in the terminal:

```zsh
gem install rails
```

**Verify**

Type this in the terminal:

```zsh
rails -v
```

Approximate expected result:

```
Rails 7.0.4
```

The resulting text may differ. This is the version in fall of 2022

### Step 9: Install VSCode

We'll be using the VSCode text editor during the workshop, though you are free to use a different editor if you prefer. It must be a plain-text editor, such as vi or nano

#### Step 9.1: Download VSCode

Download the [VSCode Installer](https://code.visualstudio.com/Download)

#### Step 9.2: Find the downloaded file in Finder

If you weren't asked where to save it, it's probably in the Downloads folder.

#### Step 9.3: Extract VSCode and move it to your Applications folder.

Move on to [Configure Git](git.md)
