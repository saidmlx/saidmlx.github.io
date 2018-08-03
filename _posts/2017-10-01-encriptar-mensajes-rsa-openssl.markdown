---
layout: post
title:  Encriptar y Desencriptar Mensajes RSA con OPENSSL
date:   2017-10-01 12:00:00 -0000
categories: Openssl
tags: Encriptación RSA Openssl
---

RSA es uno de los metodos preferidos para enviar mensajes, en este post mostrare como encriptar y desencriptar mensajes RSA con OpenSSL.

## Que necesitamos
* OPENSSL: 

#### Creamos la llave privada y la guardamos en el archivo private.pem 
```terminal
$ openssl genrsa -out private.pem 1024
```

Y tenemos
```terminal
$ cat public.pem

-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCvjq6TEI/iX/mzLihQLo+Zj0py
lnnhqI6xV9ZGLzZUwr5LzHrmVII5X0UDlcxIRxLfO/O7eIMbUSkPWHSGN5ycJx4p
JnGmxBm8R1FuNiuSDpsEQyUnKmKLEfZC4l6SO8mgmb/7OkpGnJew02OdB2y8ETQC
Qeypk0GujYB06x+urwIDAQAB
-----END PUBLIC KEY-----
```

#### creamos la llave publica a partir de la llave privada y la guardamos como public.pem

```terminal
$ openssl rsa -in private.pem -out public.pem -outform PEM -pubout
```
Y tenemos 
```
$ cat private.pem

-----BEGIN RSA PRIVATE KEY-----
MIICWwIBAAKBgQCvjq6TEI/iX/mzLihQLo+Zj0pylnnhqI6xV9ZGLzZUwr5LzHrm
VII5X0UDlcxIRxLfO/O7eIMbUSkPWHSGN5ycJx4pJnGmxBm8R1FuNiuSDpsEQyUn
KmKLEfZC4l6SO8mgmb/7OkpGnJew02OdB2y8ETQCQeypk0GujYB06x+urwIDAQAB
AoGAS1/tYEMaI82lPeMxd8ul5/53rl8hEVv3d/kIjLW0lAvQ/GZc1guupcdbsEBe
pyGs3p+08+a4E8By6p3F2eKCoGq0b7GLYfCKjhOAxFaOASc/XLs1KncubgX36uil
3zq6T3QmIazAWNuAueEzljUnmQZADlTbn39O93RdtWqEMCECQQDWvsATUYuLXk+Q
EI+bQTg4fIahAlCxpq4D7QZk2HJREYuPnGIo73k6oHX5Lw9UrEPdMiv5H1l1nyos
VO7imhdnAkEA0UipaoLcZDKtBSSYKAEd580LpfCjTRwcC75xTuqx/TqHJIuXAOxp
FmbCTeaIx1Cr5kOOSuUcMaoZEMBL28gJeQJAEsnhuE78LJd10p8AUY8lQhvzE4dG
VCTHOOS2GICvj9Q/J7OBCx/YtX5wk/rAFqJz1ZicLFHH2/ivEe3kcMMWpQJAYnU8
vs8xwDHXFERTEP71gsgEYt0C9pPy3dOKmwXvFqMiUR8au2R9S/ysDNoYHyzksj+2
AaYnzXWotSjCh3NkgQJALhNkEyorYLqWG2Ia/g7EfNpM/qS8DI2sObovMFe9KZq9
oTR0bBxJy8pv60Noxuc451eLjizJ509K+wlwcAD8NA==
-----END RSA PRIVATE KEY-----
```

#### Creamos el archivo a encriptar

```terminal
$ echo 'mi contraseña del face' > secreto.txt
```
Y tenemos
```terminal
$ cat secreto.txt

mi contraseña del face
```

#### Encriptamos el archivo secreto.txt a travez de la llave publica public.pem

```terminal
$  openssl rsautl -encrypt -inkey public.pem -pubin -in secreto.txt -out secreto.ssl
```

Y tenemos
```terminal
$ cat secreto.ssl
	
8?c???D)?@cF&1???Ǳ?????$[^fr??Y,G"P?`д??U?8Л??|???????˧?D_???SC??@??J0:???z?!?)d8z?,/v[??0bb?{]?ow?u??ִ?:?I
```

#### Desencriptamos
```terminal
$ openssl rsautl -decrypt -inkey private.pem -in secreto.ssl -out decrypted.txt
```

Y tenemos
```terminal
$ cat decrypted.txt

mi contraseña del face
```
