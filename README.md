# OpenSSL-1.1.0 Patch

## What would it do

Make the CHACHA20-POLY1305 ciphersuites the first option on those devices without AES instruction-sets.

Based on client's SSL cipher sequence.

## How to use:

### Apply patch for OpenSSL:

```
cd ~

curl -O https://www.openssl.org/source/openssl-1.1.0h.tar.gz
curl https://www.openssl.org/source/openssl-1.1.0h.tar.gz.sha256
sha256sum https://www.openssl.org/source/openssl-1.1.0h.tar.gz
## Compare the digest, check the integrity of the source code.

tar -zxvf openssl-1.1.0h.tar.gz
cd openssl-1.1.0h/

patch -p1 < ../chacha_priority.patch
## Assume the patch file is in your home directory(~)

## Assign a directory to avoid the system version of OpenSSL being covered,
## Which may leads to unexpected result.
./config --prefix={install_path} --openssldir=/etc/ssl --Wl,rpath={install_path}/lib
make -j
make install
```

### Use with applications depend on OpenSSL:

0. Recompile other Apps depends on OpenSSL 1.1.0, add `LDFLAGS="--Wl,rpath={install_path}/lib"` to use the libraries you just compiled.
0. Use OpenSSL config command `Options:+PrioritizeChaCha` to enable this feature
0. Do _NOT_ set CHACHA ciphersuites as the first one. (Which will make chacha _ALWAYS_ be the first alternative)

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
