---
title: 用php解决跨域问题
date: 2025-03-17 12:24:56
tags: []
categories: 默认
---

```php
<?php

$targetUrl = $_SERVER['REQUEST_URI'];
$targetUrl = substr($targetUrl, strpos($targetUrl, 'netdrive.php/') + 13);
if ($targetUrl == '') {
    exit;
}

$ch = curl_init($targetUrl);
curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, false);
curl_setopt($ch, CURLOPT_HEADER, false);
curl_setopt($ch, CURLOPT_USERAGENT, 'PHP cURL');

header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS');
header('Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept, Authorization');

curl_exec($ch);
curl_close($ch);

?>
```

保存为 `netdrive.php`
只要在该php文件url后加上目标url便可，跟cf反代一样的
