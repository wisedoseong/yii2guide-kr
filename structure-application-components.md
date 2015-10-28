응용 프로그램 구성 요소
==============================

응용 프로그램은 서비스 로케이터 (concept-service-locator.md)입니다.
응용 프로그램은 요청을 처리하기위한 다양한 서비스를 제공하는 일련의 객체 소위 *응용 프로그램 구성 요소*를 호스트합니다.
예를 들어, `urlManager`가 웹 요청을 적절한 컨트롤러로 라우팅하는 역할을지지 `db` 구성 요소가 DB 관련 서비스를 제공하는 등등입니다.

모든 응용 프로그램 구성 요소는 각각 동일한 응용 프로그램에서 다른 응용 프로그램 구성 요소에서 구별 할 수 있도록 고유 한 ID를 가지고 있습니다.
응용 프로그램 구성 요소는 다음 식에 따라 사용할 수 있습니다.

```php
\Yii::$app->componentID
```

예를 들어, `\Yii::$app->db`를 사용하여 응용 프로그램에 등록 된 [[yii\db\Connection|DB connection]] 연결을 얻을 수 있습니다.
또한 `\Yii::$app->cache` 를 사용하여 [[yii\caching\Cache|primary cache]] 를 얻을 수 있습니다.

응용 프로그램 구성 요소는 위의 식을 사용하여 액세스 된 처음에 생성됩니다.
두 번째 이후의 액세스는 동일한 구성 요소 인스턴스가 반환됩니다.

어떤 객체에서도 응용 프로그램 구성 요소라고 할 수 있습니다.
응용 프로그램의 구성 정보 (structure-applications.md#application-configurations)에서 [[yii\base\Application::components] 속성을 구성하여 응용 프로그램 구성 요소를 등록 할 수 있습니다.
예를 들어,

```php
[
    'components' => [
        // register "cache" component using a class name
        'cache' => 'yii\caching\ApcCache',

        // register "db" component using a configuration array
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn' => 'mysql:host=localhost;dbname=demo',
            'username' => 'root',
            'password' => '',
        ],

        // register "search" component using an anonymous function
        'search' => function () {
            return new app\components\SolrService;
        },
    ],
]
```

> Info | 정보 : 필요한만큼 많은 응용 프로그램 구성 요소를 등록 할 수 있지만 신중해야합니다.
  응용 프로그램 구성 요소는 전역 변수와 같은 것입니다.
  너무 많은 응용 프로그램 구성 요소를 사용하면 코드의 테스트 및 유지 보수가 어려워 질 수 있습니다.
  대부분의 경우 필요할 때 로컬 구성 요소를 만들고 사용하는 것만으로 충분합니다.


## 구성 요소를 부트 스트랩에 포함 <span id="bootstrapping-components"></span>

상술 한 바와 같이 응용 프로그램 구성 요소는 최초에 액세스 할 때 처음 인스턴스가 작성됩니다.
요청 사이에 전혀 액세스되지 않은 경우, 인스턴스는 생성되지 않습니다.
그러나 경우에 따라서는 명시 적으로 액세스되지 않는 경우에도 요청에 따라 응용 프로그램 구성 요소의 인스턴스를 만들고 싶을 때가 있습니다.
그렇게하기 위해서는 응용 프로그램의 [[yii\base\Application::bootstrap|bootstrap]] 속성의 목록에 해당 구성 요소의 ID를 올릴 수 있습니다.

예를 들어 다음 응용 프로그램 구성 정보는 `log` 구성 요소가 항상로드되는 것을 보장하는 것입니다.

```php
[
    'bootstrap' => [
        'log',
    ],
    'components' => [
        'log' => [
            // configuration for "log" component
        ],
    ],
]
```


## 코어 애플리케이션 구성 요소 <span id="core-application-components"></span>

Yii는 고정 된 ID와 기본 구성 정보를 가지는 일련의 *코어* 응용 프로그램 구성 요소를 정의하고 있습니다.
예를 들어, [[yii\web\Application::request|request]]  구성 요소는 사용자 요청에 대한 정보를 수집하여 그것을 루트 (runtime-routing.md)으로 해결하는 데 사용됩니다.
또한 [[yii\base\Application::db|db]] 구성 요소는이를 통해 데이터베이스 쿼리를 수행 할 수있는 데이터베이스 연결을 표현합니다.
Yii 응용 프로그램이 사용자 요청을 처리 할 수 있는 것은 바로 이러한 핵심 응용 프로그램 구성 요소의 도움이 있어야만합니다.


아래가 미리 정의 된 핵심 응용 프로그램 구성 요소입니다.
일반 응용 프로그램 구성 요소에 대한 것과 마찬가지로 이러한 구성하고 사용자 정의 할 수 있습니다.
핵심 응용 프로그램 구성 요소를 구성하는 경우에는 클래스를 지정하지 않으면 기본 클래스가 사용됩니다.

*  [[yii\web\AssetManager|assetManager]] : 자산 번들과 asset 의 퍼블리싱을 관리합니다.
  자세한 내용은 에셋 (structure-assets.md) 절을 참조하십시오.
* [[yii\db\Connection|db]] : 데이터베이스 연결을 나타냅니다. 이를 통해 DB 쿼리를 실행할 수 있습니다.
  이 구성 요소를 구성 할 때 구성 요소의 클래스는 물론 [[yii\db\Connection::dsn]] 같은 필수 구성 요소 속성을 지정해야한다는 점에주의하십시오.
  자세한 내용은 데이터 액세스 개체 (db-dao.md) 절을 참조하십시오.
*  [[yii\base\Application::errorHandler|errorHandler]] : PHP 에러와 예외를 처리합니다.
  자세한 내용은 오류 처리 (runtime-handling-errors.md) 절을 참조하십시오.
* [[yii\i18n\Formatter|formatter]]: 최종 사용자에게 표시되는 데이터의 형식을 설정합니다.
  예를 들어, 숫자가 천 단위 구분 기호를 사용하여 표시되거나 날짜가 긴 형식으로 표시되기도합니다.
  자세한 내용은 데이터 서식 (output-formatting.md) 절을 참조하십시오.
* [[yii\i18n\I18N|i18n]] : 메시지 번역과 서식을 지원합니다.
  자세한 내용은 [세제화] (tutorial-i18n.md) 절을 참조하십시오.
* [[yii\log\Dispatcher|log]] : 로그 대상을 관리합니다.
  자세한 내용은 [로깅] (runtime-logging.md) 절을 참조하십시오.
* [[yii \ swiftmailer \ Mailer | mail] : 이메일 작성 및 전송을 지원합니다.
  자세한 내용은 메일 (tutorial-mailing.md) 절을 참조하십시오.
* [[yii\base\Application::response|response]] : 최종 사용자에게 보내는 응답을 표현합니다.
  자세한 내용은 [응답] (runtime-responses.md) 절을 참조하십시오.
* [[yii\base\Application::request|request]]: 최종 사용자로부터받은 요청을 표현합니다.
  자세한 내용은 [요청] (runtime-requests.md) 절을 참조하십시오.
* [[yii\web\Session|session]] : 세션 정보를 표현합니다.
  이 구성 요소는 [[yii\web\Application|Web applications]] 에서만 사용할 수 있습니다.
  자세한 내용은 세션과 쿠키 (runtime-sessions-cookies.md) 절을 참조하십시오.
* [[yii\web\UrlManager|urlManager]] : URL 분석 및 생성을 지원합니다.
  자세한 내용은 라우팅 및 URL 생성 (runtime-routing.md) 절을 참조하십시오.
*[[yii\web\User|user]] : 사용자의 인증 정보를 표현합니다.
  이 구성 요소는 [[yii\web\Application|Web applications]]에서만 사용할 수 있습니다.
  자세한 내용은 인증 (security-authentication.md) 절을 참조하십시오.
* [[yii\web\View|view]] : 뷰의 렌더링을 지원합니다.
  자세한 내용은 [보기] (structure-views.md) 절을 참조하십시오.