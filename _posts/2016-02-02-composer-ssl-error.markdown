---
layout: post_new
title: "Composer报Composer\\Downloader\\TransportException错误的解决方法"
description: ""
tags: [Sonarqube]
published: true
---
执行`Composer`命令时，可能会报以下错误（以self-update命令为例）：

~~~bash
$ composer self-update                                  
                                                                                    
  [Composer\Downloader\TransportException]
  The "https://getcomposer.org/version" file could not be downloaded: SSL operation failed with code 1. OpenSSL Error messages:
  error:14090086:SSL routines:SSL3_GET_SERVER_CERTIFICATE:certificate verify failed
  Failed to enable crypto
  failed to open stream: operation failed                                                                                         

~~~

从错误描述可以看到原因是OpenSSL验证证书失败。我们首先看下证书文件的存放路径：

~~~bash
$ php -r "print_r(openssl_get_cert_locations());"
Array
(
    [default_cert_file] => /usr/local/ssl/cert.pem
    [default_cert_file_env] => SSL_CERT_FILE
    [default_cert_dir] => /usr/local/ssl/certs
    [default_cert_dir_env] => SSL_CERT_DIR
    [default_private_dir] => /usr/local/ssl/private
    [default_default_cert_area] => /usr/local/ssl
    [ini_cafile] => 
    [ini_capath] => 
)
~~~

`default_cert_file`表示默认的证书文件，`ini_cafile`表示`php.ini`里设置的证书文件。`ini_cafile`会比`default_cert_file`的优先级高。可以看到，`ini_cafile`的值为空，所以目前证书文件为`/usr/local/ssl/cert.pem`。

一般来说，会报以上的错误，要么是cert.pem文件不存在，或者这个文件有问题，所以我们只要弄一个新的文件下来就可以解决问题。

根据上面说的情况，我们有两个解决方式。一是直接把文件下载到`default_cert_file`指定的文件，这里就是`/usr/local/ssl/cert.pem`：

~~~bash
$ wget http://curl.haxx.se/ca/cacert.pem -O /usr/local/ssl/cert.pem
~~~

二是修改`php.ini`文件，增加以下的配置：

~~~ini
openssl.cafile=/path/to/cert.pem
~~~

> 关于这个配置项的详情，请参考PHP的[用户手册](http://cn2.php.net/manual/zh/openssl.configuration.php){:target="_blank"}。

一切正常的话，问题就得到解决：

~~~bash
$ composer self-update
Updating to version 7117a5775ffdcdfd31bbd52a138a6f9c65e7e3c2.
    Downloading: 100%         
Use composer self-update --rollback to return to version 72cd6afdfce16f36a9fd786bc1b2f32b851e764f
~~~