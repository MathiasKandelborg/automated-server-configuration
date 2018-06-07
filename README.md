# Automated Ubuntu server configuration

## A Project by [Mathias](https://www.linkedin.com/in/mathias-kandelsdorff/)

By following this guide, you'll to get a secure Ubuntu 18.04 server running and ready for use. This can be done from any environment.

Here's a list of some of the fancy technologies/software used in the project:

- Git
- Cloud-init
- Vagrant
- VirtualBox
- SSH

## Getting Started

This setup is made possible with a [virtual machine](https://en.wikipedia.org/wiki/Virtual_machine)(VM), to ensure that everybody can achieve the same results, without ever worrying about configuring their own environment.

### Prerequisites

Make sure [VirtualBox](https://www.virtualbox.org/) and [Vagrant](https://www.vagrantup.com/downloads.html) are installed. If they aren't installed yet, it's quite easy to get done by going to their websites and following the instructions for your OS.

The VM used in this project contains the instructions for setting up an Ubuntu 18.04 OS. This is done in order to ensure the possibility of generating secure SSH key-pairs with the `ssh-keygen` command.

### Local environment

Open a terminal and make a new project folder, then download this repository:

```bash
mkdir server-setup && cd server-setup
git clone https://github.com/MathiasKandelborg/server-configuration.git .
```

Now it's time to setup the VM. This generally takes some time as the computer is downloading, installing _and running_ an entire Operating System, in your current OS. This only needs to be set up once though.

```bash
vagrant up
```

When the installation processes are done, access the VM with this command:

```bash
vagrant ssh
```

#### Create an ssh key-pair and copy the contents of the public key

We'll create the key-pair with some extra flags for more security. We're also telling the program to create a 4096bit key instead of the standard 2048bit

```bash
ssh-keygen -f ~/generated-keys/server_id -t rsa -b 4096
```

> press 'enter' twice for no password.

You can choose a password if you want to, it's not needed for this guide but is good for security. The password is needed every time the key is used.

#### You are the sole possessor of your confidential files. Make sure to handle them that way

Needless to repeat, this is a secure server setup and you _need_ to figure out how **you** are going to handle your files - in a secure manner.

I cannot give any definitive suggestions to secure file & password handling, because there's so many situations to take into account - it's best if you take some time to learn about file & group permissions on computers + proper data-handling practices.

You might have noticed that two files popped up inside the `generated-keys` folder, the one with the .pub extension is for registering your key-pair. The other is *highly confidential*, if it gets compromised(shared with the real world) you have no defence against someone who has that key. This lack of defence means that your database(s), login credentials, command history and everything else on your server is free to access for _anyone_.

### Server setup with cloud-init

The cloud-init file is where everything that should get installed and be executed on first boot - is defined.

This setup is quite bare-bones but this is a guide to a secure server setup, not a complete one. ;)

Here's an overlook of the instructions defined in the file:

- Set the main user to 'admin'

- Disable running admin commands without sudo
- Disable root login functionality
- Define authorized SSH keys for user 'admin'
- Change the default SSH port to defer generic hacking attempts
- Disable password login on server
- Enable login for new user' admin' only
- Setup a firewall using the UFW
  - Opt-in on ingoing connections instead of opt-out
  - Disable default SSH connection
  - Enable default web hosting ports
- Set timezone to UTC

#### Configuring cloud-init / server setup

At this point we want to edit the cloud-init file to suit our environment.

The only thing we need to do right now, is input the newly generated SSH key:

- Copy the contents of server_id.pub
- Open up the `cloud-init` file in your text-editor/IDE
> cloud-init is a [yaml](https://en.wikipedia.org/wiki/YAML) file and indentation is really important
- Input *all* the contents of server_id.pub on line 8, from column 9

> If you choose to change the name of the user, remember to change it on line 12 as well. This is where we tell the server which users are allowed to connect to the server via SSH.

___

### Server environment

If you don't have an account at [Digital Ocean](https://www.digitalocean.com/) already, go make one right now [(here's my referral link, if, you know..)](https://m.do.co/c/acc19c3cd28f).

- Log into your account

- Press the green button with the words 'create' in the top-right corner
- Choose the 'Droplets' option
- At the start in the images dropdown-menu choose `Ubuntu 18.04 x64`
- Choose your preferred server size (the smallest is fine)
- Skip the sections about enabling backups and adding volumes
- Chose a data-center region closest to your location
- Select the following additional options:
  - IPv6
  - Monitoring
  - User data

After having chosen 'user data', a big text-box show up. Let's put something in that box!

This box is actually where most of the 'magic' happens.
In here, we'll input our cloud-init file, which sets up the server, with a pre-defined firewall and **one** user, that has access _only_ with our new SSH key.

Let's continue:

- Copy the contents of your customized cloud-init file
- Paste the file contents into the 'user data' text-box

> You can happily skip the SSH key section, as that functionality is what we've just done. We're just storing the key in our own, local environment. That is, not on a server somewhere we don't have control over it.

In the last section, you can define how many droplets you want and an optional hostname.

Now for the last and hardest part of setting up a server, I'm not sure any of us are ready for this:

- Click create!

It'll take a minute or two before the droplet is accessible with the custom configuration. In the meantime, let's go back to the terminal and access our VM.

___

### Accessing the remote server

In order to access the remote server, the fast way is to go to our project folder.

- Access the terminal with the VM running or run `vagrant up && vagrant ssh`

- Connect to the server

#### Alternatively, you can use any SSH program of your choice, as long as you can define the locations of your keys.

```bash
ssh -p 2222 admin@ip.to.customized.server -i ~/generated-keys.pub
```

**If you choose to use other software than provided, remember to change the connection port to 2222 instead of 22.** You can change the port on line 10. Change the `2222` part to the port of your choice. Remember to change the corresponding port on line 16.

For [some clarification on why or why not, read this](https://www.quora.com/Is-changing-the-SSHD-default-port-a-good-practice).

#### E.g. changing SSH port by editing Line 10 & 16

```bash
sed -i -e '/^#Port 22/s/^.*$/Port 9001/' /etc/ssh/sshd_config

 ...
 ...

ufw allow 9001/tcp
```

This would result in having to use port 9001, when connecting to this host.

## Built With

- [VSCode](https://code.visualstudio.com/) - The IDE & Text editor
- [VirtualBox](https://www.virtualbox.org/) - [VMM](https://en.wikipedia.org/wiki/Hypervisor)
- [Vagrant](https://www.vagrantup.com/) - [Fantastic tool for building and managing automated environments](https://www.vagrantup.com/intro/index.html)
- [Cloud-init](cloudinit.readthedocs.io) - The defacto multi-distribution package that handles early initialization of a cloud instance

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Author

- **Mathias Wøbbe** - *Initial work* - [MathiasKandelborg](https://github.com/MathiasKandelborg)
