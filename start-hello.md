Hello 출력하기
==============

이 절에서는 응용 프로그램에 "Hello"라는 새로운 페이지를 만드는 방법을 설명합니다.
이 목표를 달성하기 위하여 [액션](structure-controllers.md#creating-actions)과 [뷰](structure-views.md)를 만듭니다.

* 응용 프로그램은이 페이지에 대한 요청을 해당 작업에 송부합니다.
* 다음 조치가 "Hello"라는 말을 최종 사용자에게 보여주는 뷰를 표시합니다.

이 튜토리얼을 통해 세 가지를 배웁니다.

1. 요청에 만족하는 [액션](structure-controllers.md)을 만드는 방법
2. 응답 컨텐츠를 작성하는 [뷰](structure-views.md)을 만드는 방법
3. 응용 프로그램이 요청을 [액션](structure-controllers.md#creating-actions)에 송부하는 방법


액션 만들기 <span id = "creating-action"></span>
--------------------

"Hello"출력 작업을 위해 요청에서`message` 매개 변수를 읽고, 그 메시지를 사용자에게 표시하고 반환 하는 `say` [액션](structure-controllers.md#creating-actions)을 만듭니다.
요청이`message` 매개 변수를 제공하지 않은 경우 기본값으로 "Hello"라는 메시지를 표시하는 것으로합니다.

> Info: [액션](structure-controllers.md # creating-actions)은 최종 사용자가 직접 수행 할 수있는 객체입니다.
  액션은 [컨트롤러](structure-controllers.md)으로 그룹화됩니다.
  액션의 실행 결과가 최종 사용자가 받는 응답입니다.

액션은 [컨트롤러](structure-controllers.md)에서 선언되어야합니다.
이야기를 쉽게하기 위해`say` 액션을 기존`SiteController`에서 선언합시다.
이 컨트롤러는`controllers/SiteController.php`라는 클래스 파일에 정의되어 있습니다.
다음과 같이하여 새 작업을 시작합니다.

```php
<? php

namespace app\controllers;

use yii\web\Controller;

class SiteController extends Controller
{
    // ...existing code...

    public function actionSay($message = 'Hello')
    {
        return $this->render('say', ['message' => $message]);
    }
}
```

위의 코드는`SiteController` 클래스에서`say` 액션이`actionSay`라는 메소드로 정의되어 있습니다.
Yii는 컨트롤러 클래스에서 액션 메소드와 액션이 아니기 메소드를 구별하기 위해`action`라는 접두사를 사용합니다.
`action` 접두사 다음에 오는 이름이 액션 ID에 매핑됩니다.

액션을 명명하는 내용은 Yii가 작업 ID를 어떻게 관리 되는지를 알고 있어야합니다.
액션 ID는 항상 소문자로 참조됩니다.
액션 ID가 여러 단어를 필요로 할 때 단어가 대시 (-)로 연결됩니다 (예를 들어,`create-comment`).
액션 메소드의 이름은 액션 ID에서 대시를 모두 제거하고 각 단어의 첫 글자를 대문자로 한 결과`action`라는 접두어를 붙인 것입니다.
예를 들어, 액션 ID`create-comment`에 대응하는 액션 메소드 이름은`actionCreateComment`입니다.

우리의 예제에서는 액션 메소드는`$ message`라는 매개 변수를 디폴트 값은` "Hello"`입니다
(PHP 함수 나 메소드의 인수에 기본값을 설정하는 것과 동일한 방법입니다.)
응용 프로그램이 요청을 받아 해당 요청을 처리`say` 작업을 담당해야한다고 결정한 경우, 요청에 발견 된 같은 이름의 매개 변수의 값이`$ message` 매개 변수에 대입 합니다.
즉, 만약 요청에` "안녕"`값의`message` 매개 변수가 들어 있으면, 액션의`$ message` 변수에 값이 할당됩니다.

액션 메소드 내에서는 [[yii\web\Controller::render()|render()]]라 불리는 `say`라는 이름의 [뷰](structure-views.md) 파일이 렌더링됩니다 .
`message` 매개 변수도 동시에 뷰에 전달되어 거기서 사용됩니다.
렌더링 결과는 액션 메소드에 의해 반환됩니다.
반환 된 결과는 응용 프로그램에서 수신 브라우저에서 최종 사용자에게 (완전한 HTML 페이지의 일부로) 표시됩니다.


뷰 만들기 <span id = "creating-view"></span>
----------------

[뷰](structure-views.md)는 응답의 컨텐츠를 생성하기 위해 작성된 스크립트입니다.
"Hello"작업을 위해서는 작업 메서드로부터받은`message` 매개 변수를 출력하는`say` 뷰를 작성합니다.

```php
<? php
use yii\helpers\Html;
?>
<?= Html::encode($message) ?>
```

`say` 뷰는`views/site/say.php`라는 파일에 저장해야합니다.
액션에서 [[yii\web\Controller::render()|render()]] 메소드가 호출 될 때`render ()`메소드는`views/ControllerID/ViewName.php`라는 PHP 파일을 찾습니다 합니다.

위의 코드에서`message` 매개 변수가 출력되기 전에 [[yii\helpers\Html::encode()|HTML-encoded]] 에 인코딩됩니다.
매개 변수는 최종 사용자로부터 오는 것이며, 악성 JavaScript 코드를 포함하여 크로스 사이트 스크립팅 (XSS) 공격 (http://en.wikipedia.org/wiki/Cross-site_scripting)에 사용될 수있다 것이므로 취약점을 방지하기 위해 이렇게하는 것이 필요합니다.

물론`say`보기에는 더 많은 콘텐츠를 넣어도 상관 없습니다.
콘텐츠는 HTML 태그, 일반 텍스트, 심지어 PHP 문장을 포함 할 수 있습니다.
실제로`say`보기 [[yii\web\Controller::render()|render()]] 메소드에 의해 실행되는 PHP 스크립트에 지나지 않습니다.
뷰 스크립트에 의해 출력 된 콘텐츠는 응답 결과로 응용 프로그램에 반환됩니다.
그리고 응용 프로그램이 결과를 최종 사용자에게 출력합니다.


확인 <span id = "trying-it-out"></span>
----------

액션과 뷰를 만든 후에는 다음 URL에서 새로운 페이지에 액세스 할 수 있습니다.

```
http://hostname/index.php?r=site/say&message=Hello+World
```

! [Hello World](images / start-hello-world.png)

이 URL은 결과적으로 "Hello World"를 표시하는 페이지입니다.
이 페이지는 응용 프로그램의 다른 페이지와 동일한 상단과 하단 글을 공유 할 수 있습니다.

URL에서`message` 매개 변수를 생략하면 "Hello"를 표시하는 페이지를 보게 될 것입니다.
그것은`message`가`actionSay ()`메소드에 파라미터로서 건네받는 것이며, 그것이 생략 된 경우 기본값 인` "Hello"`이 대신 사용되기 때문입니다.

> Info: 새로운 페이지는 다른 페이지와 동일한 상단과 하단글 을 공유 할 수 있지만 그것은 [[yii\web\Controller::render()|render()]] 메소드 `say`가 결과를 보기위해 소위 [레이아웃](structure-views.md#layouts)에 자동으로 포함하기 때문입니다.
레이아웃일 경우`views/layout/main.php`에 있습니다.

위의 URL의`r` 파라미터에 대해서는 더 설명이 필요합니다.
이것은 루트 (runtime-routing.md), 즉 액션을 가리키는 응용 프로그램을 통해 고유 한 ID를 나타냅니다.
루트 형식은`ControllerID/ActionID`입니다.
응용 프로그램은 요청을 수신하면이 매개 변수`r`을 확인하고`ControllerID` 부분을 사용하여이 요청을 처리하기 위해 어떤 컨트롤러 클래스의 인스턴스를 작성해야하는지 결정합니다.
그리고 컨트롤러는`ActionID` 부분을 사용하여 실제 일을하기 위해 어떤 조치를 호출 할 것인가를 결정합니다.
이 예로 말하면`site/say`라는 루트는`SiteController` 컨트롤러 클래스와`say` 작업으로 해결됩니다.
결과적으로`SiteController::actionSay()`메소드가 요청을 처리하기 위해 호출됩니다.


> Info: 액션과 마찬가지로 컨트롤러도 또한 응용 프로그램에서 고유하게 정의 된 ID를 가지고 있습니다.
  컨트롤러 ID는 액션 ID와 같은 명명 규칙을 사용합니다.
  컨트롤러 클래스 이름은 컨트롤러 ID에서 대시를 제거하고 각 단어의 첫 글자를 대문자로 해, 결과적으로 가능한 문자열에`Controller`라는 접미사를 추가 한 것입니다.
  예를 들어,`post-comment`는 컨트롤러 ID에 해당하는 컨트롤러 클래스 이름은`PostCommentController`입니다.


정리 <span id = "summary"></span>
------

이 절에서는 MVC 디자인 패턴 중 컨트롤러와 뷰 부분에 대해서 확인 했습니다.
특정 요청을 처리하기위한 조치를 컨트롤러의 일부로 만들었습니다.
또한 응답의 콘텐츠를 만들 수있는 뷰를 작성했습니다.
이 간단한 예제에서는 사용되는 유일한 데이터가`message` 매개 변수 이었기 때문에 모델은 관계하지 않습니다.

또한 Yii의 루트에 대해서도 배웠습니다. 루트는 사용자의 요청과 컨트롤러의 액션 사이의 다리 역활을 합니다.

다음 절에서는 모델을 만드는 방법을 배웁니다. 그리고 HTML 양식이있는 페이지를 추가합니다.