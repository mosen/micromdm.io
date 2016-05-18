---
date: 2016-05-16T18:56:13-04:00
title: Getting Started
---

## Installation
### Requirements
* MicroMDM binary - see download
* [PostgreSQL](http://www.postgresql.org/) - used for long lived data
* [Redis](http://redis.io/) - used to queue MDM commands
* MDM Push Certificate - see section on certificates
* SSL Certificate - see section on certificates
* Enrollment Profile - template provided
* DEP Server token -- optional, required for DEP integration/enrollment
* server must be accessible on the internet

### Configure and Run
MicroMDM can be configured by providing the necessary flags or setting environment variables.
To see all available commands, run `./micromdm -help`.
The command will assume some defaults, but there are still some necessary flags to get started.
```sh
sudo ./micromdm \
    -tls-cert=/path/to/server.crt \
    -tls-key=/path/to/server.key \
    -postgres='user=micromdm password=micromdm dbname=micromdm sslmode=disable' \
    -redis='127.0.0.1:6379'  \
    -profile=/path/to/Enroll.mobileconfig \
    -push-cert=path/to/mdm.p12 \
    -push-pass='secret_password' \
    # pkg-repo is the path to a folder containing application manifests and signed packages
    -pkg-repo=/path/to/pkgrepo \
    -dep-consumer-key "CK_48dd68d198350f512..." \
    -dep-consumer-secret "CS_34c7b2b531a600d..." \
    -dep-access-token "AT_927696831c59ba" \
    -dep-access-secret "AS_c31afd7a09691d8354848"
```


## Certificates
### Server Certificate
### Push Certificate

## Enrollment Profile

## DEP Token

To obtain a DEP token, you will have to upload a PEM encoded copy of the server X.509 certificate with the public key to [DEP portal](https://deploy.apple.com). The DEP service will create some credentials for you and encrypt them with the servers public key. 
You'll be give a PKCS#7 encrypted file named `myserver_Token_2016-04-09T23-50-41Z_smime.p7m`.  

To use the token with MicroMDM, first run 
```sh
openssl smime -decrypt -in myserver_Token_2016-04-09T23-50-41Z_smime.p7m -inkey /path/to/server/privatekey.pem > stoken.json
```
You can use the OAuth credentials from `stoken.json` as the `-dep-...` flags in `micromdm` command.

## Package Repo
### Application Manifest
