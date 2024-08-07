# :material-clipboard-check-multiple: Prerequisites

## Creating a GitHub Account
Please do the following if you'd like to import a CACAO template:

[Create a GitHub Account](https://github.com){target=_blank}

## Creating an SSH Key Pair

If you need to create a new ssh key pair, you can follow these steps on the command line. These steps assume a Linux environment with openssh client installed and that you will use this terminal (and account) to ssh into any CACAO-provisioned vms.

1. open a terminal or terminal window
2. type: `openssh-keygen -t ed25519`
???+ success "Expected Response"
    ```bash linenums="1"
    Generating public/private ed25519 key pair.
    Enter file in which to save the key (/home/demouser/.ssh/id_ed25519): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/demouser/.ssh/id_ed25519
    Your public key has been saved in /home/demouser/.ssh/id_ed25519.pub
    The key fingerprint is:
    SHA256:Fw3O5Ih54IdGnW9BT/MOFDII73E8WuaOVxhKPJQ1ovI root@itsummit-deploy
    The key's randomart image is:
    +--[ED25519 256]--+
    |      +oo*O =.   |
    |     o X=O.X o   |
    |    . B X.%.+ .  |
    |     + = Xo= o   |
    |      E S.+ . .  |
    |         + .     |
    |        . o      |
    |         .       |
    |                 |
    +----[SHA256]-----+
    ```
3. You can then use the public key found `/home/demouser/.ssh/id_ed25519.pub`.




