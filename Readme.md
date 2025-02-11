# Certbot DNS DuckDNS Plugin

Plugin for certbot for a DNS-01 challenge with a DuckDNS domain.

---

![PyPI](https://img.shields.io/pypi/v/certbot_dns_duckdns) ![PyPI - Python Version](https://img.shields.io/pypi/pyversions/certbot_dns_duckdns) [![GitHub](https://img.shields.io/github/license/infinityofspace/certbot_dns_duckdns)](https://github.com/infinityofspace/certbot_dns_duckdns/blob/master/License) ![PyPI - Downloads](https://img.shields.io/pypi/dm/certbot_dns_duckdns) ![GitHub Workflow Status](https://img.shields.io/github/workflow/status/infinityofspace/certbot_dns_duckdns/Publish%20release%20distribution%20to%20PyPI)
![Docker Image Version (latest semver)](https://img.shields.io/docker/v/infinityofspace/certbot_dns_duckdns?sort=semver) ![Docker Image Size (latest semver)](https://img.shields.io/docker/image-size/infinityofspace/certbot_dns_duckdns?sort=semver) ![GitHub Workflow Status](https://img.shields.io/github/workflow/status/infinityofspace/certbot_dns_duckdns/build%20and%20publish%20release%20to%20Docker%20Hub)
---

### Table of Contents

1. [About](#about)
2. [Installation](#installation)
    1. [Prerequirements](#prerequirements)
    2. [With pip (recommend)](#with-pip-recommend)
    3. [From source](#from-source)
3. [Usage](#usage)
    1. [Local installation usage](#local-installation-usage)
    2. [Docker usage](#docker-usage)
4. [FAQ](#faq)
5. [Third party notices](#third-party-notices)
6. [License](#license)

---

### About

*certbot_dn_duckdns* is a plugin for [*certbot*](https://github.com/certbot/certbot) to create the DNS-01 challenge for
a DuckDNS domain. The plugin takes care of setting and deleting the TXT entry via the DuckDNS API.

### Installation

#### Prerequirements

*If you want to use the docker image, then you don't need any requirements other than a working docker installation and
can proceed directly to the [usage](#docker-usage)*

If you prefer the local installation, then you need at least version 3.6 of Python installed. If you want to install
this plugin with pip, then you also need pip3 installed.

If you already have *certbot* installed, make sure you have at least version 1.7.0 installed.

You can check what version of *certbot* is installed with this command:

```commandline
certbot --version
```

If you don't have certbot installed yet, then the PyPI version of certbot will be installed automatically during the
installation.

**Note: If you want to run certbot with root privileges, then you need to install the plugin with root privileges too.
Otherwise, certbot cannot find the plugin.**

#### With pip (recommend)

Use the following command to install *certbot_dns_duckdns* with pip:

```commandline
pip install certbot_dns_duckdns
```

You can also very easily update to a newer version:

```commandline
pip install certbot_dns_duckdns -U
```

#### From source

```commandline
git clone https://github.com/infinityofspace/certbot_dns_duckdns
cd certbot_dns_duckdns
pip install .
```

### Usage

*Note: You cannot create certificates for multiple DuckDNS domains with one certbot call. This is because DuckDNS only
allows one TXT record. If certificates for several domains should be created at the same time, then the same number of
distinct DNS TXT records must be created. To solve the problem, you simply have to make a separate certbot call for each
domain.*

**Note that the certificate generation through Letsencrypt has rate limits. For testing, use the additional
argument `--staging` to solve this problem.**

#### Local installation usage

To check if the plugin is installed correctly and detected properly by certbot, you can use the following command:

```commandline
certbot plugins
```

---

Below are some examples of how to use the plugin:

Generate a certificate for a DNS-01 challenge of the domain "example.duckdns.org":

```commandline
certbot certonly \
  --non-interactive \
  --agree-tos \
  --email <your-email> \
  --preferred-challenges dns \
  --authenticator dns-duckdns \
  --dns-duckdns-token <your-duckdns-token> \
  --dns-duckdns-propagation-seconds 60 \
  -d "example.duckdns.org"
```

Generate a certificate for a DNS-01 challenge of the subdomain "cloud.example.duckdns.org":

```commandline
certbot certonly \
  --non-interactive \
  --agree-tos \
  --email <your-email> \
  --preferred-challenges dns \
  --authenticator dns-duckdns \
  --dns-duckdns-token <your-duckdns-token> \
  --dns-duckdns-propagation-seconds 60 \
  -d "cloud.example.duckdns.org"
```

Generate a wildcard certificate for a DNS-01 challenge of all subdomains "*.example.duckdns.org":

```commandline
certbot certonly \
  --non-interactive \
  --agree-tos \
  --email <your-email> \
  --preferred-challenges dns \
  --authenticator dns-duckdns \
  --dns-duckdns-token <your-duckdns-token> \
  --dns-duckdns-propagation-seconds 60 \
  -d "*.example.duckdns.org"
```

Generate a certificate for a DNS-01 challenge of the domain "example.duckdns.org" without an account (i.e. without an
email address):

```commandline
certbot certonly \
  --non-interactive \
  --agree-tos \
  --register-unsafely-without-email \
  --preferred-challenges dns \
  --authenticator dns-duckdns \
  --dns-duckdns-token <your-duckdns-token> \
  --dns-duckdns-propagation-seconds 60 \
  -d "example.duckdns.org"
```

Generate a staging certificate (i.e. temporary testing certificate) for a DNS-01 challenge of the domain "
example.duckdns.org":

```commandline
certbot certonly \
  --non-interactive \
  --agree-tos \
  --email <your-email> \
  --preferred-challenges dns \
  --authenticator dns-duckdns \
  --dns-duckdns-token <your-duckdns-token> \
  --dns-duckdns-propagation-seconds 60 \
  -d "example.duckdns.org" \
  --staging
```

Try to update all currently generated certificates:

```commandline
certbot renew
```

You can find al list of all available certbot cli options in
the [official documentation](https://certbot.eff.org/docs/using.html#certbot-command-line-options) of *certbot*.

#### Docker usage

You can simply start a new container and use the same certbot commands to obtain a new certificate:

```commandline
docker run -v "/etc/letsencrypt:/etc/letsencrypt" -v "/var/log/letsencrypt:/var/log/letsencrypt" infinityofspace/certbot_dns_duckdns:latest \
   certbot certonly \
     --non-interactive \
     --agree-tos \
     --email <your-email> \
     --preferred-challenges dns \
     --authenticator dns-duckdns \
     --dns-duckdns-token <your-duckdns-token> \
     --dns-duckdns-propagation-seconds 60 \
     -d "example.duckdns.org"
```

If you want to use the docker image to renew your certificates automatically, you can do this with the host cron, for
example. To use this example you must have crontab and cron installed beforehand. Note that depending on the
installation you may need to use the crontab of a root user to access the docker deamon or file directories. For
example, use the following crontab expression:

```
0 3 */8 * * docker run --rm -v "/etc/letsencrypt:/etc/letsencrypt" -v "/var/log/letsencrypt:/var/log/letsencrypt" infinityofspace/certbot_dns_duckdns:latest certbot renew
```

This will start an temporary docker container every 8 days at 3am and tries to to renew expiring certificates.

An example for the usage with docker-compose can be found [here](docker/simple/Readme.md).

### FAQ

You can the FAQ in the [wiki](https://github.com/infinityofspace/certbot_dns_duckdns/wiki/FAQ).

### Third party notices

All modules used by this project are listed below:

| Name | License|
|:---:|:---:|
| [certbot](https://github.com/certbot/certbot) | [Apache 2.0](https://raw.githubusercontent.com/certbot/certbot/master/LICENSE.txt) |
| [requests](https://github.com/psf/requests) | [Apache 2.0](https://raw.githubusercontent.com/psf/requests/master/LICENSE) |
| [zope.interface](https://github.com/zopefoundation/zope.interface) | [ZPL-2.1](https://raw.githubusercontent.com/zopefoundation/zope.interface/master/LICENSE.txt) |
| [setuptools](https://github.com/pypa/setuptools) | [MIT](https://raw.githubusercontent.com/pypa/setuptools/main/LICENSE) |
| [dnspython](https://github.com/rthalley/dnspython) | [ISC](https://raw.githubusercontent.com/rthalley/dnspython/master/LICENSE) |

Furthermore, this readme file contains embeddings of [Shields.io](https://github.com/badges/shields).

### License

[MIT](License) - Copyright (c) 2021 Marvin Heptner
