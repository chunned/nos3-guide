# Installation
Note: Internet required throughout installation process.

This assumes a Windows system using Chocolatey.
```
choco install git vagrant virtualbox
git clone https://github.com/nasa/nos3.git /path/to/destination/nos3
cd /path/to/destination/nos3
git checkout dev
git submodule update --init --recursive
vagrant up  # Can take a long time to complete, be patient!
```
Once the final command finishes, the VM should be up and you can log in as `jstar` with the password `jstar123!`.

Open a terminal and run `cd /home/jstar/github-nos3`. Before proceeding, you can make a choice between COSMO4 and COSMO5. Read more [here](https://github.com/nasa/nos3/wiki/NOS3-Ground-Systems). TL;DR - 4 is older looking native applications and 5 is more modern looking web interface.

```
make clean
make prep
make
make launch
```
If using COSMO4, the application will launch. If using COSMO5, wait a while for configuration and then navigate to `localhost:2900`.
