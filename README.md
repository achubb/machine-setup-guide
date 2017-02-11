# Setup steps for a new MacOS machine
This is a collection of personal system preferences and configuration values to help when setting up a new machine. I primarily do web development in the Laravel framework.

## Brew
Brew is a package manager that can be used to manage all of the applications and packages on MacOS. Install by running:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

I also install brew bundle. This creates a Bundlefile of packages that you have installed on your machine, and should you need to reinstall based on these packages you can install from the Bundlefile. Install using

```
brew tap Homebrew/bundle
```

Then after you have installed some packages run:

```
brew bundle dump --force
```

This will generate a new Bundlefile if you don't have one, or overwrite an existing one, then populate it with the packages that you have installed with brew.

## Brew Cask
Many apps are available via Homebrew cask, which makes installing and updating apps much easier. The [search page](https://caskroom.github.io/search) will list all the apps that are installable using cask.

## gitignore.io
This is a useful command line tool for generating .gitignore files. Install using:

```
$ echo "function gi() { curl -L -s https://www.gitignore.io/api/\$@ ;}" >> ~/.zshrc && source ~/.zshrc
```

Then generate .gitignore files by passing in common environments such as

```
gi macos >> .gitignore
```

## iTerm
- Iterm theme is modded version of [Misterioso](https://github.com/flooose/misterioso-iterm2)
- Black is changed to a 90% grey
- Font is custom version of [Input Mono](http://input.fontbureau.com/)

## zsh
- Theme is [Bullet Train](https://github.com/caiogondim/bullet-train.zsh), and uses the Input Mono font. To install clone the repo and copy the .theme file to .oh-my-zsh/themes. Now set the theme in .zshrc

## ssh
Generate a new public key by entering the following command:

```
ssh-keygen -t rsa
```

Then follow the instructions to generate a new public key. The key can be copied to the clipboard by running:

```
pbcopy < ~/.ssh/id_rsa.pub
```

You can paste this key in to use it with a service such as Github. Alternatively if you need to upload your public key to a server, ssh has a command to to this

```
ssh-copy-id USERNAME@SERVERNAME
```

## Setting up PHP and Composer
Grab the latest version of php and install with brew. Then run the installer script below:

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

This will install composer, now move the composer.phar file so that it is part of your PATH and can be accessed globally.

```
mv composer.phar /usr/local/bin/composer
```

## Setting up Vagrant and Homestead
Start by installing VirtualBox and Vagrant with brew. Once these are downloaded and installed proceed with installing homestead. Start by adding the homestead box to vagrant:

```
vagrant box add laravel/homestead
```

Now create a Homestead directory in your home folder, this is where the Homestead server will run. Into this folder clone Homestead from Github.

```
git clone https://github.com/laravel/homestead.git Homestead
```

Now run the init.sh to generate a Homestead.yaml configuration file.

```
bash init.sh
```

In the configuration file you can assign folders that you want homestead to serve, by default the directory "Code" in your home directory is already set up to be synced in vagrant. You will need to set up individual sites in the sites section of the configuration file. The map value is the domain, the to value is the public folder of the Laravel site as it appears on the Vagrant box. i.e.

```
- map: andychubb.local
  to: /home/vagrant/Code/andychubb.com/public
```

Whenever you add or remove a site, you will need to re-provision Vagrant.

```
vagrant reload --provision
```

You will also need to add an entry to your hosts file to point to the server

```
192.168.10.10  andychubb.local
```

When homestead is configured run **vagrant up** within the homestead directory to start the server.

NOTE - If using Homestead a lot, it is probably worth putting this homestead function into .zshrc, it will allow you to run commands such as "homestead up" and "homestead halt" from anywhere on the system.

```
function homestead() {
    ( cd ~/Homestead && vagrant $* )
}
```

## Setting up Laravel

At this point if you have php and composer set up, you should just be able to run **composer global require "laravel/installer"** to install Laravel globally.

In order to run the laravel command globally add the following line to .zshrc

```
export PATH=$HOME/.composer/vendor/bin:$PATH
```

Now you should be able to run commands such as **laravel new blog** to scaffold Laravel projects.
