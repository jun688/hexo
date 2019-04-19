---
title: 'SQLSTATE[HY000] [2054] The server requested '
tags:
  - php
originContent: ''
categories:
  - php
toc: true
date: 2018-12-24 11:10:36
---

> laravel 报错， SQLSTATE[HY000] [2054] The server requested authentication method

#### 使用PHP连接MySQL 8的时候，可能会发生如标题所示的错误：
```bash
SQLSTATE[HY000] [2054] The server requested authentication method unknown to the client
```

 * 发生这种错误，是由于MySQL8默认使用了新的密码验证插件：caching_sha2_password，而之前的PHP版本中所带的mysqlnd无法支持这种验证。
 
 * 解决办法：
 * 修改密码认证方式
ALTER USER 'YOURUSERNAME'@'localhost' IDENTIFIED WITH mysql_native_password BY 'YOURPASSWORD';

#### 设置mysql简单密码报错：
  ##### 解决方案：
  * 查看mysql全局参数配置
  
   ```bash
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password_dictionary_file    |        |
| validate_password_length             | 8      |
| validate_password_mixed_case_count   | 1      |
| validate_password_number_count       | 1      |
| validate_password_policy             | MEDIUM |
| validate_password_special_char_count | 1      |
-------------------------------------------------
```
  * 参数解释
   1) validate_password_dictionary_file ：插件用于验证密码强度的字典文件路径。

   2) validate_password_length ：密码最小长度，参数默认为8，它有最小值的限制，最小值为：validate_password_number_count + validate_password_special_char_count + (2 * validate_password_mixed_case_count)

   3) validate_password_mixed_case_count：密码至少要包含的小写字母个数和大写字母个数。

   4) validate_password_number_count：密码至少要包含的数字个数。

   5) validate_password_policy：密码强度检查等级，0/LOW、1/MEDIUM、2/STRONG

   6) validate_password_special_char_count：密码至少要包含的特殊字符数。

  * 修改mysql参数配置
  
  ```bash
mysql> set global validate_password_policy=0;
Query OK, 0 rows affected (0.05 sec)
 
mysql> 
mysql> 
mysql> set global validate_password_mixed_case_count=0;
Query OK, 0 rows affected (0.00 sec)
 
mysql> set global validate_password_number_count=3;
Query OK, 0 rows affected (0.00 sec)
 
mysql> set global validate_password_special_char_count=0;
Query OK, 0 rows affected (0.00 sec)
 
mysql> set global validate_password_length=3;
Query OK, 0 rows affected (0.00 sec)
 
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password_dictionary_file    |       |
| validate_password_length             | 3     |
| validate_password_mixed_case_count   | 0     |
| validate_password_number_count       | 3     |
| validate_password_policy             | LOW   |
| validate_password_special_char_count | 0     |
+--------------------------------------+-------+
  ```