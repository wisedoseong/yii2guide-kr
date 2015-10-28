뷰
======

뷰는 [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 아키텍처의 일부를 이루는 것입니다.
뷰는 최종 사용자에게 데이터를 표시 할 책임이있는 코드입니다.
웹 응용 프로그램에서는 뷰는 일반적으로 주로 HTML 코드로 표시 목적의 PHP 코드를 포함 PHP 스크립트 파일 인 *뷰 템플릿* 형식으로 작성됩니다.
그리고 뷰 템플릿을 관리하는 [[yii\web\View|view]] [응용 프로그램 구성 요소]structure-application-components.md)가 뷰의 구축과 렌더링을 하기 위해 자주 사용되는 메소드를 제공합니다.
또한, 간결함을 중시하여 뷰 템플릿 또는 뷰 템플릿 파일을 단순히 뷰라고 부르는 경우가 많습니다.


## 뷰 생성 <span id="creating-views"></span>

앞서 언급한 한 바와 같이 뷰는 HTML과 PHP 코드가 섞인 단순한 PHP 스크립트입니다.
다음은 로그인 폼을 표시하는 보기입니다.
보시다시피, PHP 코드가 제목이나 양식 등 동적 콘텐츠를 생성하는 데 사용되는 HTML 코드가 그들을 편성 해 볼 수있는 HTML 페이지를 만들고 있습니다.

```php
<?php
use yii\helpers\Html;
use yii\widgets\ActiveForm;

/* @var $this yii\web\View */
/* @var $form yii\widgets\ActiveForm */
/* @var $model app\models\LoginForm */

$this->title = 'Login';
?>
<h1><?= Html::encode($this->title) ?></h1>

<p>Please fill out the following fields to login:</p>

<?php $form = ActiveForm::begin(); ?>
    <?= $form->field($model, 'username') ?>
    <?= $form->field($model, 'password')->passwordInput() ?>
    <?= Html::submitButton('Login') ?>
<?php ActiveForm::end(); ?>
```

뷰에서이 뷰 템플릿을 관리하고 렌더링 하고 있으며 [[yii\web\View|view component]]를 참조하는 `$this` 에 액세스 할 수 있습니다.

`$this` 이외에 위의 예에서 `$model` 처럼 미리 정의 된 변수를 뷰에 둘 수 있습니다.
이러한 변수는 [뷰의 렌더링](#rendering-views)을 트리거하는 [컨트롤러](structure-controllers.md) 등의 객체에 의해 뷰에 *푸쉬*되 는 데이터를 나타냅니다.

> Tip | 팁 : 위의 예제 에서는 미리 정의 된 변수는 IDE에 인식되도록 뷰의 첫 번째 주석에 나열되어 있습니다.
  이것은 뷰에 문서를 붙이는도 좋은 방법입니다.


### 보안 <span id="security"></span>

HTML 페이지를 생성하는 뷰를 만들 때 최종 사용자로부터받은 데이터를 표시하기 전에 인코딩 및/또는 필터링을 하는 것이 좋습니다.
그렇지 않으면 당신의 응용 프로그램은 크로스 사이트 스크립팅 (http://en.wikipedia.org/wiki/Cross-site_scripting) 공격을 입을 우려가 있습니다.

일반 텍스트를 표시하기 위해서는 먼저 [[yii\helpers\Html::encode()]] 를 불러 인코딩합니다.
예를 들어, 다음 코드는 사용자의 이름을 표시하기 전에 인코딩을 하고 있습니다.

```php
<?php
use yii\helpers\Html;
?>

<div class="username">
    <?= Html::encode($user->name) ?>
</div>
```

HTML 콘텐츠를 표시하기 위해서는 [[yii\helpers\HtmlPurifier]]  사용하여 먼저 컨텐츠를 필터링합니다.
예를 들어 다음 코드는 게시물의 컨텐츠를 표시하기 전에 필터하고 있습니다.

```php
<?php
use yii\helpers\HtmlPurifier;
?>

<div class="post">
    <?= HtmlPurifier::process($post->text) ?>
</div>
```

> Tip | 팁 : HTMLPurifier 출력을 안전하게 함에있어서 좋은 역활을 하고 있지만 빠르지는 않습니다.
  응용 프로그램이 높은 성능을 요구하는 경우에는 필터 결과를 [캐싱](caching-overview.md)하는 것을 고려해야합니다.


### 뷰 의 구성 <span id="organizing-views"></span>

[컨트롤러](structure-controllers.md)와 [모델](structure-models.md) 은 같이 뷰를 구성하는 규칙이 있습니다. .

* 컨트롤러에 의해 표시되는 뷰는 기본적으로 디렉토리 `@app/views/ControllerID` 아래에 놓일 것입니다.
  여기서 `ControllerID`은 [컨트롤러 ID](structure-controllers.md#routes)를 말합니다.
  예를 들어, 컨트롤러 클래스가 `PostController` 라면 디렉토리는 `@app/views/post` 입니다.
  `PostCommentController` 의 경우 디렉토리는 @app/views/post-comment`입니다.
  또한 컨트롤러 모듈에 속하는 경우 디렉토리는 [[yii\base\Module::basePath|module directory]] 아래의 `views/ControllerID` 입니다.
* [위젯](structure-widgets.md)으로 표시되는 뷰는 기본적으로 `WidgetPath/views` 디렉토리 아래에 위치 할것 입니다.
  여기 `WidgetPath` 에서 위젯의 클래스 파일을 포함하는 디렉토리를 가리 킵니다.
* 다른 객체에 의해 표시되는 뷰에 대해서도 위젯의 경우 같은 규칙을 따를 것을 권장됩니다.

이러한 기본 뷰 디렉토리는 컨트롤러와 위젯의 [[yii\base\ViewContextInterface::getViewPath()]] 메소드를 오버라이드하여 사용자가 정의 할 수 있습니다.


## 뷰 렌더링 <span id="rendering-views"></span>

[컨트롤러](structure-controllers.md), [위젯](structure-widgets.md) 둘중에서 다른 어떤 장소에서도 뷰를 렌더링하는 메소드를 호출하여 뷰를 렌더링 할 수 합니다.
이러한 방법은 아래에 보이는 것과 같은 유사한 설명을 공유합니다.

```
/ **
 * @param string $view 뷰 이름 또는 파일 경로 (실제 렌더링 방법에 따라 다름)
 * @param array $params 뷰에 전달되는 데이터
 * @return string 렌더링 결과
 * /
methodName($view, $params = [])
```


### 컨트롤러에서 렌더링 하기 <span id="rendering-in-controllers"></span>

[컨트롤러](structure-controllers.md) 에서 뷰를 렌더링하기 위해서는 다음의 컨트롤러 메소드를 호출 할 수 있습니다.

* [[yii\base\Controller::render()|render()]] : 명명 된 뷰 (#named-views)를 렌더링하고 그 결과에 레이아웃 (#layouts)를 적용한다.
* [[yii\base\Controller::renderPartial()|renderPartial()]] : 명명 된 뷰 (#named-views)를 레이아웃없이 렌더링한다.
* [[yii\web\Controller::renderAjax()|renderAjax()]] : 명명 된 뷰 (#named-views)를 레이아웃없이 렌더링 등록 된 모든 JS/CSS 스크립트 및 파일 를 적용한다.
  일반적으로 AJAX 웹 요청에 대한 응답에 사용된다.
* [[yii\base\Controller::renderFile()|renderFile()]] : 뷰 파일의 경로 또는 [별칭](concept-aliases.md) 형식으로 지정된 뷰를 렌더링한다.
* [[yii\base\Controller::renderContent()|renderContent()]] : 정적 문자열을 렌더링하여 현재 적용 가능한 레이아웃 (#layouts)를 포함합니다. 이 메소드는 버전 2.0.1 이상에서 사용 가능.

예를 들어,

```php
namespace app\controllers;

use Yii;
use app\models\Post;
use yii\web\Controller;
use yii\web\NotFoundHttpException;

class PostController extends Controller
{
    public function actionView($id)
    {
        $model = Post::findOne($id);
        if ($model === null) {
            throw new NotFoundHttpException;
        }

        // renders a view named "view" and applies a layout to it
        return $this->render('view', [
            'model' => $model,
        ]);
    }
}
```


### 위젯 렌더링 <span id="rendering-in-widgets"></span>

[위젯](structure-widgets.md) 에서 뷰를 렌더링하기 위해 다음 위젯 메소드를 사용할 수 있습니다.

* [[yii\base\Widget::render()|render()]]: [명명 된 뷰](#named-views)를 렌더링한다.
* [[yii\base\Widget::renderFile()|renderFile()]] : 뷰 파일의 경로 또는 별칭 (concept-aliases.md) 형식으로 지정된 뷰를 렌더링한다.

예를 들어,

```php
namespace app\components;

use yii\base\Widget;
use yii\helpers\Html;

class ListWidget extends Widget
{
    public $items = [];

    public function run()
    {
        // renders a view named "list"
        return $this->render('list', [
            'items' => $this->items,
        ]);
    }
}
```


### 뷰에서 렌더링 <span id="rendering-in-views"></span>

[[yii\base\View|view component]]에서 제공하는 다음의 방법 중 하나를 사용하면 뷰에서 다른 뷰를 렌더링 할 수 있습니다.

* [[yii\base\View::render()|render()]] : [명명 된 뷰](#named-views)를 렌더링한다.
* [[yii\web\View::renderAjax()|renderAjax()]] : [명명 된 뷰](#named-views)을 렌더링하고 등록 된 모든 JS/CSS 스크립트 및 파일을 적용.
  일반적으로 AJAX 웹 요청에 대한 응답에 사용된다.
* [[yii\base\View::renderFile()|renderFile()]] : 뷰 파일의 경로 또는 별칭 (concept-aliases.md) 형식으로 지정된 뷰를 렌더링한다.

예를 들어, 뷰에서 다음 코드는 현재 렌더링 된 뷰와 동일한 디렉토리에있는 `_overview.php` 는보기 파일을 렌더링합니다.
보기에서 [[yii\base\View|view]] 구성 요소를 참조하는 것을 기억하십시오.

```php
<?= $this->render('_overview') ?>
```


### 다른 장소에서의 렌더링 <<span id="rendering-in-other-places"></span>

장소가 어디든 `Yii::$app->view` 라는 식으로 [[yii\base\View|view]] 응용 프로그램 구성 요소에 액세스 할 수 있기 때문에, 위의 [[yii\base\View|view]] 컴포넌트 메소드를 사용하여 뷰를 렌더링 할 수 있습니다.
예를 들어,

```php
// displays the view file "@app/views/site/license.php"
echo \Yii::$app->view->renderFile('@app/views/site/license.php');
```


### 명명 된 뷰 <span id="named-views"></span>

뷰를 렌더링 할 때 뷰를 지정하려면 뷰의 이름 또는 뷰 파일의 경로/별칭 또는 중 하나를 사용할 수 있습니다.
대부분의 경우는 더 간결하고 유연한 전자를 사용합니다.
이름으로 지정된 뷰를 *명명 된 뷰*라고합니다.

뷰의 이름은 다음 규칙에 따라 해당 뷰 파일의 경로로 확인됩니다.

* 뷰 이름은 파일 확장자를 생략 할 수 있습니다. 이 경우 `.php` 이 확장자로 사용됩니다.
  예를 들어, `about` 는 뷰 이름은 `about.php`라 는 파일 이름에 해당합니다.
* 뷰 이름이 두 슬래시 (`//`) 로 시작하는 경우 해당 뷰 파일의 경로는 `@app/views/ViewName`.입니다.
  즉, 뷰 파일은[[yii\base\Application::viewPath|application's view path]] 아래에서 찾아야합니다.
  예를 들어, `//site/about` 는 `@app/views/site/about.php`. 으로 해결됩니다.
* 뷰 이름이 하나의 슬래시 (`/`)로 시작하는 경우 뷰 파일의 경로는 뷰 이름 앞에 현재 활성화 된 모듈 (structure-modules.md)의  [[yii\base\Module::viewPath|view path]] 를 두 어서 형성됩니다.
  활성 모듈이없는 경우는  `@app/views/ViewName` 가 사용됩니다.
  예를 들어,`/user/create` 는 현재 활성화 된 모듈이 `user` 인 경우는 `@app/modules/user/views/user/create.php` 으로 해결됩니다.
  활성 모듈이없는 경우는 뷰 파일의 경로는 `@app/views/user/create.php` 입니다.
* 뷰가[[yii\base\View::context|context]]를 따라 렌더링 해당 컨텍스트가 [[yii\base\ViewContextInterface]]를 구현하고있는 경우는 뷰 파일의 경로는 컨텍스트 의 [[yii\base\ViewContextInterface::getViewPath()|view path]] 를 뷰 이름 앞에 두 어서 형성됩니다.
  이것은 주로 컨트롤러와 위젯에서 렌더링되는 뷰에 적용됩니다.
  예를 들어, 컨텍스트가 `SiteController` 컨트롤러 인 경우 `about` 는 `@app/views/site/about.php` 으로 해결됩니다.
* 몇몇 뷰가 다른 뷰에서 렌더링되는 경우는 후자의 뷰 파일을 포함하는 디렉토리가 전자 뷰 이름 앞에 놓여 실제 뷰 파일의 경로가 형성됩니다.
  예를 들어,`item` 은 `@app/views/post/item.php` 라는 뷰에서 렌더링되는 경우 `@app/views/post/item`으로 해결됩니다.

위의 규칙에 따라 컨트롤러 `app\controllers\PostController`에서 `$ this->render( 'view')`를 호출하면 실제로 뷰 파일 `@ app/views/post/view.php` 이 렌더링되는 반면 뷰에서 `$this->render('_overview')` 를 호출하면 뷰 파일 `@app/views/post/_overview.php`가 렌더링 될 것입니다.


### 뷰에서 데이터에 액세스 하기 <span id="accessing-data-in-views"></span>

뷰에서 데이터에 액세스하기위한 방법이 두 가지가 있습니다. "푸시"와 "풀"입니다.

뷰를 렌더링하는 방법 두 번째 매개 변수로 데이터를 전달하는 것이 "푸시"접근입니다.
데이터는 "이름 - 값"쌍의 배열로 표현되어야합니다.
뷰가 렌더링 될 때, PHP의`extract ()`함수가이 배열에 대해서 불려 뷰에서 사용할 변수가 추출됩니다.
예를 들어, 다음 코드는 컨트롤러에서 뷰를 렌더링하고 있습니다 만,`report`보기에 두 변수, 즉`$ foo = 1`과`$ bar = 2`를 푸시하고 ??있습니다.

```php
echo $ this-> render ( 'report',
    'foo'=> 1,
    'bar'=> 2,
]);
```

「풀」의 접근 방식은 [yii \ base \ View | 뷰 구성 요소 또는보기에서 액세스 할 수있는 다른 객체 (예를 들어`Yii :: $ app`)에서 적극적으로 데이터를 읽어내는 것입니다.
다음 코드 예제처럼보기 중에서는`$ this-> context`는 식으로 컨트롤러 오브젝트를 취득 할 수 있습니다.
그 결과`report` 뷰에서 컨트롤러의 모든 속성과 메서드에 액세스 할 수 있습니다.
다음 예제에서는 컨트롤러 ID에 액세스하고 있습니다.

```php
The controller ID is : <? = $ this-> context-> id?>
```

일반적으로 "밀어"접근이 뷰에서 데이터에 액세스하는 방법으로 권장됩니다.
왜냐하면 뷰의 컨텍스트 개체에 대한 의존이 더 적기 때문입니다.
그 단점은 항상 데이터 배열을 수동으로 작성해야한다는 것입니다.
뷰가 공유되어 다양한 장소에서 렌더링되는 경우, 그 작업이 귀찮아지고 또한 실수도 생기기 쉽습니다.


### 뷰간에 데이터를 공유하는 <span id = "sharing-data-among-views"> </ span>

[yii \ base \ View | 뷰 구성 요소]가 제공하는 [yii \ base \ View :: params | params] 속성을 사용하면 뷰간에 데이터를 공유 할 수 있습니다.

예를 들어,`about`라는 뷰에서 다음과 같은 코드를 사용하여 부스러기 목록의 현재의 구분을 지정할 수 있습니다.

```php
$ this-> params [ 'breadcrumbs'] [] = 'About Us';
```

그리고 레이아웃 (# layouts) 파일 (이것도 하나의 뷰입니다)에서 [yii \ base \ View :: params | params]에 의해 전달 된 데이터를 사용하여 부스러기 목록보기 할 수 있습니다.

```php
<? = yii \ widgets \ Breadcrumbs :: widget ([
    'links'=> isset ($ this-> params [ 'breadcrumbs'])? $ this-> params [ 'breadcrumbs'] : [],
])?>
```


## 레이아웃 <span id = "layouts"> </ span>

레이아웃은 여러 뷰의 공통 부분을 나타내는 특수한 유형의 뷰입니다.
예를 들어, 대부분의 웹 애플리케이션은 페이지의 머리글과 바닥 글을 가지고 있습니다.
모든 시점에서 동일한 머리글과 바닥 글을 반복 할 수도 있지만, 더 좋은 방법은 그런 것은 레이아웃에서 한 번만하여 컨텐트 뷰의 렌더링 결과를 레이아웃 안의 적절한 위치에 삽입하는 것입니다 .


### 레이아웃을 만드는 <span id = "creating-layouts"> </ span>

레이아웃 또한 뷰이므로 일반 뷰와 동일한 방법으로 만들 수 있습니다.
기본적으로 레이아웃은`@ app / views / layouts` 디렉토리에 저장됩니다.
[모듈] (structure-modules.md)에서 사용되는 레이아웃은 [yii \ base \ Module :: basePath | 모듈 디렉토리 아래의`views / layouts` 디렉토리에 저장 될 것 입니다.
기본 레이아웃 디렉토리는 응용 프로그램 또는 모듈의 [yii \ base \ Module :: layoutPath] 속성을 구성하여 사용자 정의 할 수 있습니다.

다음 예제는 레이아웃이 무엇인지를 보여줍니다. 설명을 위해, 레이아웃 안에있는 코드를 크게 단순화하고 있음에주의하십시오.
사실, 헤드 태그와 메인 메뉴, 그리고 더 많은 콘텐츠를 추가 할 필요가있을 것입니다.

```php
<? php
use yii \ helpers \ Html;

/ * @var $ this yii \ web \ View * /
/ * @var $ content string * /
?>
<? php $ this-> beginPage ()?>
<! DOCTYPE html>
<html lang = "en">
<head>
    <meta charset = "UTF-8"/>
    <? = Html :: csrfMetaTags ()?>
    <title> <? = Html :: encode ($ this-> title)?> </ title>
    <? php $ this-> head ()?>
</ head>
<body>
<? php $ this-> beginBody ()?>
    <header> My Company </ header>
    <? = $ content?>
    <footer> & copy; 2014 by My Company </ footer>
<? php $ this-> endBody ()?>
</ body>
</ html>
<? php $ this-> endPage ()?>
```

보시다시피 레이아웃은 모든 페이지에 공통 HTML 태그를 생성합니다.
`<body>`섹션에서 레이아웃이`$ content`라는 변수를 에코하고 있습니다 만, 이것은 컨텐트 뷰의 렌더링 결과를 나타내는 것이며, [yii \ base \ Controller :: render () ]]가 호출 될 때 레이아웃에 푸시되는 것입니다.

위의 코드에 표시된 것처럼, 대부분의 레이아웃은 다음에 드는 메소드를 호출해야합니다.
이러한 방법은 주로 렌더링 과정에 관한 이벤트를 트리거하는 것으로, 다른 장소에서 등록 된 스크립트 나 태그가 메소드가 호출 된 위치에 정확하게 주입되도록하기위한 것입니다.

- [yii \ base \ View :: beginPage () | beginPage () :이 메소드가 레이아웃의 시작 부분에서 호출되어야합니다.
  이 페이지의 시작을 나타내는 [yii \ base \ View :: EVENT_BEGIN_PAGE | EVENT_BEGIN_PAGE] 이벤트를 트리거합니다.
- [yii \ base \ View :: endPage () | endPage () :이 메소드가 레이아웃의 끝에서 호출되어야합니다.
  이 페이지의 종료를 나타내는 [yii \ base \ View :: EVENT_END_PAGE | EVENT_END_PAGE] 이벤트를 트리거합니다.
- [yii \ web \ View :: head () | head () :이 메소드가 HTML 페이지의`<head>`섹션에서 불려야합니다.
  이 방법은 페이지 렌더링이 완료 될 때 등록 된 head의 HTML 코드 (링크 태그, 메타 태그 등)에 대체 플레이스 홀더를 생성합니다.
- [yii \ web \ View :: beginBody () | beginBody () :이 메소드가`<body>`섹션의 시작 부분에서 호출되어야합니다.
  이 메소드는 [yii \ web \ View :: EVENT_BEGIN_BODY | EVENT_BEGIN_BODY] 이벤트를 트리거하고 body의 시작 위치를 대상으로하는 등록 된 HTML 코드 (JavaScript)로 대체 플레이스 홀더를 생성합니다.
- [yii \ web \ View :: endBody () | endBody () :이 메소드가`<body`> 섹션의 끝으로 불리는해야합니다.
  이 메소드는 [yii \ web \ View :: EVENT_END_BODY | EVENT_END_BODY] 이벤트를 트리거하고 body의 끝을 대상으로 등록 된 HTML 코드 (JavaScript)로 대체 플레이스 홀더를 생성합니다.


### 레이아웃에서 데이터에 액세스 할 <span id = "accessing-data-in-layouts"> </ span>

레이아웃에서 미리 정의 된 두 변수, 즉`$ this`과`$ content`에 액세스 할 수 있습니다.
전자는 일반 뷰에서와 마찬가지로 [yii \ base \ View | 뷰 구성 요소를 참조합니다.
한편, 후자는 컨트롤러에서 [yii \ base \ Controller :: render () | render ()] 메소드를 호출하여 렌더링되는 컨텐트 뷰의 렌더링 결과를 포함합니다.

레이아웃에서 다른 데이터에 액세스 할 필요가있을 때에는 뷰에서 데이터에 액세스하는 (# accessing-data-in-views) 절에 설명되어있는 「풀」의 방법을 사용 해야합니다.
컨텐트 뷰에서 레이아웃에 데이터를 전달해야 할 때 뷰간에 데이터를 공유 함 (# sharing-data-among-views) 절에서 설명 된 방법을 사용할 수 있습니다.


### 레이아웃을 사용 <span id = "using-layouts"> </ span>

[컨트롤러에서의 렌더링 (# rendering-in-controllers) 섹션에 설명 된대로 컨트롤러에서 [yii \ base \ Controller :: render () | render ()] 메소드를 불러 뷰를 렌더링 할 때 렌더링 결과에 레이아웃이 적용됩니다.
기본적으로`@ app / views / layouts / main.php`는 레이아웃이 사용됩니다.

[yii \ base \ Application :: layout] 또는 [yii \ base \ Controller :: layout]] 중 하나를 구성하여 다른 레이아웃을 사용할 수 있습니다.
전자는 모든 컨트롤러에 의해 사용되는 레이아웃을 결정하는 것이지만, 후자는 개별 컨트롤러에 대해 전자를 대체하는 것입니다.
예를 들어 다음 코드는`post` 컨트롤러가 뷰를 렌더링 할 때`@ app / views / layouts / post.php`을 레이아웃으로 사용하게하는 것입니다.
기타 컨트롤러는`layout` 속성에 언급되지 않았다고 가정하면 계속 기본`@ app / views / layouts / main.php`을 레이아웃으로 사용합니다.
 
```php
namespace app \ controllers;

use yii \ web \ Controller;

class PostController extends Controller
{
    public $ layout = 'post';
    
    // ...
}
```

모듈에 속하는 컨트롤러는 모듈에서 [yii \ base \ Module :: layout | layout] 속성을 구성하여 모듈의 컨트롤러에 특정 레이아웃을 사용할 수도 있습니다.

`layout` 속성은 다른 레벨 (컨트롤러, 모듈, 응용 프로그램)로 구성되어있다 것이므로, Yii는 뒤에서 두 단계를 밟아 특정 컨트롤러에서 실제로 사용되는 레이아웃 파일이 무엇인지를 결정 합니다.

첫 번째 단계에서 Yii는 레이아웃의 값과 컨텍스트 모듈을 결정합니다.

- 컨트롤러에서 [yii \ base \ Controller :: layout] 속성이 null가 아닌 경우, 그것을 레이아웃의 값으로 사용 컨트롤러에서 [yii \ base \ Controller :: module | 모듈을 컨텍스트 모듈로 사용한다.
- [yii \ base \ Controller :: layout | layout]]가 null의 경우는 컨트롤러의 조상이되고있는 모든 모듈 (응용 프로그램 자체도 포함)을 찾은 다음 [yii \ base \ Module :: layout | layout] 속성이 null이 아닌 첫 번째 모듈을 찾을 수 있습니다.
  발견 된 모듈과 [yii \ base \ Module :: layout | layout]]의 값을 컨텍스트 모듈 선정 된 레이아웃의 값으로한다.
  그런 모듈을 찾을 수없는 경우에는 레이아웃이 적용되지 않는다는 것을 의미한다.

두 번째 단계에서는 첫 번째 단계에서 결정된 레이아웃의 값과 컨텍스트 모듈에 따라 실제 레이아웃 파일을 결정합니다.
레이아웃 값은 다음 중 하나에있을 수 있습니다.

- 경로 별칭 (예를 들어,`@ app / views / layouts / main`).
- 절대 경로 (예를 들어,`/ main`) : 즉 슬래쉬 레이아웃의 값의 경우.
  실제 레이아웃 파일은 응용 프로그램의 [yii \ base \ Application :: layoutPath | 레이아웃 경로 (기본적으로`@ app / views / layouts`)에서 찾아야한다.
- 상대 경로 (예를 들어,`main`) : 실제 레이아웃 파일 컨텍스트 모듈의 [yii \ base \ Module :: layoutPath | 레이아웃 경로 (기본적으로 [yii \ base \ Module :: basePath | 모듈 디렉토리 ] 아래의`views / layouts` 디렉토리)에서 찾아야한다.
- 부울`false` : 레이아웃은 적용되지 않는다.

레이아웃의 값이 파일 확장명을 포함하지 않는 경우, 기본값 인`.php`을 사용합니다.


### 상자의 레이아웃 <span id = "nested-layouts"> </ span>

때로는 한 레이아웃에서 다른 레이아웃을보실 수있을 것입니다.
예를 들어, 웹 사이트의 다른 섹션에서 다른 레이아웃을 사용 싶지만 그 레이아웃은 모두 전반적으로 HTML5 페이지 구조를 생성하는 동일한 기본 레이아웃을 공유하고 있다는 경우입니다.
이 목적을 달성 할 다음과 같이 자식 레이아웃에서 [yii \ base \ View :: beginContent () | beginContent ()] 및 [yii \ base \ View :: endContent () | endContent ()를 호출함으로써 가능합니다.

```php
<? php $ this-> beginContent ( '@ app / views / layouts / base.php');?>

... 아이 레이아웃의 컨텐츠를 여기에 ...

<? php $ this-> endContent ();?>
```

위의 코드에서 알 수 있듯이 자식 레이아웃의 컨텐츠는 [yii \ base \ View :: beginContent () | beginContent ()] 및 [yii \ base \ View :: endContent () | endContent ()]] 에 의해 포위해야합니다.
[yii \ base \ View :: beginContent () | beginContent ()으로 전달되는 매개 변수는 부모 레이아웃이 무엇인지를 지정하는 것입니다.
레이아웃 파일 또는 별칭 중 하나를 사용할 수 있습니다.

위의 방법을 사용하여 2 레벨 이상의 레이아웃을 중첩 할 수 있습니다.


### 블록을 사용 <span id = "using-blocks"> </ span>

블록을 사용하면 한 위치에서 뷰 컨텐트를 정의하여 다른 위치에서 그것을 볼 수 있습니다.
블록은 대부분은 레이아웃과 함께 사용됩니다.
예를 들어, 블록을 컨텐트 뷰에서 정의하고 그것을 레이아웃으로 표시하는 것이 가능합니다.

[yii \ base \ View :: beginBlock () | beginBlock ()] 및 [yii \ base \ View :: endBlock () | endBlock ()를 부르고 블록을 정의합니다.
그러자 그 블록을`$ view-> blocks [$ blockID]`의해 액세스 할 수있게합니다.
여기서`$ blockID` 정의 할 때 블록에 할당 된 고유 ID를 말합니다.

다음 예제는 어떻게 블록을 사용하면 레이아웃의 특정 부분을 컨텐트 뷰에서 사용자 정의 할 수 있는지 보여줍니다.

첫째, 컨텐트 뷰에서 하나 이상의 블록을 정의합니다.

```php
...

<? php $ this-> beginBlock ( 'block1');?>

... block1의 콘텐츠 ...

<? php $ this-> endBlock ();?>

...

<? php $ this-> beginBlock ( 'block3');?>

... block3의 콘텐츠 ...

<? php $ this-> endBlock ();?>
```

다음 레이아웃보기에서 얻을 수 있다면 블록을 렌더링 블록이 정의되어 있지 않으면 어떠한 기본 컨텐트를 표시합니다.

```php
...
<? php if (isset ($ this-> blocks [ 'block1'])) :?>
    <? = $ this-> blocks [ 'block1']?>
<? php else :?>
    ... block1의 기본 콘텐츠 ...
<? php endif;?>

...

<? php if (isset ($ this-> blocks [ 'block2'])) :?>
    <? = $ this-> blocks [ 'block2']?>
<? php else :?>
    ... block2의 기본 콘텐츠 ...
<? php endif;?>

...

<? php if (isset ($ this-> blocks [ 'block3'])) :?>
    <? = $ this-> blocks [ 'block3']?>
<? php else :?>
    ... block3의 기본 콘텐츠 ...
<? php endif;?>
...
```


## 뷰 구성 요소를 사용 <span id = "using-view-components"> </ span>

[yii \ base \ View | 뷰 구성 요소] 뷰에 관련된 많은 기능을 제공합니다.
뷰 구성 요소는 [yii \ base \ View] 또는 하위 클래스의 개별 인스턴스를 만들어도 얻을 수 있지만 대부분의 경우는`view` 응용 프로그램 구성 요소를 주로 사용하게 될 것입니다.
이 구성 요소는 응용 프로그램의 구성 정보 (structure-applications.md # application-configurations)에서 다음과 같이 구성 할 수 있습니다.

```php
[
    // ...
    'components'=>
        'view'=>
            'class'=> 'app \ components \ View'
        ,
        // ...
    ,
]
```

뷰 구성 요소는 다음에 드는보기 관련 유용한 기능을 제공합니다. 각각은 독립 절에서 자세히 설명됩니다.

* [테마] (output-theming.md) : 웹 사이트의 테마를 개발하고 변경하는 것을 허용합니다.
* [단편 캐싱 (caching-fragment.md) : 웹 페이지 중 조각을 캐시하는 것을 허용합니다.
* 클라이언트 스크립트 처리 (output-client-scripts.md) : CSS 및 JavaScript의 등록 및 렌더링을 지원합니다.
* 자산 번들의 취급 (structure-assets.md) : [에셋 번들 (structure-assets.md)의 등록 및 렌더링을 지원합니다.
* 대체 템플릿 엔진 (tutorial-template-engines.md) : [Twig (http://twig.sensiolabs.org/), Smarty (http://www.smarty.net/) 등 다른 템플릿 엔진을 사용하는 것을 허용합니다.

다음에 드는 미성년자 있어도 유용한 여러 기능은 웹 페이지를 개발할 때 자주 사용하는 것입니다.


### 페이지 제목을 설정하는 <span id = "setting-page-titles"> </ span>

어떤 웹 페이지에 제목이 없으면 안됩니다. 일반적으로 제목 태그 [layout (# layouts)에서 표시됩니다.
그러나 실제에 있어서는 많은 경우 제목은 레이아웃이 아니라 컨텐트 뷰에서 결정됩니다.
이 문제를 해결하기 위해 [yii \ web \ View]는 타이틀 정보를 컨텐트 뷰에서 레이아웃에 전달하는 [yii \ web \ View :: title | title] 속성을 제공합니다 .

이 기능을 이용하기 위해서는 모든 컨텐트 뷰에서 다음과 같이 제목을 설정합니다.

```php
<? php
$ this-> title = 'My page title';
?>
```

그리고 레이아웃보기에서`<head>`섹션에 다음 코드를 잊지 말고 쓰도록합니다.

```php
<title> <? = Html :: encode ($ this-> title)?> </ title>
```


### 메타 태그를 등록하는 <span id = "registering-meta-tags"> </ span>

웹 페이지는 일반적으로 다양한 관계자에 의해 요구되는 다양한 메타 태그를 생성해야합니다.
페이지 제목처럼 메타 태그는`<head>`섹션에 출현하며, 일반적으로 레이아웃에서 생성됩니다.

어떤 메타 태그를 생성하는지 컨텐트 뷰에서 지정하고 싶은 경우에는 아래와 같이 [[yii \ web \ View :: registerMetaTag ()을 컨텐트 뷰의 부를 수 있습니다.

```php
<? php
$ this-> registerMetaTag ([ 'name'=> 'keywords', 'content'=> 'yii, framework, php');
?>
```

위의 코드는 뷰 구성 요소에 의해 "keywords"메타 태그를 등록하는 것입니다. 등록 된 메타 태그는 레이아웃이 렌더링을 완료 한 후 렌더링됩니다.
즉 레이아웃에서 [yii \ web \ View :: head ()를 호출 한 위치에 다음의 HTML 코드가 생성되어 삽입됩니다.

```php
<meta name = "keywords"content = "yii, framework, php">
```

[yii \ web \ View :: registerMetaTag ()를 여러 차례 호출했을 경우 메타 태그가 동일 여부에 관계없이 여러 메타 태그가 등록되는 것에주의하십시오.

특정 형식의 메타 태그의 인스턴스가 하나만되는 것을 보증하고 싶은 경우는,이?? 메소드를 부를 때 두 번째 매개 변수로 키를 지정할 수 있습니다.
예를 들어 다음 코드에서는 두 "description"메타 태그를 등록하고, 두 번째 것만이 렌더링되는 것입니다.

```html
$ this-> registerMetaTag ([ 'name'=> 'description', 'content'=> '내가 Yii로 만든 멋진 웹 사이트이다 제ィ!', 'description');
$ this-> registerMetaTag ([ 'name'=> 'description', 'content'=> '재미있는 너구리에 대한 웹 사이트입니다.', 'description');
```


### 링크 태그를 등록하는 <span id = "registering-link-tags"> </ span>

[메타 태그 (# registering-meta-tags)처럼 링크 태그도 많은 경우에 유용합니다.
예를 들어, favicon을 사용자 정의하거나 RSS 피드를 가리거나 OpenID를 다른 서버로 위임하거나 등등.
링크 태그도 [yii \ web \ View :: registerLinkTag ()를 사용하여 메타 태그와 같은 방법으로 처리 할 수?? 있습니다.
예를 들어, 컨텐트 뷰에서 다음과 같이 링크 태그를 등록하실 수 있습니다.

```php
$ this-> registerLinkTag ([
    'title'=> 'Yii 라이브 뉴스'
    'rel'=> 'alternate'
    'type'=> 'application / rss + xml',
    'href'=> 'http://www.yiiframework.com/rss.xml/'
]);
```

위의 코드는 다음과 같은 결과가됩니다.

```html
<link title = "Yii 라이브 뉴스"rel = "alternate"type = "application / rss + xml"href = "http://www.yiiframework.com/rss.xml/">
```

[yii \ web \ View :: registerMetaTag () | registerMetaTags ()]]처럼 [[yii \ web \ View :: registerLinkTag () | registerLinkTag ()를 부를 때 키를 지정하면 같은 링크 태그를 반복하여 생성되는 것을 방지 할 수 있습니다.


## 뷰 이벤트 <span id = "view-events"> </ span>

[yii \ base \ View | 뷰 구성 요소] 뷰를 렌더링하는 과정에서 몇 가지 이벤트를 트리거합니다.
이러한 이벤트에 반응하여 뷰에 콘텐츠를 주입하거나 최종 사용자에게 전송되기 전에 렌더링 결과를 가공 할 수 있습니다.

- [yii \ base \ View :: EVENT_BEFORE_RENDER | EVENT_BEFORE_RENDER] : 컨트롤러에서 파일을 렌더링하기 전에 트리거됩니다.
  이 이벤트 핸들러는 [yii \ base \ ViewEvent :: isValid]를 false로 설정하여 렌더링 프로세스를 취소 할 수 있습니다.
- [yii \ base \ View :: EVENT_AFTER_RENDER | EVENT_AFTER_RENDER] : 파일의 렌더링 후 [yii \ base \ View :: afterRender ()를 호출하여 트리거됩니다.
  이 이벤트 핸들러는 렌더링 결과를 속성 [yii \ base \ ViewEvent :: output]를 통해 취득하여 그것을 수정하고 렌더링 결과를 변경할 수 있습니다.
- [yii \ base \ View :: EVENT_BEGIN_PAGE | EVENT_BEGIN_PAGE] : 레이아웃에서 [yii \ base \ View :: beginPage ()를 호출하여 트리거됩니다.
- [yii \ base \ View :: EVENT_END_PAGE | EVENT_END_PAGE] : 레이아웃에서 [yii \ base \ View :: endPage ()를 호출하여 트리거됩니다.
- [yii \ web \ View :: EVENT_BEGIN_BODY | EVENT_BEGIN_BODY] : 레이아웃에서 [yii \ web \ View :: beginBody ()를 호출하여 트리거됩니다.
- [yii \ web \ View :: EVENT_END_BODY | EVENT_END_BODY] : 레이아웃에서 [yii \ web \ View :: endBody ()를 호출하여 트리거됩니다.

예를 들어, 다음 코드는 페이지의 body의 마지막에 현재 날짜를 삽입하는 것입니다.

```php
\ Yii :: $ app-> view-> on (View :: EVENT_END_BODY, function () {
    echo date ( 'Y-m-d');
});
```


## 정적 페이지를 렌더링하는 <span id = "rendering-static-pages"> </ span>

정적 페이지라고하는 것은 주된 콘텐츠의 대부분이 정적 인 것 컨트롤러에서 보낸 동적 데이터에 액세스 할 필요가없는 페이지를 말합니다.

정적 페이지는 그 코드를 뷰에두고 컨트롤러에서 다음과 같은 코드를 사용하면 표시 할 수 있습니다.

```php
public function actionAbout ()
{
    return $ this-> render ( 'about');
}
```

웹 사이트가 많은 정적 페이지를 포함하는 경우, 같은 코드를 여러 번 반복하는 것은 매우 귀찮은 것입니다.
이 문제를 해결하기 위해 [yii \ web \ ViewAction]라는 독립 실행 형 작업 (structure-controllers.md # standalone-actions)를 컨트롤러에 도입 할 수 있습니다.
예를 들어,

```php
namespace app \ controllers;

use yii \ web \ Controller;

class SiteController extends Controller
{
    public function actions ()
    {
        return [
            'page'=>
                'class'=> 'yii \ web \ ViewAction'
            ,
        ];
    }
}
```

이렇게하면 디렉토리`@ app / views / site / pages` 아래에`about`라는 뷰를 만들 때 다음 URL을 통해이보기를 표시 할 수있게합니다.

```
http : //localhost/index.php? r = site / page & view = about
```

`view`는`GET` 매개 변수가 어떤 뷰가 요청되고 있는지 [yii \ web \ ViewAction]]에게 알려준다.
그래서 액션이 뷰를 디렉토리`@ app / views / site / pages`에서 찾습니다.
[yii \ web \ ViewAction :: viewPrefix]를 구성하여 뷰를 찾을 경로를 지정 할 수 있습니다.


## 모범 사례 <span id = "best-practices"> </ span>

뷰는 최종 사용자가 원하는 형식으로 모델을 표현하는 것에 대해 책임이 있습니다. 일반적으로보기

* 주로 표시 목적 코드를 포함한다. 예를 들어, HTML 또는 데이터를 따라 서식 화하여 표현하는 간단한 PHP 코드 등.
* DB 쿼리를 실행하는 코드는 포함되어서는 없습니다. 그런 코드는 모델에서 실행되어야합니다.
*`$ _GET` 나`$ _POST` 같은 요청 데이터에 직접 액세스해야하는 것이 아닙니다. 그것은 컨트롤러의 일입니다.
  요청 데이터가 필요한 경우는 컨트롤러에서 뷰로 푸시되어야합니다.
* 모델의 속성을 읽어 낼 수 있습니다. 그러나 그것을 수정해야하는 것이 아닙니다.

뷰를 관리하기 쉽도록하기 위해 너무 복잡 뷰와 중복 코드를 너무 많이 포함 뷰를 만드는 것은 피합시다.
이 목적을 달성하기 위해 다음과 같은 기술을 사용할 수 있습니다.

* 일반적인 표시 섹션 (페이지 머리글과 바닥 글 등)을 나타 내기 위해 레이아웃 (# layouts)를 사용한다.
* 복잡한 뷰는 몇 가지 작은 뷰로 분할한다. 앞서 언급 한 렌더링 방법을 사용하면 작은 뷰를 렌더링하고 큰 뷰를 작성하는 것이 가능하다.
* 뷰의 구성 요소로 위젯 (structure-widgets.md)를 사용한다.
* 뷰에서 데이터를 변환하고 형식화하기위한 헬퍼 클래스를 만들어 사용한다.