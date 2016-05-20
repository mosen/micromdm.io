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
The `-pkg-repo` flag points to a folder that contains signed packages and [application manifests](http://help.apple.com/deployment/osx/#/ior5df10f73a).
How you choose to organize the directory is up to you, the server will make the files and directories available at `https://serverurl/repo`

### Signing Packages
Example using munkitools pkg.

You will need an app signing certificate from apple.
```bash
/usr/bin/productsign --sign "3rd Party Mac Developer Installer: Your Name (ID)" \
    ~/Downloads/munkitools-2.7.0.2757.pkg \
    /path/to/pkgrepo/pkgs/munkitools-2.7.0.2757.pkg
```

### Application Manifest
Documented by Apple [here](http://help.apple.com/deployment/osx/#/ior5df10f73a).
Pepijn Bruienne first described how to create an manifest plist for OS X pkgs [on his blog](http://enterprisemac.bruienne.com/2015/11/17/installing-os-x-pkgs-using-an-mdm-service/).

You can manually create a plist, or you can use the [`appmanifest`](https://github.com/micromdm/tools/releases/tag/v1) utility.
```bash
./appmanifest -url https://mdm.example.com/repo/pkgs/munkitools-2.7.0.2757.plist \
	/path/to/signed/munkitools-2.7.0.2757.pkg > /path/to/pkgrepo/manifests/munkitools-2.7.0.2757.plist
```
{{< note title="Metadata" >}}
The metadata portion of the plist seems to only matter for launchpad. It might be safe to omit it, but you can edit the plist and add it yourself as needed.
The information supplied in the metadata section should be accurate, but if you don't include any items in the items array, or use a bad identifier it does not seem to matter.
{{< /note >}}






