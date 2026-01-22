---
title: php反向代理
tags: []
categories: 默认
date: 2025-07-28 13:42:38
---

反代任意网站（必须得是相对路径链接）

```php
<?php
// 填要反代的URL，不以 / 结尾，任何网络请求都会拼接到该地址之后
$proxyBase = "https://blog.oneall.eu.org";

// 获取客户端请求信息
$method = $_SERVER['REQUEST_METHOD'];
$path = $_SERVER['REQUEST_URI'] ?? '/';
$headers = getallheaders();
$body = file_get_contents('php://input');

// 构造目标 URL
$targetUrl = "{$proxyBase}{$path}";

// 准备 cURL 请求
$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => $targetUrl,
    CURLOPT_CUSTOMREQUEST => $method,
    CURLOPT_HTTPHEADER => formatHeaders($headers),
    CURLOPT_POSTFIELDS => $body,
    CURLOPT_FOLLOWLOCATION => false,
    CURLOPT_HEADER => true,
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_SSL_VERIFYPEER => false,
    CURLOPT_ENCODING => '',
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_2TLS,
]);

// 执行请求
$response = curl_exec($ch);
$error = curl_error($ch);

if ($error) {
    http_response_code(502);
    echo "Proxy error: " . $error;
    exit;
}

// 处理响应
$statusCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
$headerSize = curl_getinfo($ch, CURLINFO_HEADER_SIZE);
$responseHeaders = substr($response, 0, $headerSize);
$responseBody = substr($response, $headerSize);

curl_close($ch);

// 转发响应头
foreach (explode("\r\n", $responseHeaders) as $headerLine) {
    if (strpos($headerLine, 'HTTP/') === 0) {
        header($headerLine);
    } else if (!empty(trim($headerLine))) {
        $parts = explode(': ', $headerLine, 2);
        if (count($parts) === 2) {
            header($headerLine, false);
        }
    }
}

// 输出响应体
echo $responseBody;

// 转换请求头
function formatHeaders(array $headers): array
{
    $result = [];
    foreach ($headers as $name => $value) {

        // 跳过可能引起问题的头
        if (strtolower($name) === 'host')
            continue;
        if (strtolower($name) === 'connection')
            continue;

        // 修复 accept-encoding
        if (strtolower($name) === 'accept-encoding') {
            $value = "none";
        }

        // 不缓存数据
        if (strtolower($name) === "cache-control") {
            $value = "no-cache";
        }

        $result[] = "{$name}: {$value}";
    }
    return $result;
}
```

添加伪静态规则

```htaccess
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /

    # 跳过真实存在的文件或目录
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d

    # 重写所有请求到 index.php
    RewriteRule . /index.php [L]
</IfModule>
```
