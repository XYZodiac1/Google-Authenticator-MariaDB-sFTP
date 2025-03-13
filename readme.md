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
> create user teste identified via pam;

nano /etc/pam.d/mysql
auth            required        pam_unix.so
auth            required        pam_google_authenticator.so
account         required        pam_unix.so

```



