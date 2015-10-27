콘솔 응용 프로그램
==========================

웹 응용 프로그램을 구축하기위한 풍부한 기능 이외에, Yii는 콘솔 응용 프로그램에 대한 전체 장비의 지원을 가지고 있습니다.
콘솔 응용 프로그램은 주로 웹 사이트를 위해 수행해야하는 백그라운드 작업이나 유지 보수 작업을 만드는 데 사용되는 것입니다.

콘솔 응용 프로그램의 구조는 Yii 웹 응용 프로그램의 그것과 매우 비슷합니다.
콘솔 응용 프로그램은 하나 이상의 [yii \ console \ Controller] 클래스로 구성됩니다.
컨트롤러는 콘솔 환경에서는 종종 "명령"이라고합니다.
또한 각 컨트롤러는 웹 컨트롤러와 동일하게 하나 이상의 액션을 가질 수 있습니다.

프로젝트 템플릿은 모두 이미 콘솔 응용 프로그램을 가지고 있습니다.
저장소의 기본 디렉토리에있는`yii` 스크립트를 호출하여 콘솔 응용 프로그램을 실행할 수 있습니다.
이 스크립트는 아무것도 매개 변수를 추가하지 않고 실행하면 사용 가능한 명령의 목록을 표시합니다.

! [./ yii 명령을 실행하고 도움말을 보려면 (images / tutorial-console-help.png)

스크린 샷에 표시된 바와 같이 기본적으로 사용할 수있는 일련의 명령이 Yii 의해 이미 정의되어 있습니다.

- [yii \ console \ controllers \ AssetController | AssetController] - JavaScript 및 CSS 파일을 결합하고 압축 할 수 있습니다.
  이 명령은 자산의 절 (structure-assets.md # using-the-asset-command)에서 더 학습 할 수 있습니다.
- [yii \ console \ controllers \ CacheController | CacheController] - 응용 프로그램 캐시를 플러시 할 수 있습니다.
- [yii \ console \ controllers \ FixtureController | FixtureController] - 테스트를 위해 고정물 데이터 로딩을 관리합니다.
  이 명령은 테스트 고정물 절 (test-fixtures.md # managing-fixtures)에 자세히 설명되어 있습니다.
- [yii \ console \ controllers \ HelpController | HelpController] - 콘솔 명령에 대한 도움말 정보를 제공합니다.
  이것이 기본 명령이며, 위의 스크린 샷에서 본 출력을 표시하는 것입니다.
- [yii \ console \ controllers \ MessageController | MessageController] - 소스 파일에서 번역 할 메시지를 추출합니다.
  이 명령에 대한 학습하기 위해서는, 국제화 절 (tutorial-i18n.md # message-command)를 참조하십시오.
- [yii \ console \ controllers \ MigrateController | MigrateController] - 애플리케이션 마이그레이션을 관리합니다.
  데이터베이스 마이그레이션은 데이터베이스 마이그레이션 절 (db-migrations.md)에 자세히 설명되어 있습니다.


사용 방법 <span id = "usage"> </ span>
--------

콘솔의 컨트롤러 액션은 다음 구문을 사용하여 실행합니다.

```
yii <route> --option1 = value1 --option2 = value2 ... argument1 argument2 ...]
```

상기에서`<route>`는 컨트롤러 액션의 경로를 보여줍니다.
옵션 (options)는 클래스의 속성에 할당 된 인수 (arguments)는 액션 메소드의 매개 변수입니다.

예를 들어, [yii \ console \ controllers \ MigrateController : $ migrationTable | MigrateController : $ migrationTable]으로`migrations`를 지정하고 이주의 상한을 5로 지정하고 [yii \ console \ controllers \ MigrateController : : actionUp () | MigrateController :: actionUp ()를 호출하려면 다음과 같이합니다.

```
yii migrate / up 5 --migrationTable = migrations
```
> **주의 ** : 콘솔에서`*`을 사용하는 경우에는` "*"`로 인용 부호로 둘러싸는 것을 잊지 마세요.
> 이것은`*`을 현재 디렉토리의 모든 파일 이름으로 대체 셸 로브로 실행되는 것을 방지합니다.


항목 스크립트 <span id = "entry-script"> </ span>
------------------

콘솔 응용 프로그램의 진입 스크립트는 웹 응용 프로그램에서 사용되는 부트 스트랩 파일`index.php`에 해당하는 것입니다.
콘솔 항목 스크립트는 일반적으로`yii`라는 것으로, 어플리케이션의 루트 디렉토리에 배치되어 있습니다.
그것은 다음과 같은 코드를 포함하고 있습니다.

```php
#! / usr / bin / env php
<? php
/ **
 * Yii console bootstrap file.
 * /

defined ( 'YII_DEBUG') or define ( 'YII_DEBUG', true);

require (__ DIR__ '/vendor/autoload.php');
require (__ DIR__ '/vendor/yiisoft/yii2/Yii.php');

$ config = require (__ DIR__ '/config/console.php');

$ application = new yii \ console \ Application ($ config);
$ exitCode = $ application-> run ();
exit ($ exitCode);
```

이 스크립트는 응용 프로그램의 일부로 생성되는 것입니다.
당신의 필요를 충족하도록 자유롭게 편집하고 상관 없습니다.
에러 발생시에 스택 트레이스를보고 싶지 않거나 전반적인 성능을 올리고 싶은 경우라면,`YII_DEBUG` 상수를`false`로 설정 할 수 있습니다.
기본 프로젝트 템플릿에서 고급 프로젝트 템플릿에서 콘솔 응용 프로그램의 진입 스크립트는 개발자 친화적 인 환경을 제공하기 위해 기본적으로 디버깅을 사용하고 있습니다.


구성 정보 <span id = "configuration"> </ span>
--------

위의 코드에서 보듯이 콘솔 응용 프로그램은`console.php`라는 자체 구성 정보 파일을 사용합니다.
이 파일에서 다양한 응용 프로그램 구성 요소 (structure-application-components.md), 특히 콘솔 응용 프로그램에 대한 속성을 구성해야합니다.

웹 응용 프로그램 및 콘솔 응용 프로그램을 구성 매개 변수와 값을 많이 공유하면 공통 부분을 별도의 파일로 이동하여 해당 파일을 모두 응용 프로그램 (웹과 콘솔)의 구성 정보에 포함하는 것을 검토해도 좋을 것입니다.
예를 "고급"프로젝트 템플릿에서 볼 수 있습니다.

> Tip | 팁 : 경우에 따라 항목 스크립트에 지정된 것과 다른 응용 프로그램 구성 정보를 사용하여 콘솔 명령을 수행 할 수 있습니다.
> 예를 들어,`yii migrate` 명령을 사용하여 테스트 데이터베이스를 업그레이드 할 때 데이터베이스가 개별 테스트에서 구성되는 경우입니다.
> 구성 정보를 동적으로 변경하는 명령을 실행할 때`appconfig` 옵션을 사용하여 사용자의 구성 정보 파일을 지정하는 것만으로 괜찮습니다.
>
>```
> yii <route> --appconfig = path / to / config.php ...
>```


자신의 콘솔 명령을 만드는 <span id = "create-command"> </ span>
----------------------------------------

### 콘솔의 컨트롤러와 액션

콘솔 명령은 [yii \ console \ Controller]를 확장하는 컨트롤러 클래스로 정의 할 수 있습니다.
컨트롤러 클래스에서 컨트롤러의 하위 명령에 대응하는 하나 이상의 작업을 정의합니다.
각 작업에서 특정 하위 명령에 대한 적절한 작업을 구현하는 코드를 작성합니다.

명령을 실행할 때 컨트롤러의 액션에 대한 경로를 지정해야합니다.
예를 들어, 루트`migrate / create`은 [yii \ console \ controllers \ MigrateController :: actionCreate () | MigrateController :: actionCreate () 액션 메소드에 대응하는 하위 명령을 호출합니다.
실행시 제공된 경로에 액션 ID가 포함되지 않는 경우 (웹 컨트롤러의 경우처럼) 기본 조치가 실행됩니다.

### 옵션

[yii \ console \ Controller :: options ()] 메소드를 재정 의하여 콘솔 명령 (controller / actionID)에서 사용할 수있는 옵션을 지정할 수 있습니다.
이 메소드는 컨트롤러 클래스의 공용 속성 목록을 반환해야합니다.
명령을 실행할 때`--OptionName = OptionValue` 구문을 사용하여 옵션 값을 지정할 수 있습니다. 이것은 컨트롤러 클래스의`OptionName` 속성에`OptionValue`을 할당합니다.

옵션의 기본값이 배열 형인 경우 런타임에이 옵션을 설정하면 옵션의 값은 입력 문자열을 쉼표로 분리하여 배열로 변환됩니다.

### 인수

옵션 이외에으로 명령은 인수를 취할 수도 있습니다.
인수는 요청 된 하위 명령에 대응하는 액션 메소드에 매개 변수로 전달됩니다.
첫 번째 인수는 첫 번째 매개 변수에 대응하고, 두 번째 인수는 두 번째 매개 변수에 대응하고, 이하와 같습니다.
명령을 호출 할 때 충분한 수의 인수가 제공되지 않은 경우 해당 매개 변수는 정의되어 있으면, 선언 된 디폴트 값을 취합니다.
기본값이 설정되어 있지 않고 런타임에 값이 제공되지 않은 경우, 명령은 오류로 종료합니다.

`array` 타입 힌트를 사용하여 인수가 배열로 취급되어야 것을 나타낼 수 있습니다.
배열은 입력 문자열을 쉼표로 구분하여 생성됩니다.

다음 인수를 선언하는 방법을 보여주는 예입니다.

```php
class ExampleController extends \ yii \ console \ Controller
{
    // 명령 "yii example / create test"는 "actionCreate ( 'test')"를 호출
    public function actionCreate ($ name) {...}

    // 명령 "yii example / index city"은 "actionIndex ( 'city', 'name')"를 호출
    // 명령 "yii example / index city id"는 call "actionIndex ( 'city', 'id')"를 호출
    public function actionIndex ($ category, $ order = 'name') {...}

    // 명령 "yii example / add test"는 "actionAdd ( 'test')"를 호출
    // 명령 "yii example / add test1, test2"는 "actionAdd ( 'test1', 'test2'])"를 호출
    public function actionAdd (array $ name) {...}
}
```


### 종료 코드

종료 코드를 사용하는 것은 콘솔 응용 프로그램 개발의 모범 사례입니다.
명령은 아무런 문제가 없었던 것을 나타 내기 위해`0`을 반환 것이 관례입니다.
명령이 1 이상의 값을 반환 할 때 뭔가 오류를 시사하는 것으로 간주됩니다.
반환되는 값이 오류 코드이며,이를 통해 오류에 대한 자세한 내용을 찾아 볼 수있는 경우도 있습니다.
예를 들어,`1`은 일반적인 알 수없는 오류를 나타내는 것으로`2` ??이상의 모든 코드는 특정 오류 예를 들어, 입력 오류, 파일이 없거나, 등등을 나타내는 것이라고 할 수 있습니다 .

콘솔 명령에 종료 코드를 반환하기 위해서는 단순히 컨트롤러의 액션 메서드로 정수를 반환합니다.

```php
public function actionIndex ()
{
    if (/ * 문제가 발생 * /) {
        echo "문제가 발생했습니다! \ n";
        return 1;
    }
    // 뭔가를
    return 0;
}
```

일부 사용할 수있는 미리 정의 된 상수가 있습니다.

-`Controller :: EXIT_CODE_NORMAL` - 값은`0`
-`Controller :: EXIT_CODE_ERROR` - 값은`1`

더 많은 오류 코드 유형이있는 경우는 컨트롤러에서 의미있는 상수를 정의하는 것이 좋은 방법입니다.

### 서식 및 색상

Yii의 콘솔 명령 출력 서식을 지원하고 있습니다.
이 명령을 실행하고있는 단말기가 지원하지 않으면 자동으로 서식이없는 출력 그레이드 다운됩니다.

서식이 지정된 문자열을 출력하는 것은 간단합니다.
볼드 텍스트를 출력하려면 다음과 같이합니다.

```php
$ this-> stdout ( "Hello? \ n", Console :: BOLD);
```

여러 스타일을 동적으로 결합하여 문자열을 구성 할 필요가있는 경우는`ansiFormat`를 사용하는 편이 좋습니다.

```php
$ name = $ this-> ansiFormat ( 'Alex', Console :: FG_YELLOW);
echo "Hello, my name is $ name.";