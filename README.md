Just contains nginx, php-fpm and php-worker.


Take a look at which modules are available by default:

```bash

/var/www # php -m

[PHP Modules]
Core
ctype
curl
date
dom
fileinfo
filter
ftp
gd
hash
iconv
igbinary
intl
json
libxml
mbstring
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
redis
Reflection
session
SimpleXML
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zip
zlib

[Zend Modules]

```

Are you satisfied?

BUT notice that
> By default there is no mapping of the ` vendor ` directory.
