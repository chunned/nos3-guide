# Installation
Note: Internet required throughout installation process.

## Preparing Dependencies
Vagrant and VirtualBox are required. 

**On Windows**, use [Chocolatey](https://chocolatey.org/) to run the following command from a command prompt with administrator privileges: `choco install git vagrant virtualbox`

**On Ubuntu**, run the following commands. Refs: [1](https://developer.hashicorp.com/vagrant/downloads), [2](https://www.virtualbox.org/wiki/Linux_Downloads%20)
```
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install vagrant

wget https://download.virtualbox.org/virtualbox/7.0.16/virtualbox-7.0_7.0.16-162802~Ubuntu~jammy_amd64.deb

sudo dpkg -i virtualbox-7.0_7.0.16-162802~Ubuntu~jammy_amd64.deb
```

## Deploy

The below commands will deploy the VirtualBox VM that hosts the simulator. An explanation of the commands is below:

- Clone the repository into the desired destination
- Enter the repository directory
- Switch to the `dev` branch so we are using the latest copy of the repository with new features
- Initialize and update all submodules
- Configure and launch the VM with Vagrant


```
git clone https://github.com/nasa/nos3.git /path/to/destination/nos3
cd /path/to/destination/nos3
git checkout dev
git submodule update --init --recursive
vagrant up  # Can take a long time to complete, be patient!
```

### Build

Once the final command finishes, the VM should be up and you can log in as `jstar` with the password `jstar123!`.

Open a terminal and run `cd /home/jstar/Desktop/github-nos3`. Make sure the directory is not empty - if it is, Vagrant failed to properly configure the VM. Shutdown the VM and run `vagrant destroy`, then repeat the Deploy steps.

Before proceeding, you can make a choice between COSMO 4 and COSMO 5. Read more [here](https://github.com/nasa/nos3/wiki/NOS3-Ground-Systems). TL;DR - 4 is older looking native applications and 5 is more modern looking web interface. The default is COSMO 4 - to change this, open the file `~/Desktop/github-nos3/cfg/nos3-mission.xml` and change line 3 from `<gsw>cosmos</gsw>` to `<gsw>openc3</gsw>`.

Run the following commands in `~/Desktop/github-nos3`. If any step fails, run `make stop && make clean && rm -rf ~/.nos3` then repeat.

```
make clean
make prep
make
make launch
```
If using COSMO4, the application will launch. If using COSMO5, wait a while for configuration and then navigate to `localhost:2900`.
