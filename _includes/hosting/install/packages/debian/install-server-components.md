## 1. Install the server components
### 1.1. Package repository setup

For easier installation and update tasks Passbolt provides a package repository that you need to setup
before you download Passbolt {{ product | upcase }} and install it.

These steps assume you have already installed sudo and added your user to the sudo group.

**Step 1.** Update the apt indexes and install packages to allow apt to use https repositories:

```
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

Optionally you can install certbot to enable [Let's Encrypt](https://letsencrypt.org/) configuration:

```
sudo apt-get install certbot python3-certbot-nginx
```

**Step 2.**  Add Passbolt package official GnuPG key from keys.mailvelope.com:

```
gpg --keyserver hkps://keys.mailvelope.com --receive-keys 0xDE8B853FC155581D 
```

Or alternatively from hkps://pgp.mit.edu or hkps://keys.gnupg.net.

**Step 3.**  Check that the GPG fingerprint matches `3D1A 0346 C8E1 802F 774A  EF21 DE8B 853F C155 581D`

```
gpg --list-key --with-fingerprint 0xDE8B853FC155581D
```

Must return:

```
pub   rsa2048 2020-05-18 [SC] [expires: 2022-05-18]
      3D1A 0346 C8E1 802F 774A  EF21 DE8B 853F C155 581D
uid           [ unknown] Passbolt SA package signing key <contact@passbolt.com>
sub   rsa2048 2020-05-18 [E] [expires: 2022-05-18]
```

**Step 4.** Create APT GPG keyring

```
gpg --export 0xDE8B853FC155581D | sudo tee \
  /usr/share/keyrings/passbolt-repository.gpg >/dev/null
```

**Step 5.**  Add passbolt repository:

```
cat << EOF | sudo tee /etc/apt/sources.list.d/passbolt.sources > /dev/null
Types: deb
URIs: https://download.passbolt.com/{{ product }}/{{ distribution }}
Suites: {{ distributionVersionName }}
Components: stable
Signed-By: /usr/share/keyrings/passbolt-repository.gpg
EOF
```

**Step 6.**  Update the apt indexes with the new passbolt apt repository:

```
sudo apt-get update
```

### 1.2. Install passbolt

By default, passbolt {{ distributionLabel }} package will install Passbolt server component, mariadb-server, php-fpm and nginx
as dependencies.

There are two main ways to install the passbolt {{ distributionLabel }} package:

- **Interactive**: the package will guide the user through a set of questions to setup mariadb and nginx. If you are
going to use existing SSL certs for the web server, they need to be created and installed to the location of your choosing before
beginning. The user will be asked for the path and name of the certificate and key.
- **Non-interactive**: no questions will be asked. Useful for users with specific needs or users that want to automate the
installation. [Read this FAQ page to know more](/faq/hosting/how-to-install-passbolt-non-interactive)

#### Install passbolt package

Install the main passbolt server component:

```
sudo apt-get install passbolt-{{product}}-server
```