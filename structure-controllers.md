컨트롤러
============

컨트롤러는 [MVC] (http://ja.wikipedia.org/wiki/Model_View_Controller) 아키텍처의 일부를 이루는 것입니다.
그것은 [yii \ base \ Controller]를 확장 한 클래스의 객체이며, 요청의 처리와 응답의 생성에 대해 책임을지지 않습니다.
구체적으로는 응용 프로그램 (structure-applications.md)에서 제어를 인수 한 후에 컨트롤러는 들어온 요청의 데이터를 분석하고 그것을 모델 (structure-models.md)에 부쳐 모델이 생성 한 결과를 뷰 (structure-views.md)에 투입하고 결국 밖으로 나가는 응답을 생성합니다.


## 액션 <span id = "actions"> </ span>

컨트롤러는 최종 사용자가 주소를 지정하고 실행을 요청 할 수있는 가장 기본적인 단위이다 * 액션 *로 구성됩니다.
컨트롤러는 하나 이상의 작업을 가질 수 있습니다.

다음의 예는`view`와`create`라는 두 액션을 가지는`post` 컨트롤러를 보여줍니다.

```php
namespace app \ controllers;

use Yii;
use app \ models \ Post;
use yii \ web \ Controller;
use yii \ web \ NotFoundHttpException;

class PostController extends Controller
{
    public function actionView ($ id)
    {
        $ model = Post :: findOne ($ id);
        if ($ model === null) {
            throw new NotFoundHttpException;
        }

        return $ this-> render ( 'view',
            'model'=> $ model,
        ]);
    }

    public function actionCreate ()
    {
        $ model = new Post;

        if ($ model-> load (Yii :: $ app-> request-> post ()) && $ model-> save ()) {
            return $ this-> redirect ( 'view', 'id'=> $ model-> id]);
        } else {
            return $ this-> render ( 'create',
                'model'=> $ model,
            ]);
        }
    }
}
```

`view` 액션 (`actionView ()`메소드로 정의됩니다)에서 코드는 먼저 요청 된 모델의 ID에 따라 모델 (structure-models.md)를 읽습니다.
모델의 판독이 성공했을 때는`view`라는 뷰 (structure-views.md)를 사용하여 모델을 표시합니다.
실패했을 때는 예외를 던집니다.

`create` 액션 (`actionCreate ()`메소드로 정의됩니다)에서도 코드는 비슷한 것입니다.
먼저 요청 데이터를 사용하여 모델 (structure-models.md)에 데이터를 투입하여 모델을 저장하는 것을 시도합니다.
모두가 성공했을 때는 새로 만든 모델의 ID를 사용하여`view` 액션 브라우저를 리디렉션합니다.
중 하나가 실패하면 사용자가 필요한 데이터를 입력 할 수 있도록하기위한`create`보기를 표시합니다.


## 루트 <span id = "routes"> </ span>

최종 사용자는 이른바 * 루트 * 따라 액션의 주소를 지정합니다.
루트는 다음 부분으로 구성된 문자열입니다.

* 모듈 ID :이 부분은 컨트롤러가 응용 프로그램이 아닌 모듈 (structure-modules.md)에 속하는 경우에만 존재합니다.
* [컨트롤러 ID ((# controller-ids) : 동일한 응용 프로그램 (또는 컨트롤러가 모듈에 속하는 경우 동일한 모듈)에 속하는 모든 컨트롤러 중에서 컨트롤러를 고유하게 식별하는 문자열.
* 작업 ID (# action-ids) : 동일한 컨트롤러에 속하는 모든 행동 속에서 작업을 고유하게 식별하는 문자열.

루트는 다음의 형식을 취합니다.

```
ControllerID / ActionID
```

또는 컨트롤러가 모듈에 속하는 경우 다음의 형식을 취합니다.

```php
ModuleID / ControllerID / ActionID
```

따라서 사용자가`http : //hostname/index.php? r = site / index`라는 URL로 요청을 한 경우는`site` 컨트롤러 중`index` 액션이 실행됩니다.
루트가 어떻게 액션으로 해결되는지에 대한 자세한 내용은 다음 라우팅 및 URL 생성 (runtime-routing.md) 절을 참조하십시오.


## 컨트롤러를 만드는 <span id = "creating-controllers"> </ span>

[yii \ web \ Application | 웹 응용 프로그램]에서 컨트롤러는 [yii \ web \ Controller] 또는 자식 클래스에서 파생시켜야합니다.
마찬가지로 [yii \ console \ Application | 콘솔 응용 프로그램]에서 컨트롤러는 [yii \ console \ Controller] 또는 자식 클래스에서 파생시켜야합니다.
다음 코드는`site` 컨트롤러를 정의하는 것입니다.

```php
namespace app \ controllers;

use yii \ web \ Controller;

class SiteController extends Controller
{
}
```


### 컨트롤러 ID <span id = "controller-ids"> </ span>

컨트롤러는 일반적으로 특정 유형의 자원에 대한 요청을 처리하도록 설계됩니다.
이러한 이유로 대부분 처리하는 자원 유형을 나타내는 명사를 컨트롤러의 ID로 사용합니다.
예를 들어, 문서 데이터를 처리하는 컨트롤러의 ID로는`article`를 사용할 수 있습니다.

기본적으로 컨트롤러 ID는 소문자, 숫자, 밑줄, 대시 및 슬래시 만 포함해야합니다.
예를 들어,`article`와`post-comment`은 모두 유효한 컨트롤러 ID이지만`article?``PostComment``admin \ post`은 그렇지 않습니다.

컨트롤러 ID는 서브 디렉토리의 접두사를 포함해도 상관 없습니다.
예를 들어,`admin / article`은 [yii \ base \ Application :: controllerNamespace | 컨트롤러 네임 스페이스 아래의`admin` 하위 디렉토리에있는`article` 컨트롤러를 나타냅니다.
하위 디렉토리의 접두사로서 유효한 문자는 소문자 나 대문자, 숫자, 밑줄, 그리고 슬래시입니다.
슬래시는 여러 수준의 하위 디렉토리 구분 기호로 사용됩니다 (예를 들어,`panels / admin`).


### 컨트롤러 클래스의 명명 규칙 <span id = "controller-class-naming"> </ span>

컨트롤러 클래스의 이름은 다음과 같이 컨트롤러 ID에서 도출 할 수 있습니다.

1. 하이픈으로 구분 된 각 단어의 첫 글자를 대문자로 바꾼다.
   컨트롤러 ID가 슬래시를 포함하는 경우이 규칙은 ID의 마지막 슬래시 뒤에 부분에만 적용되는 것에주의.
2. 하이픈을 제거하고 슬래시를 모두 백 워드 슬래시로 대체한다.
3. 접미사`Controller`을 추가한다.
4. [yii \ base \ Application :: controllerNamespace | 컨트롤러 네임 스페이스를 머리에 붙인다.

다음은 [[yii \ base \ Application :: controllerNamespace | 컨트롤러 네임 스페이스]가 기본값`app \ controllers`를 가지고 있다고 가정했을 때의 몇 가지 예입니다.

*`article`는`app \ controllers \ ArticleController`된다.
*`post-comment`는`app \ controllers \ PostCommentController`된다.
*`admin / post-comment`는`app \ controllers \ admin \ PostCommentController`된다.
*`adminPanels / post-comment`는`app \ controllers \ adminPanels \ PostCommentController`된다.

컨트롤러 클래스는 자동로드 가능 (concept-autoloading.md)이어야합니다.
이러한 이유로 위의 예제의`aritcle` 컨트롤러 클래스는 별칭 (concept-aliases.md)가`@ app / controllers / ArticleController.php` 인 파일에 저장 될 것입니다.
한편`admin / post-comment` 컨트롤러는`@ app / controllers / admin / PostCommentController.php`라는 별칭 파일에 저장 될 것입니다.

> Info | 정보 : 마지막 예이다`admin / post-comment`은 어떻게 [yii \ base \ Application :: controllerNamespace | 컨트롤러 네임 스페이스]의 하위 디렉토리에 컨트롤러를 넣을 수 있는지를 보여 합니다.
  이 방법은 컨트롤러를 몇 가지 범주로 나누어 편성하고 싶은, 그러나 모듈 (structure-modules.md)는 사용하고 싶지 않은 경우 등에 유용합니다.


### 컨트롤러 맵 <span id = "controller-map"> </ span>

[yii \ base \ Application :: controllerMap | 컨트롤러 맵을 구성하면 위에서 말한 컨트롤러 ID와 클래스 이름의 제약을 극복 할 수 있습니다.
이것은 주로 클래스 이름에 대한 통제가 미치지 않는 타사의 컨트롤러를 사용하려는 경우에 유용합니다.

[yii \ base \ Application :: controllerMap | 컨트롤러 맵]은 응용 프로그램의 구성 정보 (structure-applications.md # application-configurations)에서 다음과 같이 구성 할 수 있습니다.

```php
[
    'controllerMap'=>
        // 클래스 이름을 사용하여 "account"컨트롤러를 선언
        'account'=> 'app \ controllers \ UserController'

        // 구성 정보 배열을 사용하여 "article"컨트롤러를 선언
        'article'=>
            'class'=> 'app \ controllers \ PostController'
            'enableCsrfValidation'=> false,
        ,
    ,
]
```


### 기본 컨트롤러 <span id = "default-controller"> </ span>

모든 응용 프로그램은 각각 [yii \ base \ Application :: defaultRoute] 속성에 의해 지정되는 기본 컨트롤러를 가지고 있습니다.
요청이 루트 (# routes)를 지정하지 않은 경우,이 속성에 의해 지정된 경로가 사용됩니다.
[yii \ web \ Application | 웹 응용 프로그램]에서이 값은` 'site'`이며, 한편, [yii \ console \ Application | 콘솔 응용 프로그램]에서`help`입니다.
따라서 URL이`http : // hostname / index.php` 인 경우는`site` 컨트롤러가 요청을 처리 할 수​​ 있습니다.

다음과 같이 응용 프로그램 구성 정보 (structure-applications.md # application-configurations)을 구성하여 기본 컨트롤러를 변경할 수 있습니다.

```php
[
    'defaultRoute'=> 'main'
]
```


## 액션을 만드는 <span id = "creating-actions"> </ span>

액션은 컨트롤러 클래스에서 이른바 * 액션 메소드 *을 정의하는 것만으로 간단하게 만들 수 있습니다.
액션 메소드는`action`로 시작하는 이름을 가진 * public * 메소드입니다.
액션 메소드의 반환 값은 최종 사용자에게 전송되는 응답 데이터를 나타냅니다.
다음의 코드는`index`와`hello-world`라는 두 작업을 정의하는 것입니다.

```php
namespace app \ controllers;

use yii \ web \ Controller;

class SiteController extends Controller
{
    public function actionIndex ()
    {
        return $ this-> render ( 'index');
    }

    public function actionHelloWorld ()
    {
        return 'Hello World';
    }
}
```


### 액션 ID <span id = "action-ids"> </ span>

액션은 대개 자원에 대해 특정 작업을 수행하도록 설계됩니다.
이러한 이유로 작업 ID는 일반적으로`view``update` 등과 같은 동사입니다.

기본적으로 액션 ID는 소문자, 숫자, 밑줄, 그리고 하이픈 만 포함해야합니다.
액션 ID 중 하이픈은 단어를 구분하기 위해 사용됩니다.
예를 들어,`view``update2``comment-post`는 모든 유효한 액션 ID이지만`view?``Update`은 그렇지 않습니다.

액션은 두 가지 방법, 즉 인라인 액션 또는 독립형 동작으로 만들 수 있습니다.
인라인 액션 컨트롤러 클래스의 메소드로 정의되는 것이며, 반면 독립형 동작은 [yii \ base \ Action] 또는 하위 클래스를 확장하는 클래스입니다.
인라인 액션은 만드는 더 적은 노력이 필요하기 때문에 일반적으로 액션을 재사용 할 의도가없는 경우에 권장됩니다.
다른 독립형 동작은 주로 다양한 컨트롤러에서 사용되는 것이나, 내선 (structure-extensions.md)으로 재배포하는 것을 목적으로 작성됩니다.


### 인라인 액션 <span id = "inline-actions"> </ span>

인라인 액션은 방금 설명했듯이, 액션 메소드의 형태로 정의하는 작업을 말합니다.

액션 메소드의 이름은 다음과 같이 작업 ID에서 파생됩니다.

1. 액션 ID에 포함 된 각 단어의 첫 글자를 대문자로 변환한다.
2. 하이픈을 제거한다.
3. 접두사`action`을 붙인다.

예를 들어,`index`는`actionIndex`되어`hello-world`는`actionHelloWorld`입니다.

> Note |주의 : 액션 메소드의 이름은 * 대소 문자를 구별 *합니다.
  `ActionIndex`라는 메소드가 있어도 그것은 액션 메소드로 간주되지 않고, 결과적으로`index` 액션에 대한 요청은 예외로 귀결됩니다.
  액션 메소드가 public이어야한다는 점에 유의하십시오.
  private이나 protected 메소드가 인라인 액션을 정의하는 것은 아닙니다.


인라인 액션은 창조하기 위하여 대부분 노력을 필요로하지 않는 때문에 대부분의 액션은 인라인 작업으로 정의됩니다.
그러나 동일한 작업을 다른 곳에서 재사용 할 계획을 가지고 있고, 또한 행동을 재배포하고 싶다고 생각하는 경우 조치를 * 독립형 동작 *로 정의하는 것을 고려해야한다.


### 독립형 액션 <span id = "standalone-actions"> </ span>

독립형 동작은 [yii \ base \ Action] 또는 하위 클래스를 확장하는 액션 클래스의 형태로 정의되는 것입니다.
예를 들어, Yii 릴리스에 [yii \ web \ ViewAction] 및 [yii \ web \ ErrorAction]]가 포함되어 있지만, 이들은 모두 독립형 동작입니다.

독립형 액션을 사용하기 위해서는 다음과 같이 컨트롤러 [yii \ base \ Controller :: actions ()] 메소드를 재정의하고 * 액션 맵 *에서 독립형 동작을 선언해야 안됩니다.

```php
public function actions ()
{
    return [
        // 클래스 이름을 사용하여 "error"액션을 선언
        'error'=> 'yii \ web \ ErrorAction'

        // 구성 정보 배열을 사용하여 "view"액션을 선언
        'view'=>
            'class'=> 'yii \ web \ ViewAction'
            'viewPrefix'=> '',
        ,
    ];
}
```

보시다시피,`actions ()`메소드는 키가 액션 ID이며 값이 해당 작업 클래스 이름 또는 구성 정보 (concept-configurations.md) 인 배열을 돌려주지 않으면 안됩니다 .
인라인 액션과 달리 독립형 액션의 액션 ID는`actions ()`메소드에 선언하는 범위 내에서 모든 문자를 포함 할 수 있습니다.

독립형 액션 클래스를 만들려면 [yii \ base \ Action] 또는 하위 클래스를 확장하여`run ()`라는 이름의 public 메소드를 구현해야합니다.
`run ()`메소드의 역할은 액션 메소드의 그것과 비슷한 것입니다. 예를 들어,

```php
<? php
namespace app \ components;

use yii \ base \ Action;

class HelloWorldAction extends Action
{
    public function run ()
    {
        return "Hello World";
    }
}
```


### 액션의 결과 <span id = "action-results"> </ span>

액션 메소드 또는 독립형 액션의`run ()`메소드의 반환 값은 중요한 의미를 갖습니다.
그것은 해당 작업의 결과를 나타내는 것입니다.

반환 값은 최종 사용자에게 응답으로 전송되는 [응답] (runtime-responses.md) 객체로 할 수 있습니다.

* [[yii \ web \ Application | 웹 응용 프로그램]에서 결과를 [yii \ web \ Response : data]]에 할당 된 임의의 데이터로 할 수도 있습니다. 이 데이터는 후에 응답의 본문을 나타내는 문자열로 변환됩니다.
* [[yii \ console \ Application | 콘솔 응용 프로그램]에서 반환 값을 명령 실행의 [yii \ console \ Response : exitStatus | 종료 상태를 나타내는 정수 할 수 있습니다.

지금까지의 예에서는 액션의 결과는 모든 문자열이며, 최종 사용자에게 전송되는 응답의 본문으로 다루어지는 것이 었습니다.
다음 예제에서는 액션이​​ 응답 객체를 반환하여 사용자의 브라우저를 새 URL로 리디렉션 할 수있는 모습이 나타나고 있습니다
([yii \ web \ Controller : redirect () | redirect ()] 메소드의 반환 값은 응답 객체입니다).

```php
public function actionForward ()
{
    // 사용자의 브라우저를 http://example.com로 리디렉션
    return $ this-> redirect ( 'http://example.com');
}
```


### 액션 매개 변수 <span id = "action-parameters"> </ span>

인라인 액션의 액션 메소드와 독립형 액션의`run ()`메쏘드는 * 액션 매개 변수 *라는 매개 변수를 취할 수 있습니다.
매개 변수의 값은 요청에서 검색됩니다.
[yii \ web \ Application | 웹 응용 프로그램]에서 각 작업 매개 변수의 값은`$ _GET`에서 매개 변수 이름을 키로 읽습니다.
[yii \ console \ Application | 콘솔 응용 프로그램]에서 액션 매개 변수는 명령 줄 인수에 대응합니다.

다음의 예에서는`view` 액션 (인라인 작업입니다)는 두 매개 변수, 즉`$ id`와`$ version`을 선언하고 있습니다.

```php
namespace app \ controllers;

use yii \ web \ Controller;

class PostController extends Controller
{
    public function actionView ($ id, $ version = null)
    {
        // ...
    }
}
```

액션 매개 변수는 다음과 같이 다양한 요청에 따라 다른 값이 투입됩니다.

*`http : //hostname/index.php? r = post / view & id = 123` :`$ id` 매개 변수는` '123'` 값이 포함됩니다.
  한편`version`라는 쿼리 매개 변수는 없기 때문에,`$ version`는 null대로됩니다.
*`http : //hostname/index.php? r = post / view & id = 123 & version = 2` :`$ id`와`$ version` 매개 변수에 각각` '123'`와`'2'`이 들어 합니다.
*`http : //hostname/index.php? r = post / view` : 필수`$ id` 매개 변수가 요청에 제공되지 않기 때문에 [yii \ web \ BadRequestHttpException] 예외가 발생합니다.
*`http : //hostname/index.php? r = post / view & id [] = 123` :`$ id` 매개 변수가 예기치 않은 배열 값`[ '123']`를 받으려고하기 때문에 [yii \ web \ BadRequestHttpException] 예외가 발생합니다.

액션 매개 변수에 배열 값을받지하려는 경우에는 다음과 같이 매개 변수에`array`의 형태 팁을 붙여야합니다.

```php
public function actionView (array $ id, $ version = null)
{
    // ...
}
```

이렇게하면 요청이`http : //hostname/index.php? r = post / view & id [] = 123` 인 경우는`$ id` 매개 변수는`[ '123']`라는 값을받습니다 합니다.
요청이`http : //hostname/index.php? r = post / view & id = 123` 일 수 스칼라 값` '123'`가 자동으로 배열로 변환되기 때문에`$ id` 매개 변수는 계속 같은 배열 값을받습니다.

위의 예는 주로 웹 애플리케이션에서 동작 파라미터의 동작을 보여줍니다.
콘솔 응용 프로그램은 콘솔 명령 (tutorial-console.md) 절에서 자세한 내용을 참조하십시오.


### 기본 액션 <span id = "default-action"> </ span>

모든 컨트롤러는 각각 [yii \ base \ Controller :: defaultAction]에 의해 지정되는 디폴트 액션을가집니다.
[루트] (# routes)가 컨트롤러 ID만을 포함하는 경우는 지정된 컨트롤러의 기본 액션이 요청 된 것을 의미합니다.

기본적으로 기본 액션은`index`로 설정됩니다.
이 기본값을 변경하려면 다음과 같이 컨트롤러 클래스에서이 속성을 재정의 할뿐입니다.

```php
namespace app \ controllers;

use yii \ web \ Controller;

class SiteController extends Controller
{
    public $ defaultAction = 'home';

    public function actionHome ()
    {
        return $ this-> render ( 'home');
    }
}
```


## 컨트롤러의 라이프 사이클 <span id = "controller-lifecycle"> </ span>

요청을 처리 할 때 응용 프로그램 (structure-applications.md)는 요청 된 루트 (# routes)을 기반으로 컨트롤러를 만듭니다.
그리고 다음 컨트롤러는 요청에 응하기 위하여 다음의 라이프 사이클을 경과합니다.

1. 컨트롤러가 만들어 구성된 후 [yii \ base \ Controller :: init ()]가 호출된다.
2. 컨트롤러는 요청 된 작업 ID를 기반으로 액션 객체를 생성한다.
   * 액션 ID가 지정되지 않은 경우에는 [yii \ base \ Controller :: defaultAction | 기본 작업 ID]]가 사용된다.
   * 작업 ID가 [yii \ base \ Controller :: actions () | 액션 맵] 중 발견 된 경우 독립형 동작이 생성된다.
   * 액션 ID에 일치하는 액션 메소드가 발견되면 인라인 액션이 생성된다.
   * 상기 이외의 경우 [yii \ base \ InvalidRouteException] 예외가 던져진다.
3. 컨트롤러는 애플리케이션 (컨트롤러 모듈에 속하는 경우) 모듈, 그리고 컨트롤러의`beforeAction ()`메소드를이 순서로 호출한다.
   * 어느 하나의 호출이 false를 반환하면 나머지 아직 알려져 있지 않다`beforeAction ()`메소드는 스킵되어 액션의 실행은 취소된다.
   * 기본적으로 각각의`beforeAction ()`메소드는 핸들러를 첨부 할 수있는`beforeAction` 이벤트를 트리거한다.
4. 컨트롤러가 작업을 수행한다.
   * 액션 매개 변수가 분석되고 요청 데이터에서 데이터가 투입된다.
5. 컨트롤러는 컨트롤러 (컨트롤러 모듈에 속하는 경우) 모듈, 그리고 응용 프로그램의`afterAction ()`메소드를이 순서로 호출한다.
   * 기본적으로 각각의`afterAction ()`메소드는 핸들러를 첨부 할 수있는`afterAction` 이벤트를 트리거한다.
6. 응용 프로그램 액션의 결과를 수신하여 [응답] (runtime-responses.md)에 할당한다.


## 모범 사례 <span id = "best-practices"> </ span>

잘 설계된 응용 프로그램에서는 컨트롤러는 대부분 매우 가벼운 것이되어, 각각의 액션은 몇 줄의 코드 밖에 포함하지 않는 것입니다.
당신의 컨트롤러가 조금 복잡하게되어있는 경우, 그 수는 일반적으로 컨트롤러를 리팩터링하여 코드의 일부를 다른 클래스로 이동해야 할 것을 나타냅니다.

몇 가지 모범 사례를 특히 들자면 컨트롤러는

* [요청] (runtime-requests.md) 데이터에 액세스 할 수 있습니다.
* 요청 데이터를 사용하여 모델 (structure-models.md) 및 기타 서비스 구성 요소의 메소드를 호출 할 수 있습니다.
* 뷰 (structure-views.md)를 사용하여 응답을 구성 할 수 있습니다.
* 요청 된 데이터의 처리를해야하지 않습니다 - 데이터 모델의 레이어 (structure-models.md)에서 처리되어야합니다.
* HTML을 삽입 등의 표시에 관한 코드는 피해야한다 - 표시는 뷰 (structure-views.md)에서 수행하는 것이 좋습니다.