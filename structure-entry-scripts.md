항목 스크립트
==================

항목 스크립트는 응용 프로그램의 부트 스트랩 과정에서 첫 번째 단계입니다.
응용 프로그램 (웹 응용 프로그램이든 콘솔 응용 프로그램이든) 단일 항목 스크립트가 있습니다.
최종 사용자는 항목 스크립트에 요청을 발행하고 항목 스크립트는 응용 프로그램의 인스턴스를 생성하고 그것에 요청을 전달합니다.

웹 응용 프로그램의 항목 스크립트는 최종 사용자가 액세스 할 수 있도록 웹에서 액세스 할 수있는 디렉토리에 보관되어야합니다.
대부분은 `index.php` 라고 불리게됩니다 만, 웹 서버가 찾을 수있는 한 어떤 이름을 사용해도 상관 없습니다.

콘솔 응용 프로그램의 진입 스크립트는 일반적으로 응용 프로그램의 기본 경로 (structure-applications.md) 아래에 저장되고 `yii` 라고 불리게됩니다 (`.php`의 확장명을 포함).
이것은 사용자가 `./yii <route> [arguments] [options] 라는 명령으로 콘솔 응용 프로그램을 실행시킬 수 있게하는 스크립트이며 실행 권한이 주어져야합니다.

항목 스크립트는 주로 다음의 일을합니다.

* 글로벌 상수를 정의한다.
* [Composer autoloader] (https://getcomposer.org/doc/01-basic-usage.md#autoloading)를 등록한다.
* [[Yii] 클래스 파일을 포함한다.
* 응용 프로그램의 구성 정보를 읽어 낸다.
* 응용 프로그램 (structure-applications.md)의 인스턴스를 생성하고 구성한다.
* [[yii\base\Application::run()]] 을 호출받은 요청을 처리한다.


## 웹 응용 프로그램 <span id = "web-applications"></span>

다음 예는 다음 기본 웹 프로젝트 템플릿 (start-installation.md) 항목 스크립트입니다.

```php
<?php

defined('YII_DEBUG') or define('YII_DEBUG', true);
defined('YII_ENV') or define('YII_ENV', 'dev');

// register Composer autoloader
require(__DIR__ . '/../vendor/autoload.php');

// include Yii class file
require(__DIR__ . '/../vendor/yiisoft/yii2/Yii.php');

// load application configuration
$config = require(__DIR__ . '/../config/web.php');

// create, configure and run application
(new yii\web\Application($config))->run();
```


## 콘솔 응용 프로그램 <span id = "console-applications"></span>

이와 비슷한, 콘솔 응용 프로그램의 항목 스크립트입니다.

```php
#!/usr/bin/env php
<?php
/**
 * Yii console bootstrap file.
 *
 * @link http://www.yiiframework.com/
 * @copyright Copyright (c) 2008 Yii Software LLC
 * @license http://www.yiiframework.com/license/
 */

defined('YII_DEBUG') or define('YII_DEBUG', true);

// register Composer autoloader
require(__DIR__ . '/vendor/autoload.php');

// include Yii class file
require(__DIR__ . '/vendor/yiisoft/yii2/Yii.php');

// load application configuration
$config = require(__DIR__ . '/config/console.php');

$application = new yii\console\Application($config);
$exitCode = $application->run();
exit($exitCode);
```


## 상수를 정의 <span id = "defining-constants"></span>

글로벌 상수를 정의하려면 항목 스크립트가 최선의 장소입니다.
Yii는 다음의 세 가지 상수를 지원합니다.

*`YII_DEBUG` : 응용 프로그램이 디버그 모드로 실행할지 여부를 지정합니다.
  디버그 모드에서 응용 프로그램은 더 많은 로그 정보를 유지하고, 예외가 발생하는 경우 자세한 오류 호출 스택을 표시합니다.
  이러한 이유로 디버그 모드는 주로 개발시 사용될 것입니다.
  `YII_DEBUG`의 기본값은 false입니다.
*`YII_ENV` : 어떤 환경에서 응용 프로그램이 실행되고 있는지를 지정합니다.
  자세한 내용은 구성 정보 (concept-configurations.md # environment-constants) 절에서 설명됩니다.
  `YII_ENV` 의 기본값은` 'prod'`이며, 응용 프로그램이 프로덕션 환경에 달려 있다는 것을 의미합니다.
*`YII_ENABLE_ERROR_HANDLER` : Yii에서 제공하는 에러 핸들러를 사용할지 여부를 지정합니다.
  이 상수의 기본값은 true입니다.

상수를 정의 할 때 종종 다음과 같은 코드를 사용합니다.

```php
defined('YII_DEBUG') or define('YII_DEBUG', true);
```

이것은 아래의 코드와 같은 의미입니다.

```php
if (!defined('YII_DEBUG')) {
    define('YII_DEBUG', true);
}
```

분명히 전자가 간결하고 이해하기 쉬울 것입니다.

다른 PHP 파일이 포함 된 때 상수의 효력이 생기도록하기 위해 상수는 항목 스크립트 시작 부분에서 정의되어야합니다.