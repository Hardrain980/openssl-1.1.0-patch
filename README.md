# OpenSSL-1.1.0 Patch

## What would it do

Make the CHACHA20-POLY1305 ciphersuites the first option on those devices without AES instruction-sets.

Based on client's SSL cipher sequence.

## How to use:

0. Download the 4 files in the patch
0. Replace the corresponding in the OpenSSL 1.1.0 source as directed:
  0. `ssl_conf.c` and `s3_lib.c` are in `{openssl_source}/ssl/`
  0. `apps.h` is in `{openssl_source}/apps/`
  0. `ssl.h` is in `{openssl_source}/include/openssl/`
0. Compile, don't forgot told `ld` where the shared libraries are with `--Wl,rpath=`
0. Install
0. Recompile other Apps depends on OpenSSL 1.1.0, use `--Wl,rpath=` to link the libraries you just compiled.
0. Use OpenSSL config command `Options:+PrioritizeChaCha` to enable this feature
0. Don't set CHACHA20-POLY1305 ciphersuites as the first one. (It would interfere this feature.)

Example configuration for Apache 2.4:
```
SSLCipherSuite ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305
SSLProtocol -All +TLSv1.2
SSLOpenSSLConfCmd Options +PrioritizeChaCha
```
-------

### Reference
0. OpenSSL Source of version 1.1.0g and 1.1.1-pre4
0. [https://www.openssl.org/docs/man1.1.1/man3/SSL_CONF_cmd_value_type.html](https://www.openssl.org/docs/man1.1.1/man3/SSL_CONF_cmd_value_type.html)
0. [https://httpd.apache.org/docs/trunk/mod/mod_ssl.html#sslopensslconfcmd](https://httpd.apache.org/docs/trunk/mod/mod_ssl.html#sslopensslconfcmd)
