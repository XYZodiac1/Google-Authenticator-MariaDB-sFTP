# Google Authenticator PAM Setup for MariaDB

## Introduction

This guide will show you how to configure Google Authenticator with PAM (Pluggable Authentication Module) for two-factor authentication (2FA) in a MariaDB server.

## Commands

### Install Dependencies

```bash
apt update
apt install libqrencode-dev libtool libpam-dev autoconf make -y
cd /root
git clone https://github.com/google/google-authenticator-libpam.git
cd google-authenticator-libpam
./bootstrap.sh
./configure --libdir=/lib/x86_64-linux-gnu
make
make install
```

### Installing and configuring MariaDB

```bash
apt install mariadb-server
mysql -u root -p
> INSTALL SONAME 'auth_pam';
> create user new_user identified via pam;

echo -e "auth required pam_unix.so\nauth required pam_google_authenticator.so\naccount required pam_unix.so" | sudo tee -a /etc/pam.d/mysql

sudo sed -i 's/ProtectHome=true/ProtectHome=false/' /lib/systemd/system/mariadb.service
systecmctl daemon-reload
systemctl restart mariadb
```

### Using Google Authenticator on a New User
```bash
adduser new_user
su new_user
./google-authenticator -t -C -f -d -r 5 -R 15 -w 5
```

### Set the correct permissions
```bash
sudo chown new_user:new_user /home/new_user/.google_authenticator
sudo chmod 600 /home/new_user/.google_authenticator
```









