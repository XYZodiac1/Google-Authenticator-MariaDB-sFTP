# Google Authenticator PAM Setup for MariaDB

## Introduction

This guide will show you how to configure Google Authenticator with PAM (Pluggable Authentication Module) for two-factor authentication (2FA) in a MariaDB server.

## Commands

### Install Dependencies

Installing the updated version of Google-Authenticator. If you installed from the repository keep in mind that isn't the latest version of Google Authenticator.
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

We are using the username `new_user` just for testing purposes
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

You can run the `./google-authenticator` without the parameters and manually select what settings you prefer
```bash
adduser new_user
su new_user
./google-authenticator -t -C -f -d -r 5 -R 15 -w 5
```

### Set the correct permissions

MySQL must be able to read the google-authenticator file
```bash
sudo chown new_user:new_user /home/new_user/.google_authenticator
sudo chmod 600 /home/new_user/.google_authenticator
```

### Receiving the Authentication Token

You can get the Token from a tool such as "browserscan.net/2fa" and use the secret token or use the APP Google Authenticator on your mobile phone and scan the generated QR code.

### Testing

Once you've done all the steps above you can test by trying to authenticate using the credentials for new user.
```bash
mysql -u new_user -p
```
