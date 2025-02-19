---
title: Cipher suites
order: 1
pcx-content-type: reference
---

import CipherSuitesDefinition from "../_partials/_cipher-suites-definition.md"

# Cipher suites — Edge certificates

<CipherSuitesDefinition/>

Cloudflare publishes a [public repository of our SSL/TLS configurations](https://github.com/cloudflare/sslconfig) on GitHub. You can find changes in the commit history. 

We no longer support [RC4 cipher suites](https://blog.cloudflare.com/end-of-the-road-for-rc4/) or [SSLv3](https://blog.cloudflare.com/sslv3-support-disabled-by-default-due-to-vulnerability/).

--------

## Supported cipher suites by protocol

OpenSSL Name | TLS 1.0 | TLS 1.1 | TLS 1.2 | TLS 1.3
------|-------------|---------------|---------------|---------------
ECDHE-ECDSA-AES128-GCM-SHA256|❌|❌|✅|❌
ECDHE-ECDSA-CHACHA20-POLY1305|❌|❌|✅|❌
ECDHE-RSA-AES128-GCM-SHA256|❌|❌|✅|❌
ECDHE-RSA-CHACHA20-POLY1305|❌|❌|✅|❌
ECDHE-ECDSA-AES128-SHA256|❌|❌|✅|❌
ECDHE-ECDSA-AES128-SHA|✅|✅|✅|❌
ECDHE-RSA-AES128-SHA256|❌|❌|✅|❌
ECDHE-RSA-AES128-SHA|✅|✅|✅|❌
AES128-GCM-SHA256|❌|❌|✅|❌
AES128-SHA256|❌|❌|✅|❌
AES128-SHA|✅|✅|✅|❌
ECDHE-ECDSA-AES256-GCM-SHA384|❌|❌|✅|❌
ECDHE-ECDSA-AES256-SHA384|❌|❌|✅|❌
ECDHE-RSA-AES256-GCM-SHA384|❌|❌|✅|❌
ECDHE-RSA-AES256-SHA384|❌|❌|✅|❌
ECDHE-RSA-AES256-SHA|✅	|✅|✅|❌
AES256-GCM-SHA384|❌|❌|✅|❌
AES256-SHA256|❌|❌|✅|❌
AES256-SHA|✅|✅|✅|❌
DES-CBC3-SHA|✅|❌|❌|❌
AEAD-AES128-GCM-SHA256 [^1]|❌|❌|❌|✅
AEAD-AES256-GCM-SHA384 [^1]|❌|❌|❌|✅
AEAD-CHACHA20-POLY1305-SHA256 [^1]|❌|❌|❌|✅

--------

## Disable cipher suites

With **Advanced Certificate Manager** or within **SSL for SaaS**, you can restrict connections to specific cipher suites. Currently, this functionality is only available when using the API:

- [Zone](https://api.cloudflare.com/#zone-settings-change-ciphers-setting)
- [Hostname (SSL for SaaS only)](https://api.cloudflare.com/#custom-hostname-for-a-zone-create-custom-hostname)

To specify certain [cipher suites](#supported-cipher-suites-by-protocol), include an array of applicable options in the `value` field.

To reset to the default cipher suites, send an empty array in the `value` field.

--------

## Matching on origin (optional)

Cloudflare will [present the cipher suites to your origin](/origin-configuration/cipher-suites/) and your server will select whichever cipher suite it prefers.

However, if you want to ensure that your origin server supports the same cipher suites that Cloudflare supports at our edge, use the following NGINX configuration. If you are terminating TLS on your origin using a different method, refer to that application’s documentation.

```txt
ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
ssl_ecdh_curve X25519:P-256:P-384;
ssl_ciphers '[ECDHE-ECDSA-AES128-GCM-SHA256|ECDHE-ECDSA-CHACHA20-POLY1305|ECDHE-RSA-AES128-GCM-SHA256|ECDHE-RSA-CHACHA20-POLY1305]:ECDHE+AES128:RSA+AES128:ECDHE+AES256:RSA+AES256:ECDHE+3DES:RSA+3DES';
ssl_prefer_server_ciphers on;
```

[^1]: Although TLS 1.3 uses the same cipher suite space as previous versions of TLS, TLS 1.3 cipher suites are defined differently. They only specifying the symmetric ciphers and cannot be used for TLS 1.2. Similarly, TLS 1.2 and lower cipher suites cannot be used with TLS 1.3 (IETF TLS 1.3 draft 21). BoringSSL also hard-codes cipher preferences in this order for TLS 1.3.