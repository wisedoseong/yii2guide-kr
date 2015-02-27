양식(폼) 작업
==============

이 절에서는 사용자로부터 데이터를 취득하기위한 폼을 가진 새로운 페이지를 만드는 방법을 설명합니다.
이 페이지는 이름의 입력 필드와 메일 입력 필드를 가진 폼을 표시합니다.
사용자에서이 두 정보를받은 후 페이지는 입력 된 값을 확인하기 위해 에코 백합니다.

이 목적을 달성하기 위해 하나의 [작업](structure-controllers.md)과 두 [뷰](structure-views.md)를 작성하는 것 외에, 하나의 [모델](structure-models.md)도 만듭니다.

이 튜토리얼을 통해 다음과 같은 방법을 배웁니다.

* 양식을 통해 사용자에 의해 입력되는 데이터를 나타내는 [모델](structure-models.md)을 만드는 방법
* 입력 된 데이터를 검증하는 규칙을 선언하는 방법
* [뷰](structure-views.md)에서 HTML 양식을 만드는 방법


모델 만들기 <span id = "creating-model"></span>
-----------

사용자에게 입력 받는 데이터는 아래에 표시된대로`EntryForm` 모델 클래스로 표현 된`models / EntryForm.php`라는 파일에 저장됩니다.
클래스 파일의 명명 규약에 대한 자세한 내용은 [클래스의 오토로드](concept-autoloading.md) 절을 참조하십시오.

```php
<?php

namespace app\models;

use yii\base\Model;

class EntryForm extends Model
{
    public $name;
    public $email;

    public function rules()
    {
        return [
            [['name', 'email'], 'required'],
            ['email', 'email'],
        ];
    }
}
```

이 클래스는 Yii에서 제공하는 기본 클래스[[yii\base\Model]]을 확장하는 것입니다.
일반적으로 기본 클래스가 양식 데이터를 표현하는 데 사용됩니다.

> Info: [[yii\base\Model]] 는 데이터베이스 테이블과 관련 *not* 모델 클래스의 부모로서 사용됩니다.
데이터베이스 테이블과 해당 모델 클래스는 일반적으로 [[yii\db\ActiveRecord]]가 부모입니다.

`EntryForm` 클래스는 두 개의 공용 멤버`name`와`email`을 가지고 있으며, 이들이 사용자가 입력 한 데이터를 저장하는 데 사용됩니다.
이 클래스는 또한`rules ()`라는 메소드를 가지고 있습니다. 이 메소드가 데이터를 확인하는 일련의 규칙을 돌려줍니다.
위에서 선언 된 유효성 검사 규칙은 다음을 말합니다.

* `name`와`email`는 모두 값이 요구되는
* `email` 데이터는 구문 적으로 정당한 이메일 주소이어야한다

사용자가 입력 한 데이터를 `EntryForm` 객체에 투입 한 후 [[yii\base\Model::validate()|validate()]] 를 부르고 데이터 유효성 검사 루틴을 시작할 수 있습니다.
데이터 유효성 검사가 실패하면 [[yii\base\Model::hasErrors|hasErrors]] 속성이 true로 설정됩니다.
그리고 [[yii\base\Model::getErrors|errors]]을 통해 어떤 유효성 검사 오류가 발생했는지를 알 수 있습니다.


```php
<?php
$model = new EntryForm();
$model->name = 'Qiang';
$model->email = 'bad';
if ($model->validate()) {
    // 성공!
} else {
    // 실패!
    // $model->getErrors() 사용
}
```


액션 만들기<span id = "creating-action"></span>
-----------

다음은, 새로운 모델을 사용하여 `site` 컨트롤러에 `entry` 엑션을 만들어야 합니다.
액션을 작성하고 사용 하는 과정은 [Hello 출력하기](start-hello.md) 섹션에서 이미 설명되어 있습니다.

```php
<?php

namespace app\controllers;

use Yii;
use yii\web\Controller;
use app\models\EntryForm;

class SiteController extends Controller
{
    // ... 기존 코드 ...

    public function actionEntry()
    {
        $model = new EntryForm();

        if ($model->load(Yii::$app->request->post()) && $model->validate()) {
            // $model에 유효한 데이터를받은 경우

            // 여기서 $model 대한 다른 액션을 취하는 경우...

            return $this->render('entry-confirm', ['model' => $model]);
        } else {
            // 페이지의 초기 표시냐, 뭔가 검증 오류가 있는 경우
            return $this->render('entry', ['model' => $model]);
        }
    }
}
```

작업은 처음에 `EntryForm` 개체를 생성 합니다.
그런 다음 모델에 '$ _POST' 데이터 Yii은 [[yii\web\Request::post()]]에 의해 제공 되는 데이터를 입력 하려고 시도 합니다.
모델에 데이터를 입력에 성공 하면 (즉, 사용자가 HTML 폼을 보낼 때), 액션은 [[yii\base\Model::validate()|validate()]]을 부르고, 입력 된 값이 유효한 지 여부를 확인 합니다.

> Info: `Yii::$app` 라는 식은 [응용 프로그램](structure-applications.md) 인스턴스를 표현 합니다.
이는 전역으로 액세스할 수 있는 단일 항목입니다.
이것은 또한 특정 한 기능을 지 원하는  `request`, `response`, `db`, etc 같은 구성 요소를 제공 하는 [서비스 로케이터](concept-service-locator.md)도 있습니다.
위의 코드에서는 응용 프로그램 인스턴스가 `request` 구성 요소 `$_POST` 데이터에 액세스 하는 데 사용 됩니다.

모든 것이 적정 하다 면, 작업은 `entry-confirm` 라는 보기를 표시 하 고 데이터 전송 성공 여부를 확인 합니다.
데이터가 전송 되지 않으며, 데이터의 오류를 포함 하는 경우 `entry` 뷰가 표시 되 고 해당 HTML 양식을 (만약 점검해 서 한시적으로 경우) 유효성 검사 오류 메시지와 함께 표시 됩니다.

> Note:이 예제에서 올바른 데이터 전송에 대해서 간단하 게 확인 페이지를 표시 합니다.
실제 업무에서 [폼전송의 문제](http://en.wikipedia.org/wiki/Post/Redirect/Get)을 피하기 위해 [[yii\web\Controller::refresh()|refresh()]] 또는 [[yii\web\Controller::redirect()|redirect()]]를 사용 하는 것을 고려해 야 합니다.


뷰 만들기 <span id = "creating-views"> </span>
----------

마지막으로,  `entry-confirm` 과 `entry`. 라는 두 가지 뷰 파일을 만듭니다.
지금 바로 설명한 것으로, 이들이 `entry` 작업으로 표시 됩니다.

`entry-confirm` 보기는 이름과 이메일 데이터를 표시 합니다. 이 뷰는 `views/site/entry-confirm.php` 파일에 저장 해야 합니다.

'''php
<?php
use yii\helpers\Html;
?>
<p>You have entered the following information:</p>

<ul>
    <li><label>Name</label>: <?= Html::encode($model->name) ?></li>
    <li><label>Email</label>: <?= Html::encode($model->email) ?></li>
</ul>
```

`entry`뷰는 HTML 폼을 표시 합니다. 이것은 `views/site/entry.php` 파일에 저장 해야 합니다.

'''php
<?php
use yii\helpers\Html;
use yii\widgets\ActiveForm;
?>
<?php $form = ActiveForm::begin(); ?>

    <?= $form->field($model, 'name') ?>

    <?= $form->field($model, 'email') ?>

    <div class="form-group">
        <?= Html::submitButton('Submit', ['class' => 'btn btn-primary']) ?>
    </div>

<?php ActiveForm::end(); ?>
```

이 보기는 HTML 폼을 생성 하기 위하여, [[yii\widgets\ActiveForm|ActiveForm]] 라는 강력한 [위젯](structure-widgets.md)를 사용 합니다.
위젯 `begin()`와 `end()` 메서드는 각각 폼 태그와 닫는 태그를 렌더링 합니다.
이 두 메서드를 호출 하는 동안, [[yii\widgets\ActiveForm::field()|field()]]  메서드에 의해 입력 필드가 생성 됩니다.
첫 번째 입력 필드는 "name" 데이터, 두 번째 입력 필드는 "email" 데이터를 위한 것입니다.
입력 필드에 [[yii\helpers\Html::submitButton()]]가 불러, 보내기 버튼을 생성 합니다.



실행 <span id = "trying-it-out"> </span>
----------

어떻게 작동 하는지 보기 위해 브라우저에서 다음 URL을 입력 하세요.

```
http://hostname/index.php?r=site/entry
```

두 입력 필드를 가진 폼을 표시하는 페이지가 나타날 것입니다.

각 각 의 입력 필드 앞에는 어떤 데이터를 입력 해야 하는지 나타내는 레이블이 있습니다.
아무것도 입력 하지 않고, 또는 잘못 된 메일 주소를 입력 하 고 전송 단추를 클릭 하면 각 문제점에 있는 입력 필드 뒤에 오류 메시지가 표시 됩니다.

![유효성 검사 오류가 있는 폼](images/start-form-validation.png)

올바른 이름 및 이메일 주소를 입력 하 고 보내기 단추를 클릭 하면 지금 입력 한 데이터를 표시 하는 새 페이지가 표시 됩니다.

![데이터 입력 확인](images/start-entry-confirmation.png)


### 마법같은 설명 <span id = "magic-explained"> </span>

당신은 화면에서 HTML 양식이 어떻게 움직이고 있는지 궁금해 할것입니다.
왜냐하면 양식이 거의 마술 같이, 각 입력 필드의 레이블을 표시 하 고 데이터를 올바르게 입력 하지 않은 경우에는 페이지를 다시 로드 하지 않고 오류 메시지를 표시 하기 때문입니다.

이렇게 데이터의 유효성은 JavaScript를 사용 하 여 클라이언트에서 실행 되 고 다음에 PHP에 의해 서버에서 실행 됩니다.
[[yii\widgets\ActiveForm]]는 총 명 한 것으로, `EntryForm` 에서 선언 된 유효성 검사 규칙을 추출 하 고 그것을 실행 가능한 JavaScript 코드로 변환 하 여 JavaScript를 사용 하 여 데이터 유효성 검사를 수행 합니다.
브라우저에서 JavaScript를 비활성화 한 경우에도 `actionEntry()` 에서 설명한 것 처럼 서버 쪽 유효성 검사가 계속 실행 됩니다.
이렇게 하면 어떤 상황 에서도 데이터의 유효성을 보장 합니다.

> Warning | 경고: 클라이언트측 유효성 검사는 사용자에 게 더 좋은 유용성을 위해 편의를 제공 합니다.
클라이언트측 유효성 검사의 유무에 불구 하 고, 서버 측 유효성 검사는 항상 필요 합니다.

입력 필드의 레이블은 모델에서 속성 이름을 사용 하 여 `field()`  메서드에 의해 생성 됩니다.
예를 들면, `Name` 속성에서 `Name` 레이블이 생성 됩니다.

보기에서 다음 코드와 같이 레이블을 지정할 수 있습니다.

```php
<?= $form->field($model, 'name')->label('Your Name') ?>
<?= $form->field($model, 'email')->label('Your Email') ?>
```

> Info | 정보: Yii 이런 위젯을 제공 하 여 복잡 한 동적 뷰를 신속하게 만들도록 도와 줍니다.
나중에 배운 대로, 새로운 위젯을 쓰는 것도 매우 쉽습니다.
나중에 개발을 단순화 하기 위해 많은 뷰코드를 위젯으로 변환 하고 싶을 것입니다.


정리 <span id = "summary"></span>
------

이번 설명 에서는  MVC 디자인 패턴의 모든 부분에 대해서 다루었습니다.
그리고, 사용자 데이터를 표시 하 고 데이터 유효성 검사 모델 클래스를 만드는 방법을 배웠습니다.

또한 사용자로 부터 데이터를 검색 하고 브라우저에 데이터를 표시 하여 출력 하는 방법을 배웠습니다.
이 작업은 응용 프로그램을 개발 하는 데 상당한 시간을 필요로 하는 것들입니다.
그러나 Yii은 이러한 작업을 아주 쉽게 하는 강력한 위젯을 제공 하 고 있습니다.

다음 절에서는 대부분의 응용 프로그램에 필요한 데이터베이스를 처리 하는 방법을 배웁니다.