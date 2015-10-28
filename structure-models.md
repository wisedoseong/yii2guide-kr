모델
======

모델은 [MVC] (http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 아키텍처의 일부를 이루는 것입니다.
이것은 비즈니스 데이터 규칙 로직을 표현하는 객체입니다.

모델 클래스는 [[yii\base\Model]] 또는 하위 클래스를 확장하여 만들 수 있습니다.
기본 클래스 [[yii\base\Model]] 는 다음과 같은 많은 유용한 기능을 지원합니다.

* [속성](#attributes) : 비즈니스 데이터를 표현합니다. 일반 개체 속성이나 배열 요소처럼 액세스 할 수 있습니다.
* [특성 레이블] (#attribute-labels) : 속성의 표시 레이블을 지정합니다.
* [대규모 할당] (#massive-assignment) : 한번의 단계에서 여러 속성에 데이터를 투입하는 것을 지원하고 있습니다.
* [유효성 검사 규칙](#validation-rules) : 선언 된 검증 규칙에 따라 입력 된 데이터의 유효성을 보장합니다.
* [데이터 내보내기] (# data-exporting) : 사용자 정의 형식으로 모델 데이터를 배열로 내보낼 수 있습니다.

`Model` 클래스 역시 [액티브 레코드](db-active-record.md)와 같은 고급 모델의 기본 클래스 입니다.
이러한 고급 모델에 대한 자세한 내용은 관련 문서를 참조하십시오.

> Info | 정보 : 당신의 모델 클래스의 base class로서 [[yii\base\Model]]을 기반으로 할 필요는 없습니다.
  그러나 Yii의 많은 구성 요소가  [[yii\base\Model]] 을 지원하도록 만들어져 있기 때문에, 일반적으로  [[yii\base\Model]]이 모델의 base class로서 권장됩니다.


## 속성 <span id="attributes"></span>

모델은 비즈니스 데이터를 *속성* 형식으로 표현합니다.
모든 속성은 각각 공개적으로 액세스 가능한 모델의 속성과 동일한 것입니다.
 [[yii\base\Model::attributes()]] 메소드가 모델이 어떤 속성을 가지고 있는지를 지정합니다.

속성에 대해서는 일반 개체 속성에 액세스하는 것과 같은 방법으로 액세스 할 수 있습니다.

```php
$model = new \app\models\ContactForm;

// "name" is an attribute of ContactForm
$model->name = 'example';
echo $model->name;
```

또한 배열의 요소에 액세스하도록하여 속성에 액세스 할 수 있습니다.
이것은 [[yii\base\Model]]가 [ArrayAccess](http://php.net/manual/en/class.arrayaccess.php) 및 ArrayIterator 클래스 (http://php.net/manual/en/class.arrayiterator.php)를 지원합니다.

```php
$model = new \app\models\ContactForm;

// accessing attributes like array elements
$model['name'] = 'example';
echo $model['name'];

// iterate attributes
foreach ($model as $name => $value) {
    echo "$name: $value\n";
}
```


### 속성 정의 <span id="defining-attributes"></span>

기본적으로 모델 클래스에서 [[yii\base\Model]] 을 직접 확장하는경우 모든 *non-static public* 인 멤버 변수는 속성입니다.
예를 들어, 다음 `ContactForm` 모델은 네 가지 속성, 즉 `name`, `email`,`subject` 과 `body` 를가집니다.
이 `ContactForm` 모델은 HTML 폼에서받은 입력 데이터를 표현하기 위해 사용됩니다.

```php
namespace app\models;

use yii\base\Model;

class ContactForm extends Model
{
    public $name;
    public $email;
    public $subject;
    public $body;
}
```


[[yii\base\Model::attributes()]]를 재정의하고 속성을 다르게 정의 할 수 있습니다.
이 메소드는 모델이 가지는 속성의 이름을 리턴하지 않으면 안됩니다.
예를 들어,  [[yii\db\ActiveRecord]] 은 연결된 데이터베이스 테이블의 열 이름을 속성 이름으로 반환하여 속성을 정의하고 있습니다.
그러나 이와 동시에 정의 된 속성에 대해 일반 개체 속성처럼 액세스 할 수 있도록 `__get()`, `__set()`등의 매직 메소드를 오버라이드 (override) 할 필요가 있을지도 모르니 주의 해야 합니다.


### 속성 레이블 <span id = "attribute-labels"> </ span>

속성 값을 표시하거나 입력 해달라고 할 때 속성과 연관된 레이블을 표시 할 필요가있는 경우가 종종 있습니다.
예를 들어, `firstName` 라는 속성을 생각했을 때, 입력 양식이나 오류 메시지 같은 곳에서 최종 사용자에게 표시 할 때는 더 사용자가 익숙한 `First Name` 레이블을 표시하고 싶을 것입니다.

[[yii\base\Model::getAttributeLabel()]] 를 호출하여 속성의 레이블을 얻을 수 있습니다. 예를 들어,

```php
$model = new \app\models\ContactForm;

// displays "Name"
echo $model->getAttributeLabel('name');
```

기본적으로 속성 레이블 속성 이름에서 자동으로 생성됩니다.
라벨의 생성은 [[yii\base\Model::generateAttributeLabel()]]라는 메소드에 의해 이루어집니다.
이 방법은 카멜 케이스의 변수 이름을 여러 단어로 구분하여 각 단어의 첫 글자를 대문자로합니다.
예를 들어,`username` 는 `Username` 되고, `firstName` 는 `First Name` 입니다.

자동으로 생성 된 라벨을 사용하지 않으려면  [[yii\base\Model::attributeLabels()]] 를 재정의하고 속성의 레이블을 명시 적으로 선언 할 수 있습니다. 예를 들어,

```php
namespace app\models;

use yii\base\Model;

class ContactForm extends Model
{
    public $name;
    public $email;
    public $subject;
    public $body;

    public function attributeLabels()
    {
        return [
            'name' => 'Your name',
            'email' => 'Your email address',
            'subject' => 'Subject',
            'body' => 'Content',
        ];
    }
}
```

여러 언어를 지원하는 응용 프로그램에서는 속성의 레이블을 번역하고 싶을 것입니다.
이것도 다음과 같이 [[yii\base\Model::attributeLabels()|attributeLabels()]]에서 할 수 있습니다.

```php
public function attributeLabels()
{
    return [
        'name' => \Yii::t('app', 'Your name'),
        'email' => \Yii::t('app', 'Your email address'),
        'subject' => \Yii::t('app', 'Subject'),
        'body' => \Yii::t('app', 'Content'),
    ];
}
```

```

조건에 따라 속성의 레이블을 정의 할 수도 있습니다.
예를 들어, 모델이 사용된다 [시나리오](#scenarios)에 따라 동일한 속성에 대해 다른 레이블을 반환 할 수 있습니다.

> Info | 정보 : 엄밀히 말하면 속성의 레이블은 [view](structure-views.md)의 일부를 이루는 것입니다.
  그러나 대부분의 경우 모델에서 레이블을 선언하는 것이 더 편리가 좋고, 결과적으로 깨끗하고 재사용 가능한 코드입니다.


## 시나리오 <span id="scenarios"></span>

모델은 여러 다른 *시나리오* 에서 사용됩니다.
예를 들어, `User` 모델은 사용자 로그인의 입력을 수집하는 데 사용되지만 동시에 사용자 등록의 목적으로도 사용됩니다.
다른 시나리오에서 모델이 사용하는 비즈니스 규칙과 논리도 다른 것이 될 수 있습니다.
예를 들어, `email` 속성은 사용자 등록시 필수 될지도 모릅니다 만, 로그인시에는 그렇지는 않을 것입니다.

모델은 [[yii\base\Model::scenario]] 속성을 사용하여 자신이 사용하는 시나리오를 추적합니다.
기본적으로 모델은 `default` 라는 하나의 시나리오 만 지원합니다.
다음 코드는 모델의 시나리오를 설정하는 두 가지 방법을 보여줍니다.

```php
// 시나리오를 속성으로 설정
$model = new User;
$model->scenario = User::SCENARIO_LOGIN;

// 시나리오 설정 정보를 설정
$model = new User(['scenario' => User::SCENARIO_LOGIN]);
```


기본적으로 모델에 따라 지원되는 시나리오는 모델에서 선언 된 [유효성 검사 규칙](#validation-rules)에 의해 결정됩니다.
그러나 다음과 같이 [[yii\base\Model::scenarios()]] 메소드를 재정의하고, 이 동작을 사용자가 정의 할 수 있습니다.

```php
namespace app\models;

use yii\db\ActiveRecord;

class User extends ActiveRecord
{
    const SCENARIO_LOGIN = 'login';
    const SCENARIO_REGISTER = 'register';

    public function scenarios()
    {
        return [
            self::SCENARIO_LOGIN => ['username', 'password'],
            self::SCENARIO_REGISTER => ['username', 'email', 'password'],
        ];
    }
}
```


> Info | 정보 : 위의 예제와 후속 예제에서는 모델 클래스는 [[yii\db\ActiveRecord]] 를 확장하는 것으로되어 있습니다.
  이유는 여러 시나리오를 사용하는 것은 일반적으로 엑티브 레코드 (db-active-record.md) 클래스에서 발생하기 때문입니다.

`scenarios()` 메소드는 키가 시나리오의 이름이고 값이 해당 *엑티브 속성* 인 배열을 반환합니다.
엑티브 속성은 [대규모 할당](#massive-assignment) 할 수 있으며, [validation](#validation-rules)의 대상이되는 속성입니다.
위의 예에서는 `login` 시나리오에서는 `username` 과 `password` 의 속성이 엑티브 상태이며 반면 `register` 시나리오에서는`username` 과 `password` 이외에`email` 도 활성화됩니다 .

`scenarios()` 의 기본 구현은 유효성 검사 규칙의 선언 방법입니다. [[yii\base\Model::rules()]]에 나오는 모든 시나리오를 반환합니다.
`scenarios()` 를 재정의 할 때 기본 시나리오 이외에 새로운 시나리오를 도입하려면 다음과 같은 코드를 작성합니다.

```php
namespace app\models;

use yii\db\ActiveRecord;

class User extends ActiveRecord
{
    const SCENARIO_LOGIN = 'login';
    const SCENARIO_REGISTER = 'register';

    public function scenarios()
    {
        $scenarios = parent::scenarios();
        $scenarios[self::SCENARIO_LOGIN] = ['username', 'password'];
        $scenarios[self::SCENARIO_REGISTER] = ['username', 'email', 'password'];
        return $scenarios;
    }
}
```

시나리오의 기능은 주로 [유효성 검사](#validation-rules)와 속성의 일괄 대입(#massive-assignment)에 의해 사용됩니다.
하지만 다른 목적에 사용할 수 있습니다. 예를 들어, 현재의 시나리오에 따라 다른 특성 레이블 (#attribute-labels)을 선언 할 수도 있습니다.


## 유효성 검사 규칙 <span id="validation-rules"></span>

모델 데이터를 최종 사용자로부터 받았을 때에 데이터를 검증하고 그것이 일정한 규칙 (* 증 규칙* 또는 소위 *비즈니스 규칙*)을 충족하는지 확인해야합니다 .
`ContactForm` 모델을 예로 든다면, 모든 속성이 비어 있지 않고 `email` 속성이 유효한 이메일 주소를 포함하는지 확인할수 있습니다.
하나의 속성 값이 대응하는 비즈니스 규칙을 충족하지 않는 경우, 사용자가 오류를 정정 할 것을 도울 적절한 오류 메시지가 표시되어야합니다.

수신 한 데이터를 검증하기 위해 [[yii\base\Model::validate()]] 을 호출 할 수 있습니다.
이 방법은 [[yii\base\Model::rules()]] 에서 선언 된 유효성 검사 규칙을 사용하여 해당하는 모든 속성을 확인합니다.
오류가 발견되지 않으면 메소드는 true를 돌려줍니다.
그렇지 않으면, [[yii\base\Model::errors]] 오류를 저장하고 false를 반환합니다.
예를 들어,

```php
$model = new \app\models\ContactForm;

// populate model attributes with user inputs
$model->attributes = \Yii::$app->request->post('ContactForm');

if ($model->validate()) {
    // all inputs are valid
} else {
    // validation failed: $errors is an array containing error messages
    $errors = $model->errors;
}
```


모델에 관련된 유효성 검사 규칙을 선언하기 위해서는  [[yii\base\Model::rules()]] 메소드를 오버라이드하여 모델의 특성을 충족 할 규칙을 반환합니다.
다음의 예는 `ContactForm` 모델에 선언 된 유효성 검사 규칙을 나타냅니다.

```php
public function rules()
{
    return [
        // the name, email, subject and body attributes are required
        [['name', 'email', 'subject', 'body'], 'required'],

        // the email attribute should be a valid email address
        ['email', 'email'],
    ];
}
```

하나의 규칙은 하나 이상의 속성을 확인하는 데 사용할 수 있습니다.
또한 하나의 속성은 하나 이상의 규칙으로 검증 할 수 있습니다.
검증 규칙을 어떻게 선언하는지에 대한 자세한 내용은 입력 [유효성 확인](input-validation.md) 절을 참조하십시오.

어떠한 경우에는, 특정의 [시나리오](#scenarios)에만 적용되는 규칙이 필요할 것입니다.
그러기 위해서는, 아래와 같이 규칙에 `on` 속성을 지정할 수 있습니다.

```php
public function rules()
{
    return [
        // username, email and password are all required in "register" scenario
        [['username', 'email', 'password'], 'required', 'on' => self::SCENARIO_REGISTER],

        // username and password are required in "login" scenario
        [['username', 'password'], 'required', 'on' => self::SCENARIO_LOGIN],
    ];
}
```

`on` 속성을 지정하지 않으면 그 규칙은 모든 시나리오에 적용되는 것입니다.
현재[[yii\base\Model::scenario|scenario]] 에 적용 가능한 규칙 *엑티브 규칙* 이라고 합니다.

속성이 검증되는 것은 그것이 `scenarios()` 의 엑티브 속성 으로 선언되고 또한 그 속성이 `rules()` 에서 선언 된 하나 이상의 엑티브 규칙과 연결된 경우 입니다.


## 일괄 적용 <span id = "massive-assignment"> </ span>

일괄 적용은 한행의 코드를 쓰는 것만으로 사용자가 입력 한 여러 데이터를 모델에 투입 할 수있는 편리한 방법입니다.
일괄 적용은 입력 된 데이터를[[yii\base\Model::$attributes]] 속성에 직접 대입하여 모델의 속성에 데이터를 등록합니다.
다음 두 코드 조각은 동일하며 모두 최종 사용자로부터 전송 된 폼 데이터를 `ContactForm` 모델의 속성에 할당 할 것입니다.
물론, 일괄 적용을 사용 하는것이 전자가 후자보다 명쾌하고 실수하기도 적고 유용한 방법입니다.

```php
$model = new \app\models\ContactForm;
$model->attributes = \Yii::$app->request->post('ContactForm');
```

```php
$model = new \app\models\ContactForm;
$data = \Yii::$app->request->post('ContactForm', []);
$model->name = isset($data['name']) ? $data['name'] : null;
$model->email = isset($data['email']) ? $data['email'] : null;
$model->subject = isset($data['subject']) ? $data['subject'] : null;
$model->body = isset($data['body']) ? $data['body'] : null;
```


### 안전 속성 <span id="safe-attributes"></span>

일괄 적용 소위 *안전 속성* 즉, [[yii\base\Model::scenarios()]] 에서 모델의 현재[[yii\base\Model::scenario|scenario]] 에 대한 목록 되는 속성에만 적용됩니다.
예를 들어, `User` 모델이 다음과 같은 시나리오 선언을 가지고있는 경우, 현재의 시나리오가 `login` 인 경우는` username` 과 `password` 만 일괄 지정이 가능합니다.
기타 속성은 일체 언급하지 않습니다.

```php
public function scenarios()
{
    return [
        self::SCENARIO_LOGIN => ['username', 'password'],
        self::SCENARIO_REGISTER => ['username', 'email', 'password'],
    ];
}
```

> Info | 정보 : 일괄 적용이 안전 속성에만 적용되는 것은 최종 사용자의 입력 데이터가 어떤 속성을 수정할 수있는 것인가하는 것을 제어 할 필요가 있기 때문입니다.
  예를 들어, `User` 모델의 사용자에게 할당 된 권한을 결정하는 `permission` 라는 속성이있는 경우이 속성을 수정할 수있는 관리자가 백엔드 인터페이스를 통해 할 때만 제한 할 것입니다 .

[[yii\base\Model::scenarios()]] 의 기본 구현은 [[yii\base\Model::rules()]] 에 나타나는 모든 시나리오와 속성을 반환합니다.
따라서,이 메서드를 재정의하지 않으면 엑티브 유효성 검사 규칙 중 하나에 출현하는 한, 그 속성은 안전하다는 것입니다.

따라서 실제로 확인하지 않고 특성을 안전하다고 선언 할 수 있도록 `safe` 이라는 별칭을 주어진 특별한 안전 데이터가 제공되고 있습니다.
예를 들어, 다음 규칙은 `title` 과 `description` 모두가 안전한 속성임을 선언하고 있습니다.

```php
public function rules()
{
    return [
        [['title', 'description'], 'safe'],
    ];
}
```


### 안전하지 않은 속성 <span id="unsafe-attributes"></span>

위에서 설명한 바와 같이, [[yii\base\Model::scenarios()]] 방법은 두 가지 목적을 가지고 있습니다.
즉, 어떤 특성이 검증되어야 할 것인가를 결정하는 것과 어떤 속성이 안전한지 여부를 결정할 것입니다.
드문 경우로, 속성을 검증 할 필요가 있지만, 안전하다는 표시는 지정 싶지 않다는 수 있습니다.
그럴 때는 아래의 예 `secret` 속성처럼 이름 앞에 느낌표 `!` 로 `scenarios()` 에서 선언 할 수 있습니다.

```php
public function scenarios()
{
    return [
        self::SCENARIO_LOGIN => ['username', 'password', '!secret'],
    ];
}
```

이 모델이 `login` 시나리오의 경우 세 가지 특성은 모두 확인됩니다.
그러나 `username` 과 `password` 의 특성들만 대량으로 할당 할 수 있습니다.
`secret` 속성에 입력 값을 할당하기 위해서는 아래와 같이 명시 적으로 할당을 수행해야합니다.

```php
$model->secret = $secret;
```


## 데이터 내보내기 <span id = "data-exporting"> </ span>

모델을 다른 형식으로 내보내기해야 할 때가 많습니다.
예를 들어, 모델의 컬렉션을 JSON 또는 Excel 형식으로 변환하고 싶은 경우가있을 것입니다.
내보내기 프로세스는 두 개의 독립적 인 단계로 나눌 수 있습니다.
첫 번째 단계에서 모델은 배열로 변환됩니다.
그리고 두 번째 단계에서 배열이 원하는 형식으로 변환됩니다.
당신은 첫 번째 단계에만 주력 할 수 있습니다.
이란은 두 번째 단계는 일반적인 데이터 포맷, 예를 들어 [[yii\web\JsonResponseFormatter]]에 의해 달성 할 수 있기 때문입니다.

모델을 배열로 변환하는 가장 쉬운 방법은 [[yii\base\Model::$attributes]]속성을 사용하는 것입니다.
예를 들어,

```php
$post = \app\models\Post::findOne(100);
$array = $post->attributes;
```

기본적으로 [[yii\base\Model::$attributes]] 속성은 [[yii\base\Model::attributes()]] 에서 선언 된 *모든* 속성 값을 반환합니다.

모델을 배열로 변환하기 위해 더 유연하고 강력한 방법은 [[yii\base\Model::toArray()]] 메소드를 사용하는 것입니다.
이 메소드의 디폴트의 동작은 [[yii\base\Model::$attributes]]와 동일합니다.
그러나이 방법을 사용하면 어떤 데이터 항목 (*필드*라고합니다)을 결과의 배열에 넣거나 그리고 그 항목에 어떤 서식을 적용할지 여부를 선택할 수 있습니다.
실제로, [응답 형식의 설정](rest-response-formatting.md)에서 설명 된 바와 같이 RESTful 웹 서비스 개발에서는이 모델을 내보내는 기본 방법이 있습니다.


### 필드 <span id="fields"></span>

필드는 단순히 모델 [[yii\base\Model::toArray()]] 메소드를 호출하는 것에 의해 취득되는 배열에 포함되는 명명 된 요소입니다.

기본적으로 필드의 이름은 속성 이름과 같아야합니다.
하지만이 기본 동작은 [[yii\base\Model::fields()|fields()]] 및 / 또는 [[yii\base\Model::extraFields()|extraFields()]]메소드를 오버라이드 (override) 하여 변경할 수 있습니다.
두 메소드 모두 필드 정의의리스트를 돌려줍니다.
`fields()` 에 의해 정의되는 필드는 기본 필드입니다. 즉 `toArray()` 는 기본적으로 이러한 필드를 반환한다는 것을 의미합니다.
`extraFields()` 메소드는 `$expand` 매개 변수로 지정하는 한 `toArray()`에 의해 반환되는 추가 필드를 정의하는 것입니다.
예를 들어, 다음 코드는 `fields()` 에서 정의 된 모든 필드와 (`extraFields()` 로 정의 된 경우) `prettyName` 과 `fullAddress` 필드를 반환합니다.

```php
$array = $model->toArray([], ['prettyName', 'fullAddress']);
```

`fields()` 를 무시하고 필드를 추가, 삭제, 이름 변경, 다시 정의 할 수 있습니다.
`fields()` 의 반환 값은 배열이 아니면 안됩니다. 배열의 키는 필드 이름이며, 배열의 값은 해당 필드 정의입니다.
필드의 정의는 속성 / 속성 이름 또는 해당 필드의 값을 반환 익명 함수를 사용할 수 있습니다.
필드 이름이 그것을 정의하는 속성 이름과 동일하다는 특수한 경우에는 배열의 키를 생략 할 수 있습니다.
예를 들어,

```php
// 명시 적으로 모든 필드를 나열하는 방법. (API의 호환성을 유지하기 위해) DB 테이블 또는
// 모델 특성의 변경이 필드의 변경을 일으키지 않도록하려는 경우에 적합하다.
public function fields()
{
    return [
        // field name is the same as the attribute name
        'id',

        // field name is "email", the corresponding attribute name is "email_address"
        'email' => 'email_address',

        // field name is "name", its value is defined by a PHP callback
        'name' => function () {
            return $this->first_name . ' ' . $this->last_name;
        },
    ];
}

// filter out some fields, best used when you want to inherit the parent implementation
// and blacklist some sensitive fields.
public function fields()
{
    $fields = parent::fields();

    // remove fields that contain sensitive information
    unset($fields['auth_key'], $fields['password_hash'], $fields['password_reset_token']);

    return $fields;
}
```

> Warning | 경고 : 기본적으로 모델의 모든 속성이 수출되는 배열에 포함되므로 데이터를 조사하여 공개해야 할 정보가 포함되어 있지 않은지 확인해야합니다.
> 그런 정보가 있다면,`fields )` 를 무시하고 제외해야합니다.
> 위의 예에서는 `auth_key`, `password_hash` 과 `password_reset_token`을 제외하고 있습니다.


## 모범 사례 <span id="best-practices"></span>

모델은 비즈니스 데이터 규칙 로직을 나타내는 핵심 개체입니다.
모델은 대부분 다양한 장소에서 재사용 될 필요가 있습니다.
잘 설계된 응용 프로그램은 일반적으로 모델은 [컨트롤러](structure-controllers.md)보다 훨씬 무거울 것입니다.

요약하면, 모델은

* 비즈니스 데이터를 표현하는 속성을 포함 할 수 있습니다.
* 데이터의 유효성과 무결성을 보장하는 유효성 검사 규칙을 포함 할 수 있습니다.
* 비즈니스 로직을 구현하는 방법을 포함 할 수 있습니다.
* 요청, 세션, 또는 다른 환경 데이터에 직접 액세스해야하는 것이 아닙니다.
  이러한 데이터는 [컨트롤러](structure-controllers.md)에 의해 모델에 주입되어야합니다.
* HTML을 삽입 등의 표시 용 코드는 피해야한다 - 표시는 뷰 (structure-views.md)에서 수행하는 것이 좋습니다.
* 너무 많은 [시나리오](#scenarios)를 하나의 모델로 갖는 것은 피합시다.

크고 복잡한 시스템을 개발하는 경우에는 대개 위의 마지막에 올린 권장 사항을 고려하는 것이 좋습니다.
그러한 시스템에서는 모델은 많은 장소에서 사용되며 그에 따라 수많은 규칙 세트와 비즈니스 로직을 포함하기 때문에 아주 무겁게 될 수 있습니다.
코드의 한 곳을 누르면 몇 군데의 다른 위치에 영향을 미치기 때문에 결국은 모델 코드의 유지 보수 악몽이되어 버리는 일도 자주 있습니다.
모델 코드의 보수성을 높이기 위해서는 다음의 전략을 취할 수 있습니다.

* 다른 [응용 프로그램](structure-applications.md) 또는 [모듈](structure-modules.md)에 의해 공유되는 일련의 기본 모델 클래스를 정의합니다.
  이러한 모델 클래스는 모두 공통으로 사용되는 최소한의 규칙 세트와 논리만을 포함한다.
* 모델을 사용하는 각각의 응용 프로그램 (structure-applications.md) 또는 모듈 (structure-modules.md)에 해당하는 기본 모델 클래스에서 확장 한 구체적인 모델 클래스를 정의합니다.
  이 구체적인 모델 클래스가 해당 응용 프로그램이나 모듈에 고유의 규칙과 논리를 포함한다.

예를 들어, 고급 프로젝트 템플릿 (https://github.com/yiisoft/yii2-app-advanced/blob/master/docs/guide/README.md)에서 기본 모델 클래스 `common\models\Post` 을 정의 할 수 있습니다.
다음 프런트 엔드 응용 프로그램에서는 `common\models\Post`에서 확장 한 구체적인 모델 클래스 `frontend\models\Post` 를 정의하여 사용합니다.
또한 백엔드 응용 프로그램에서도 마찬가지로 `frontend\models\Post` 를 정의합니다.
이 전략을 가지고 `frontend\models\Post` 안의 코드는 프런트 엔드 응용 프로그램 고유의 것이라고 보장 할 수 있습니다.
그리고 프런트 엔드의 코드에 어떤 변경도 백엔드 어플리케이션을 끊을지도 모른다는 걱정은 할 필요가 없습니다.