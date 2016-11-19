# PHP Screw Improved(ScrewIm) extension

## Abstract

***PHP Screw Imporved(ScrewIm)*** is a PHP script encryption tool. When you are developing a commercial package using PHP, the script can be distributed as encrypted up until just before execution. This preserves your intellectual property.

This extension is based from PHP screw who made by ***Kunimasa Noda*** in PM9.com, Inc. and, his information is follows:
 * Homepage: http://www.pm9.com
 * Email: kuni@pm9.com
 * Project page: http://www.pm9.com/newpm9/itbiz/php/phpscrew/

The differences from the original PHP-screw are as follows:
 1. Improved performance by processing in memory rather than creating temporary files during decoding.
 2. Improved performance by changing memory reallocation logic when encoding or decoding large files.
 3. Only works if 'screwim.enable' option is on.
  * Improved performance by don't check magic key under normal environment(regular php script).
  * See also https://github.com/OOPS-ORG-PHP/mod_screwim/issues/3
 4. Fixed memory leaks.
 5. Maybe thread safe.
 6. And so on..

## Description

***ScrewIm*** is encipher a PHP script with the encryption tool.

And, at the time of execution of a PHP script, the decryptor screwim.so is executed as PHP-Extension, just before the PHP script is handed to the Zend-Compiler.

In fact what is necessary is just to add the description about php.screw to php.ini. A PHP script programmer does not need to be conscious of decrypting process. Moreover it is possible for you to intermingle an enciphered script file and an unenciphered one.

The encryption logic in the encryption tool(screwim) and the decryption logic in the decryptor(screwim.so), can be customized easily.

The normal purpose code and decryption logic included, can be customized by only changing the encryption SEED key. Although it is easy to cusomize the encryption, by the encryption SEED, it does NOT mean, that the PHP scripts can be decrypted by others easily.

## License

Copyright (c) 2016 JoungKyun.Kim

[BSD 2-clause](LICENSE)

## Requirement

* ***PHP 5*** and after (Also support ***PHP 7***)
* PHP ***zlib*** extension.  
  Check that PHP has zlib compiled in it with the PHP script:
```php
    <?php gzopen(); ?>
```
* Unix like OS.

## Installation

### 1. Customize encrytion / decryption  
  * change the encryption SEED key (***screwim_mcryptkey***) in ***my_screw.h*** into the values according to what you like.
  * The encryption will be harder to break, if you add more values to the encryption SEED array.
  * However, the size of the SEED is unrelated to the time of the decrypt processing.
  * (***Optional***) Encrypted scripts get a stamp added to the beginning of the file. If you like, you may change this stamp defined by ***SCREWIM*** and ***SCREWIM_LEN*** in ***php_screwim.h***. ***SCREWIM_LEN*** must be less than or equal to the size of ***SCREWIM***.

### 2. Build and install  
  ```bash
  [root@host mod_screwim]$ phpize
  [root@host mod_screwim]$ ./configure
  [root@host mod_screwim]$ make install
  ```

### 3. Configuration
Add next line to php configuration file (php.ini and so on)

```ini
extension=screwim.so
screwim.enable = 1
```

By default, decryption does not work, so the performance of regular PHP files is better than the original PHP Screw. The screwim.enable option must be turned on for decryption to work. See also https://github.com/OOPS-ORG-PHP/mod_screwim/issues/3

## Encryption Tool

The encription tool is located in ***mod_screwim/tools/***.

### 1. Build

```bash
[root@host mod_screwim]$ cd tools
[root@host tools]$ ./autogen.sh
[root@host tools]$ ./configure --prefix=/usr
[root@host tools]$ make
[root@host tools]$ make install # Or copy the screwim file into an appropriate directory.
[root@host tools]$ /usr/bin/screwim -h
screwim 1.0.0 : encode or decode php file
Usage: screwim [OPTION] PHP_FILE
   -d,     --decode   decrypt encrypted php script
   -h,     --help     this help messages
   -H VAL, --hlen=VAL length of magic key(SCREWIM_LEN or PM9SCREW_LEN). use with -d mode
   -k VAL, --key=VAL  key bytes. use with -d mode
   -v,     --view     print head length and key byte of this file
[root@host tools]$
```

### 2. Encryptioin

The follow command creates the script file enciphered by the name of ***script file name .screw***.
```
   [root@host ~]$ /usr/bin/screwim test.php
   Success Crypting(test.php.screw)
   [root@host ~]$
```

### 3. Decryption

The follow command creates the script file enciphered by the name of ***script file name .disscrew***.
```
   [root@host ~]$ /usr/bin/screwim -d test.php.screw
   Success Decrypting(test.php.screw.discrew)
   [root@host ~]$
```

### 4. Execution

Add next line to php configuration file (php.ini and so on)

```ini
extension=screwim.so
```

By default, decryption does not work, so the performance of regular PHP files is better than the original PHP Screw. The screwim.enable option must be turned on for decryption to work.

Here is how to use the screwim.enable option:

#### 4.1. PHP configuration

```ini
screwim.enable = 1
```

It is not recommended because it causes performance degradation when processing unencrypted php scripts.

#### 4.2. mod_php (Apache module) envionment

Use the <directory> block to make the decryptor work on the desired path.

```apache
<Directory /path>
    php_falg screwim.enable on
</Directory>
```

#### 4.3. PHP Cli environments

use -d option.

```bash
[root@host ~]$ php -d screwim.enable=1 encrypted.php
```

#### 4.4. embeded php code (Recommand)

```php
<?php
ini_set ('screwim.enable', true);
require_once 'encrypted.php';
ini_set ('screwim.enable', false);
require_once 'normal.php';

blah_blah();
?>
```