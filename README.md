# COS-PHP-SDK-V5

腾讯云 COS-PHP-SDK-V5（[XML API](https://cloud.tencent.com/document/product/436/7751)）

[![Latest Stable Version](https://poser.pugx.org/qcloud/cos-sdk-v5/v/stable)](https://packagist.org/packages/qcloud/cos-sdk-v5)
[![Total Downloads](https://img.shields.io/packagist/dt/qcloud/cos-sdk-v5.svg?style=flat)](https://packagist.org/packages/qcloud/cos-sdk-v5)
[![Build Status](https://travis-ci.com/tencentyun/cos-php-sdk-v5.svg?branch=master)](https://travis-ci.com/tencentyun/cos-php-sdk-v5)
[![codecov](https://codecov.io/gh/tencentyun/cos-php-sdk-v5/branch/master/graph/badge.svg)](https://codecov.io/gh/tencentyun/cos-php-sdk-v5)

## 环境准备

- PHP 7.2+ 您可以通过`php -v`命令查看当前的 PHP 版本。

- cURL 扩展 您可以通过`php -m`命令查看 cURL 扩展是否已经安装好。

> - Ubuntu 系统中，您可以使用 apt-get 包管理器安装 PHP 的 cURL 扩展，安装命令如下。

```
sudo apt-get install php-curl
```

> - CentOS 系统中，您可以使用 yum 包管理器安装 PHP 的 cURL 扩展。

```
sudo yum install php-curl
```

#### 安装步骤：

使用 Composer 安装，执行以下命令。

```
composer require qcloud/cos-sdk-v5 -vv
```

现在您的项目已经可以使用 COS 的 V5 版本 SDK 了。


## 快速入门

可参照 Demo 程序，详见 [sample 目录](https://github.com/tencentyun/cos-php-sdk-v5/tree/master/sample)。

## 接口文档

PHP SDK 接口文档，详见 [https://cloud.tencent.com/document/product/436/12267](https://cloud.tencent.com/document/product/436/12267)

### 配置文件

```php
$cosClient = new Qcloud\Cos\Client(array(
    'region' => '<Region>',
    'credentials' => array(
        'secretId' => '<SecretId>',
        'secretKey' => '<SecretKey>'
    )
));
```

若您使用 [临时密钥](https://cloud.tencent.com/document/product/436/14048) 初始化，请用下面方式创建实例。

```php
$cosClient = new \Qcloud\Cos\Client(array(
    'region' => '<Region>',
    'credentials' => array(
        'secretId' => '<SecretId>',
        'secretKey' => '<SecretKey>',
        'token' => '<XCosSecurityToken>'
    )
));
```

### 上传文件

- 使用 putObject 接口上传文件(最大 5G)
- 使用 Upload 接口分块上传文件

```php
# 上传文件
## putObject(上传接口，最大支持上传5G文件)
### 上传内存中的字符串
//bucket 的命名规则为{name}-{appid} ，此处填写的存储桶名称必须为此格式
try {
    $result = $cosClient->putObject(array(
        'Bucket' => $bucket,
        'Key' => $key,
        'Body' => 'Hello World!'));
    print_r($result);
} catch (\Exception $e) {
    echo "$e\n";
}

### 上传文件流
try {
    $result = $cosClient->putObject(array(
        'Bucket' => $bucket,
        'Key' => $key,
        'Body' => fopen($local_path, 'rb')));
    print_r($result);
} catch (\Exception $e) {
    echo "$e\n";
}

### 设置header和meta
try {
    $result = $cosClient->putObject(array(
        'Bucket' => $bucket,
        'Key' => $key,
        'Body' => fopen($local_path, 'rb'),
        'ACL' => 'string',
        'CacheControl' => 'string',
        'ContentDisposition' => 'string',
        'ContentEncoding' => 'string',
        'ContentLanguage' => 'string',
        'ContentLength' => integer,
        'ContentType' => 'string',
        'Expires' => 'mixed type: string (date format)|int (unix timestamp)|\DateTime',
        'Metadata' => array(
            'string' => 'string',
        ),
        'StorageClass' => 'string'));
    print_r($result);
} catch (\Exception $e) {
    echo "$e\n";
}

## Upload(高级上传接口，默认使用分块上传最大支持50T)
### 上传内存中的字符串
try {
    $result = $cosClient->Upload(
        $bucket = $bucket,
        $key = $key,
        $body = 'Hello World!');
    print_r($result);
} catch (\Exception $e) {
    echo "$e\n";
}

### 上传文件流
try {
    $result = $cosClient->Upload(
        $bucket = $bucket,
        $key = $key,
        $body = fopen($local_path, 'rb'));
    print_r($result);
} catch (\Exception $e) {
    echo "$e\n";
}

### 设置header和meta
try {
    $result = $cosClient->Upload(
        $bucket= $bucket,
        $key = $key,
        $body = fopen($local_path, 'rb'),
        $options = array(
            'ACL' => 'string',
            'CacheControl' => 'string',
            'ContentDisposition' => 'string',
            'ContentEncoding' => 'string',
            'ContentLanguage' => 'string',
            'ContentLength' => integer,
            'ContentType' => 'string',
            'Expires' => 'mixed type: string (date format)|int (unix timestamp)|\DateTime',
            'Metadata' => array(
                'string' => 'string',
            ),
            'StorageClass' => 'string'));
    print_r($result);
} catch (\Exception $e) {
    echo "$e\n";
}
```

### 下载文件

- 使用 getObject 接口下载文件
- 使用 getObjectUrl 接口获取文件下载 URL

```php
# 下载文件
## getObject(下载文件)
### 下载到内存
//bucket 的命名规则为{name}-{appid} ，此处填写的存储桶名称必须为此格式
try {
    $result = $cosClient->getObject(array(
        'Bucket' => $bucket,
        'Key' => $key));
    echo($result['Body']);
} catch (\Exception $e) {
    echo "$e\n";
}

### 下载到本地
try {
    $result = $cosClient->getObject(array(
        'Bucket' => $bucket,
        'Key' => $key,
        'SaveAs' => $local_path));
} catch (\Exception $e) {
    echo "$e\n";
}

### 指定下载范围
/*
 * Range 字段格式为 'bytes=a-b'
 */
try {
    $result = $cosClient->getObject(array(
        'Bucket' => $bucket,
        'Key' => $key,
        'Range' => 'bytes=0-10',
        'SaveAs' => $local_path));
} catch (\Exception $e) {
    echo "$e\n";
}

### 设置返回header
try {
    $result = $cosClient->getObject(array(
        'Bucket' => $bucket,
        'Key' => $key,
        'ResponseCacheControl' => 'string',
        'ResponseContentDisposition' => 'string',
        'ResponseContentEncoding' => 'string',
        'ResponseContentLanguage' => 'string',
        'ResponseContentType' => 'string',
        'ResponseExpires' => 'mixed type: string (date format)|int (unix timestamp)|\DateTime',
        'SaveAs' => $local_path));
} catch (\Exception $e) {
    echo "$e\n";
}

## getObjectUrl(获取文件UrL)
try {
    $signedUrl = $cosClient->getObjectUrl($bucket, $key, '+10 minutes');
    echo $signedUrl;
} catch (\Exception $e) {
    print_r($e);
}
```
