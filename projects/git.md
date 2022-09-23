## Configure Git (on your MacOS not your sandbox)

You may already have setup up your git. If you have not here's direction. In the next commands, don't copy-paste the whole thing. Use the name and email address you usually use. Don't use a username (like your github username).
Type this in the terminal:

```zsh
git config --global user.name "Your Actual Name, In Quotes"
```

Type this in the terminal:

```zsh
git config --global user.email "Your Actual Email, In Quotes"
```

Type this in the terminal to install and configure your pager.

```zsh
brew install bat
git config --global core.pager bat
```

**Verify**

Type this in the terminal:

```zsh
git config --get user.name
```

Approximate expected result:

```
Your Name
```

Type this in the terminal:

```zsh
git config --get user.email
```

Approximate expected result:

```
Your email address
```

Move on to [Configure SSH keys](sshkeys.md) if you haven't already
