---
title: 使用phpunit进行单元测试
tags:
  - php
originContent: >-
  PHPUnit 是一个用PHP编程语言开发的开源软件，是一个单元测试框架。PHPUnit由Sebastian Bergmann创建，源于Kent
  Beck的SUnit，是xUnit家族的框架之一。


  单元测试是对单独的代码对象进行测试的过程，比如对函数、类、方法进行测试。单元测试可以使用任意一段已经写好的测试代码，也可以使用一
  些已经存在的测试框架，比如JUnit、PHPUnit或者Cantata++，单元测试框架提供了一系列共同、有用的功能来帮助人们编写自动化的检测单元，
  例如检查一个实际的值是否符合我们期望的值的断言。单元测试框架经常会包含每个测试的报告，以及给出你已经覆盖到的代码覆盖率。


  总之一句话，使用 phpunit 进行自动测试，会使你的代码更健壮，减少后期维护的成本，也是一种比较标准的规范，现如今流行的PHP框架都带
  了单元测试，如Laraval,Symfony,Yii2等，单元测试已经成了标配。


  另外，单元测试用例是通过命令操控测试脚本的，而不是通过浏览器访问URL的。 这里以phpstorm为例，说明如何利用phpunit做单元测试。


  官网地址：https://phpunit.de/

  官方文档：https://phpunit.readthedocs.io/zh_CN/latest/

  ### 1、安装phpunit

  可以全局安装，也可以在项目中通过composer安装


  全局安装

  ```bash

  $:cd /usr/local/bin

  $:wget -O phpunit https://phar.phpunit.de/phpunit-7.phar

  $:chmod a+x phpunit

  PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

  ```


  composer依赖

  ```bash

  $:composer require --dev phpunit/phpunit ^7

  $:./vendor/bin/phpunit --version

  PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

  ```


  ### 2、配置phpunit

  在phpstorm中，在reference中选择 Languages & Frameworks > PHP > Test Frameworks。

  ![Test Frameworks](../images/image2018-12-4_13-23-29.png)


  在右侧点击“+”号，选择添加“PHPUnit Local”。

  ![PHPUnit Local](../images/image2018-12-4_13-24-12-2.png)


  选择“Path to phpunit.phar"，路径为“/usr/local/bin/phpunit”，“Default configuration
  file:”为项目中的phpunit.xml文件即可。

  ![Path to phpunit](../images/image2018-12-4_13-30-47-3.png)


  经过上面的步骤，在phpstorm里边就可以对项目中的测试用例进行测试了。


  ### 3、配置文件

  lavarel框架中，已经默认置入对phpunit的支持了，在项目目录中的phpunit.xml就是phpunit的配置文件，它指明了phpunit的启动文件、对哪些文件进行测试，是否输出测试覆盖率等行为。


  一般的lavarel的phpunit.xml的内容如下：

  ```xml

  <?xml version="1.0" encoding="UTF-8"?>

  <phpunit backupGlobals="false"
           backupStaticAttributes="false"
           bootstrap="bootstrap/autoload.php"
           colors="true"
           convertErrorsToExceptions="true"
           convertNoticesToExceptions="true"
           convertWarningsToExceptions="true"
           processIsolation="false"
           stopOnFailure="false">
      <testsuites>
          <testsuite name="Feature">
              <directory suffix="Test.php">./tests/Feature</directory>
          </testsuite>

          <testsuite name="Unit">
              <directory suffix="Test.php">./tests/Unit</directory>
          </testsuite>
      </testsuites>
      <filter>
          <whitelist processUncoveredFilesFromWhitelist="true">
              <directory suffix=".php">./app</directory>
          </whitelist>
      </filter>
      <logging>
          <!--这段代码可用来生成测试覆盖率报告-->
          <log type="coverage-html" target="./reports/" charset="UTF-8"/>
      </logging>
      <php>
          <env name="APP_ENV" value="testing"/>
          <env name="CACHE_DRIVER" value="array"/>
          <env name="SESSION_DRIVER" value="array"/>
          <env name="QUEUE_DRIVER" value="sync"/>
      </php>
  </phpunit>


  ```

  具体的用法可以通过这个文档进一步了解：https://phpunit.readthedocs.io/zh_CN/latest/configuration.html



  ### 4、创建测试用例

  在laravel中创建测试用例，可以使用下面的命令：

  ```bash

  // 在 Feature 目录下创建测试类...

  php artisan make:test UserTest


  // 在 Unit 目录下创建测试类...

  php artisan make:test UserTest --unit


  ```


  下面就是一个简单地对Model进行测试的单测

  ```php

  <?php

  namespace Tests\Feature;


  use App\Models\Schedule;

  use Tests\TestCase;

  use Illuminate\Foundation\Testing\WithFaker;

  use Illuminate\Foundation\Testing\RefreshDatabase;


  class ConflictTest extends TestCase

  {
      /**
       * A basic test example.
       *
       * @return void
       */
      public function testExample()
      {
          $teachers = Schedule::where('teacher_id', 2574)->get()->toArray();
          // 断言返回的数据不为空
          $this->assertNotEmpty($teachers);
      }
  }

  ```


  ![](../images/image2018-12-4_13-45-12-4.png)

  点击phpstorm左侧的绿色圆圈，就可以对testExample方法进行测试了。

  ![](../images/image2018-12-4_13-46-6-5.png)


  ### 5、代码覆盖率

  如果在phpunit.xml中使用了代码覆盖率功能，则会在对应的目录生成测试报告。

  ![](../images/image2018-12-4_14-7-13-6.png)


  根据报表可以查看某个方法的测试覆盖率。

  ![](../images/image2018-12-4_14-10-10-7.png)


  进入到文件，可以查看更详细地覆盖情况。

  ![](../images/image2018-12-4_14-8-16-8.png)

  绿色的代码行表示在测试过程中有进入这段代码，红色表示未进入过。一方面可以查看哪些逻辑为考虑到位，另一方面也可以检查是否按照既定的 业务逻辑在运行代码。
categories:
  - php
toc: true
date: 2018-12-05 15:56:14
---

PHPUnit 是一个用PHP编程语言开发的开源软件，是一个单元测试框架。PHPUnit由Sebastian Bergmann创建，源于Kent Beck的SUnit，是xUnit家族的框架之一。

单元测试是对单独的代码对象进行测试的过程，比如对函数、类、方法进行测试。单元测试可以使用任意一段已经写好的测试代码，也可以使用一 些已经存在的测试框架，比如JUnit、PHPUnit或者Cantata++，单元测试框架提供了一系列共同、有用的功能来帮助人们编写自动化的检测单元， 例如检查一个实际的值是否符合我们期望的值的断言。单元测试框架经常会包含每个测试的报告，以及给出你已经覆盖到的代码覆盖率。

总之一句话，使用 phpunit 进行自动测试，会使你的代码更健壮，减少后期维护的成本，也是一种比较标准的规范，现如今流行的PHP框架都带 了单元测试，如Laraval,Symfony,Yii2等，单元测试已经成了标配。

另外，单元测试用例是通过命令操控测试脚本的，而不是通过浏览器访问URL的。 这里以phpstorm为例，说明如何利用phpunit做单元测试。

官网地址：https://phpunit.de/
官方文档：https://phpunit.readthedocs.io/zh_CN/latest/
### 1、安装phpunit
可以全局安装，也可以在项目中通过composer安装

全局安装
```bash
$:cd /usr/local/bin
$:wget -O phpunit https://phar.phpunit.de/phpunit-7.phar
$:chmod a+x phpunit
PHPUnit 7.0.0 by Sebastian Bergmann and contributors.
```

composer依赖
```bash
$:composer require --dev phpunit/phpunit ^7
$:./vendor/bin/phpunit --version
PHPUnit 7.0.0 by Sebastian Bergmann and contributors.
```

### 2、配置phpunit
在phpstorm中，在reference中选择 Languages & Frameworks > PHP > Test Frameworks。
![Test Frameworks](/images/image2018-12-4_13-23-29.png)

在右侧点击“+”号，选择添加“PHPUnit Local”。
![PHPUnit Local](/images/image2018-12-4_13-24-12-2.png)

选择“Path to phpunit.phar"，路径为“/usr/local/bin/phpunit”，“Default configuration file:”为项目中的phpunit.xml文件即可。
![Path to phpunit](/images/image2018-12-4_13-30-47-3.png)

经过上面的步骤，在phpstorm里边就可以对项目中的测试用例进行测试了。

### 3、配置文件
lavarel框架中，已经默认置入对phpunit的支持了，在项目目录中的phpunit.xml就是phpunit的配置文件，它指明了phpunit的启动文件、对哪些文件进行测试，是否输出测试覆盖率等行为。

一般的lavarel的phpunit.xml的内容如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit backupGlobals="false"
         backupStaticAttributes="false"
         bootstrap="bootstrap/autoload.php"
         colors="true"
         convertErrorsToExceptions="true"
         convertNoticesToExceptions="true"
         convertWarningsToExceptions="true"
         processIsolation="false"
         stopOnFailure="false">
    <testsuites>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>

        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
    </testsuites>
    <filter>
        <whitelist processUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">./app</directory>
        </whitelist>
    </filter>
    <logging>
        <!--这段代码可用来生成测试覆盖率报告-->
        <log type="coverage-html" target="./reports/" charset="UTF-8"/>
    </logging>
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="SESSION_DRIVER" value="array"/>
        <env name="QUEUE_DRIVER" value="sync"/>
    </php>
</phpunit>

```
具体的用法可以通过这个文档进一步了解：https://phpunit.readthedocs.io/zh_CN/latest/configuration.html


### 4、创建测试用例
在laravel中创建测试用例，可以使用下面的命令：
```bash
// 在 Feature 目录下创建测试类...
php artisan make:test UserTest

// 在 Unit 目录下创建测试类...
php artisan make:test UserTest --unit

```

下面就是一个简单地对Model进行测试的单测
```php
<?php
namespace Tests\Feature;

use App\Models\Schedule;
use Tests\TestCase;
use Illuminate\Foundation\Testing\WithFaker;
use Illuminate\Foundation\Testing\RefreshDatabase;

class ConflictTest extends TestCase
{
    /**
     * A basic test example.
     *
     * @return void
     */
    public function testExample()
    {
        $teachers = Schedule::where('teacher_id', 2574)->get()->toArray();
        // 断言返回的数据不为空
        $this->assertNotEmpty($teachers);
    }
}
```

![](/images/image2018-12-4_13-45-12-4.png)
点击phpstorm左侧的绿色圆圈，就可以对testExample方法进行测试了。
![](/images/image2018-12-4_13-46-6-5.png)

### 5、代码覆盖率
如果在phpunit.xml中使用了代码覆盖率功能，则会在对应的目录生成测试报告。
![](/images/image2018-12-4_14-7-13-6.png)

根据报表可以查看某个方法的测试覆盖率。
![](/images/image2018-12-4_14-10-10-7.png)

进入到文件，可以查看更详细地覆盖情况。
![](/images/image2018-12-4_14-8-16-8.png)
绿色的代码行表示在测试过程中有进入这段代码，红色表示未进入过。一方面可以查看哪些逻辑为考虑到位，另一方面也可以检查是否按照既定的 业务逻辑在运行代码。