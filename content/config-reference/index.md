---
date: 2016-07-01T10:49:13-04:00
title: Configuration Reference
---

## Supported flags and environment variables

### `--url` `MICROMDM_URL` Externally facing URL
You must supply an externally facing URL if you are using the enrollment endpoint `/mdm/enroll` so that the check-in
and server URLs can be automatically generated. This url should include the port.

**Example**

> --url https://micromdm.domain.org:8443

### `--port` `MICROMDM_HTTP_LISTEN_PORT` Listen port
The port to listen on. Defaults to 443 or 80 if not using TLS.

### `--tls` `MICROMDM_USE_TLS` Use TLS
Pass this flag to use TLS. This requires you to supply all of the certificate flags as well.

### `--tls-cert` `MICROMDM_TLS_CERT` Path to TLS certificate
This flag is required if you are using TLS.

### `--tls-key` `MICROMDM_TLS_KEY` Path to TLS private key

### `--tls-ca-cert` `MICROMDM_TLS_CA_CERT` Path to certificate authority certificate
If supplied, this will be included in the enrollment profile so that the device can trust your self-signed or enterprise
CA certificates.

### `--scep-url` `MICROMDM_SCEP_URL` URL to a scep service
A SCEP service is required if you want to use the `/mdm/enroll` URL to generate enrollment certificates.

### `--postgres` `MICROMDM_POSTGRES_CONN_URL` PostgreSQL connection URL
Supply a connection string for the postgres database.
This can be either a connection URI, example:

> --postgres 'postgres://micromdm:micromdm@db/micromdm?sslmode=disable'

OR in this form

> --postgres 'user=micromdm password=micromdm dbname=micromdm sslmode=disable'

### `--redis` `MICROMDM_REDIS_CONN_URL` Redis connection URL

### `--push-cert` `MICROMDM_PUSH_CERT` Path to push certificate (PKCS#12 format)

### `--push-pass` `MICROMDM_PUSH_PASS` Password for push certificate

### `--profile` `MICROMDM_ENROLL_PROFILE` Path to enrollment profile
Currently this profile is supplied to DEP devices only.

### `--dep-consumer-key` `DEP_CONSUMER_KEY` DEP consumer key

### `--dep-consumer-secret` `DEP_CONSUMER_SECRET` DEP consumer secret

### `--dep-access-token` `DEP_ACCESS_TOKEN` DEP access token

### `--dep-access-secret` `DEP_ACCESS_SECRET` DEP access secret

### `--depsim` `DEP_USE_DEPSIM` Use depsim default credentials

### `--dep-server-url` `DEP_SERVER_URL` DEP server url, for testing only.
This is normally only used when you are running with depsim. Leave blank otherwise.

### `--pkg-repo` `PKG_REPO` Package repository path

### `--cors-origin` `MICROMDM_CORS_ORIGIN` CORS origins allowed
Use this if you are developing a web based system that needs to make cross-origin requests to the MDM.
Specify '*' as the value if you don't care about where the requests are coming from.


