## Armitage Debian 10/11/12 Installation Guide

This guide offers updated and optimized instructions for installing and running Armitage on Debian 12. Over the past year, I encountered numerous challenges when trying to get Armitage set up on my lightweight Debian system. After struggling with the original, often incomplete instructions, I decided to streamline the process to make future installations more straightforward.

These steps are specifically designed for Debian 12, tailored to address the unique aspects of this distribution. Having transitioned away from Kali Linux more than a decade ago, I've been building a custom, lightweight Debian setup to keep my system lean while still retaining the essential tools for penetration testing and security research. Armitage, being a favorite too of mine, was one of the programs I wanted to get running on my current setup. This guide condenses the installation process into a clear set of instructions, eliminating common pitfalls and unnecessary bloat, so you can efficiently install Armitage on Debian 12 with ease.

## 1. Reset the metasploit database:

```
msfdb stop
rm -rf ~/.msf4
msfdb init
```

## 2. Edit postgresql config file:

```
sudo nano /etc/postgresql/15/main/pg_hba.conf
```

Find the lines: 

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
```

Change ```scram-sha-256``` to ```trust```

Save and Exit.

## 3. Restart postgresql service:

```
sudo systemctl enable postgresql
sudo systemctl stop postgresql
sudo systemctl start postgresql
```

## 4. Check/switch java install version:

```java -version```

- If the output shows anything other than JDK 11, you need to install JDK 11.
- If JDK 11 is already installed but not active, switch to it using the command below.

```sudo update-alternatives --config java```

## 5. Install JDK 11

Create a file called ```pin-stable-unstable.pref``` in /etc/apt/preferences.d/ and copy and past the following into the file:

```
Package: *
Pin: release a=stable
Pin-Priority: 900

Package: *
Pin: release a=unstable
Pin-Priority: 50
```

Save and Exit.

Edit your sources file:

```
sudo nano /etc/apt/sources.list
```

Add the following line to the end of the file:

```
deb http://deb.debian.org/debian unstable main non-free contrib
```

Save and Exit.

```
sudo apt update
sudo apt install openjdk-11-jdk
``` 

Only execute the next command if you have multiple java versions installed.

```
sudo update-alternatives --config java
```

## 6. Remove unstable source from your sources.list

```
sudo nano /etc/apt/sources.list
deb http://deb.debian.org/debian unstable main non-free contrib
```

Save and Exit.

## 7. Download and install armitage:

```
cd /opt
sudo git clone https://github.com/Saajaadeen/armitage.git
cd armitage
sudo ./package.sh
```

## 8. Set enviroment variables:

```
export MSF_DATABASE_CONFIG=/home/$USER/.msf4/database.yml
```

## 9. Run armitage:

```
cd /opt/armitage/release/unix
./armitage
```

## Happy Hunting!

## 10. Troubleshooting:

You may need to run commands from section 8 and 9 together, sometimes when i set the enviroment variable and then run ./armitage it throws an error that i didnt set the enviroment variable or tells me to restart postgresql so run the command below to start armitage.

```
export MSF_DATABASE_CONFIG=/home/$USER/.msf4/database.yml && ./armitage
```

## Sources:

- Install JDK 11: https://ralph.blog.imixs.com/2024/01/24/install-open-jdk-11-on-debian-12-bookworm/
- Set env vars: https://askubuntu.com/questions/1001711/armitage-error-could-not-connect-to-database (user: Rodrigo Azzolini)


