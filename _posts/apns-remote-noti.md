---
title:  Apns远程推送证书生成
date:   2015-04-27 15:30:26
categories: 
- iOS
tags:
- apns
---
```
openssl x509 -in aps_development.cer -inform DER -out aps_development.pem

openssl pkcs12 -nocerts -out Push_Noenc.pem -in Push.p12

openssl pkcs12 -export -in aps_development.pem -inkey Push_Noenc.pem -certfile CertificateSigningRequest.certSigningRequest -name "aps_developer_identity" -out aps_developer_identity.p12

telnet gateway.sandbox.push.apple.com 2195
Trying 17.110.227.100...
Connected to gateway.sandbox.push-apple.com.akadns.net.
Escape character is '^]'.
```