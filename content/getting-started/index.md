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
Getting all the necessary certificates in place can be confusing; there are several certificate chains which are not related. Let's try to break these requirements down and explain how each certificate is used.

### Server Certificate
The MDM server needs to be secured by TLS. If you have a PKI solution in place, you can just create a server certificate and key for the MDM just like you would for securing any other HTTPS server. 
You can also create a self signed certificate. In the case of using your own CA and self signed cert you will need to add the certificate chain, all the way to the Root CA in a profile to allow the device to trust the server.

### Push Certificate
The Push certificate allows the server to send a MDM push notification to a device, which causes the device to query the server for available commands.   
The Push certificate is created at `https://identity.apple.com/pushcert/` by uploading a CSR signed by an MDM Vendor Certificate. Because we are our own MDM provider, we must be the ones that sign the push certificate with our own vendor certificate.  
The MDM Vendor Certificate is only available to organizations that have an Enterprise Developer account, which requires a $300/year fee. Even if your organization has an enterprise account, the `MDM CSR` option is not immediately visible. The administrator of the enterprise account must open a support ticket with Apple and request that the `MDM CSR` option is available. Once Apple enable the MDM CSR option, you can upload a CSR for apple to sign it and create a MDM Vendor certificate.

For those who have the MDM CSR option enabled, MicroMDM provides a [`certhelper`](https://github.com/micromdm/tools/tree/master/certhelper) utility to reduce the number of steps needed to create the MDM CSR, sign a push CSR and format a push certificate request.
```bash
# creates an MDM CSR and private key for vendor cert
# upload the created MDM CSR to enterprise portal to get a push certificate
certhelper vendor -csr -cn=mdm-certtool -password=secret -country=US -email=foo@gmail.com

# create a "provider" or a "customer" CSR. 
# This CSR will be signed by the vendor cert and submitted to apple to get a push cert
certhelper provider -csr -cn=mdm-certtool -password=secret -country=US -email=foo@gmail.com

# sign the provider csr with the vendor private key
# assumes `mdm.cer` is in the folder with all the other files. You can specify each path separately as well.
certhelper vendor -sign -password=secret

# Now upload the PushCertificateRequest to https://identity.apple.com/pushcert
```

### Device Authentication
The third part of certificate management is verifying device identity. The MDM protocol requires client certificates for devices to authenticate to the server.
One way for a device to have a client certificate is to use SCEP. MicroMDM will support this in the future. 

A second approach is to have the server CA sign a certificate for each device. The certificate can be embedded in the enrollment profile in a PKCS#12 format and passed on to the device. At the momment, we must do this manually.
```bash
# create a private key for the device
openssl genrsa 2048 > identity.key

# create a CSR using the private key
openssl req -new -key identity.key -out identity.csr

# use the server CA to sign the CSR
openssl x509 -req -days 365 -in identity.csr -CA cacert.crt -CAkey cakey.key -CAcreateserial -out identity.crt

# export the certificate as PKCS#12 using a passphrase.
openssl pkcs12 -export -out identity.p12 -inkey identity.key -in identity.crt -certfile cacert.crt
```

The identity.p12 file and passphrase can be added in the enrollment profile of the device.  
The next release of MicroMDM will add an enrollment endpoint which will automate the creation of the enrollment profile and certificate.


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

### Creating and Signing Packages
Note that only distribution style packages are supported. 
Use `productbuild` to turn a component flat pkg into a distribution one.
```bash
productbuild --package someFlatPkg.pkg myNewPkg.pkg
```

Example signing using munkitools pkg.   
You will need an app signing certificate from apple.
```bash
/usr/bin/productsign --sign "3rd Party Mac Developer Installer: Your Name (ID)" \
    ~/Downloads/munkitools-2.7.0.2757.pkg \
    /path/to/pkgrepo/pkgs/munkitools-2.7.0.2757.pkg
```

### Application Manifest
Documented by Apple [here](http://help.apple.com/deployment/osx/#/ior5df10f73a).
Pepijn Bruienne first described how to create an manifest plist for OS X pkgs [on his blog](http://enterprisemac.bruienne.com/2015/11/17/installing-os-x-pkgs-using-an-mdm-service/).

The default `md5-size` value is 10MB, but can be set to anything. The array of md5 hashes must match the md5-size specified in the md5-size field.
In case of a connection error, the client will retry the download starting with the last succesful chunk, so it's a good idea to keep the sizes relatively small.

You can manually create a plist, or you can use the [`appmanifest`](https://github.com/micromdm/tools/releases/tag/v1) utility.
```bash
./appmanifest -url https://mdm.example.com/repo/pkgs/munkitools-2.7.0.2757.plist \
	/path/to/signed/munkitools-2.7.0.2757.pkg > /path/to/pkgrepo/manifests/munkitools-2.7.0.2757.plist
```
{{< note title="Metadata" >}}
The metadata portion of the plist seems to only matter for launchpad. It might be safe to omit it, but you can edit the plist and add it yourself as needed.
The information supplied in the metadata section should be accurate, but if you don't include any items in the items array, or use a bad identifier it does not seem to matter.
{{< /note >}}






