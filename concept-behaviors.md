동작(Behaviors)
=====

비헤이비어은 [[yii\base\Behavior]], 혹은 그 하위 클래스의 인스턴스입니다. 비헤이비어는
[mixins](http://en.wikipedia.org/wiki/Mixin), 로 알려진 기존의 [[yii\base\Component|component]] 클래스
기능을 클래스의 상속을 변경하지 않고 확장 할 수 있습니다. 구성 요소에 비헤이비어를 연결하면
구성 요소는 동작 메서드와 속성이 "injects"되고 그 메서드와 속성은
구성 요소 클래스 자체에 정의되어있는 것처럼 액세스 할 수 있습니다. 또한 비헤이비어는
컴퍼넌트에 의해 트리거 된 [events](concept-events.md)에 응답 할 수 있기 때문에,
비헤이비어 구성 요소의 일반 코드 실행을 사용자 정의 할 수 있습니다.


비헤이비어 정의  <span id="defining-behaviors"></span>
------------------

비헤이비어를 정의하려면 [[yii\base\Behavior]] 또는 자식 클래스를 상속하는 클래스를 만듭니다. 예를 들어 :

```php
namespace app\components;

use yii\base\Behavior;

class MyBehavior extends Behavior
{
    public $prop1;

    private $_prop2;

    public function getProp2()
    {
        return $this->_prop2;
    }

    public function setProp2($value)
    {
        $this->_prop2 = $value;
    }

    public function foo()
    {
        // ...
    }
}
```

위의 코드는`app \ components \ MyBehavior`라는 두 개의 속성 -`prop1`와`prop2` -와
`foo ()`메소드를 가지는 동작 클래스를 정의합니다. `prop2` 속성은`getProp2 ()`getter 메소드와`setProp2 ()`setter 메소드로 정의되는 것에 주목하십시오.
[yii \ base \ Behavior]]는 [[yii \ base \ Object]를 계승하고 있기 때문에 getter와 setter에 따르면 속성 (concept-properties.md) 정의를 지원합니다.

이 클래스는 동작이므로 구성 요소에 연결되면, 그 구성 요소는`prop1`와`prop2` 속성 그것`foo ()`메소드를 갖게됩니다.

> Tip : 동작 내에서 [yii \ base \ Behavior : owner] 속성을 통해 비헤이비어를 연결 한 구성 요소에 액세스 할 수 있습니다.

구성 요소 이벤트 처리
------------------

비헤이비어가 부착 된 구성 요소가 트리거하는 이벤트에 응답 할 필요가있는 경우
[yii \ base \ Behavior :: events ()]] 메소드를 오버라이드하자. 예를 들어 :

```php
namespace app \ components;

use yii \ db \ ActiveRecord;
use yii \ base \ Behavior;

class MyBehavior extends Behavior
{
    // ...

    public function events ()
    {
        return [
            ActiveRecord :: EVENT_BEFORE_VALIDATE => 'beforeValidate'
        ];
    }

    public function beforeValidate ($ event)
    {
        // ...
    }
}
```

[yii \ base \ Behavior :: events ()]] 메소드는 이벤트와 해당 핸들러의리스트를 돌려줍니다.
위의 예에서는 [yii \ db \ ActiveRecord :: EVENT_BEFORE_VALIDATE | EVENT_BEFORE_VALIDATE] 이벤트가있는 것,
그 핸들러 정의 인`beforeValidate ()`을 선언하고 있습니다. 이벤트 핸들러를 지정할 때는 다음의 표기 방법을 사용할 수 있습니다 :

* 동작 클래스의 메소드 이름을 참조하는 문자열 (위의 예 등)
* 객체 또는 클래스 이름과 문자열 메소드 이름 (괄호없이) 예`[$ object 'methodName']`
* 익명 함수

이벤트 핸들러의 서명은 다음과 같이하십시오. `$ event`는 이벤트의 매개 변수를 참조합니다. 이벤트에 대한 자세한 내용은
[이벤트] (concept-events.md) 섹션을 참조하십시오.

```php
function ($ event) {
}
```

비헤이비어 첨부 <span id = "attaching-behaviors"> </ span>
-------------------

[yii \ base \ Component | 구성 요소]]에 동작의 연결은 정적이거나 동적 일 수 있습니다. 실제로, 전자 쪽이 더 일반적이지만.

비헤이비어를 정적으로 연결하려면 비헤이비어를 첨부 할 구성 요소 클래스의 [yii \ base \ Component :: behaviors () | behaviors ()]] 메서드를 재정의합니다.
[yii \ base \ Component :: behaviors () | behaviors ()]] 메소드는 비헤이비어 구성 (concept-configurations.md)의 목록을 반환해야합니다.
각 동작의 구성은 비헤이비어 클래스 이름도 구성 정보 배열도 괜찮습니다.

```php
namespace app \ models;

use yii \ db \ ActiveRecord;
use app \ components \ MyBehavior;

class User extends ActiveRecord
{
    public function behaviors ()
    {
        return [
            // 이름 동작 동작 클래스 이름 만
            MyBehavior : className ()

            // 명명 된 동작 동작 클래스 이름 만
            'myBehavior2'=> MyBehavior : className ()

            // 이름 동작 구성 정보 배열
            [
                'class'=> MyBehavior : className ()
                'prop1'=> 'value1',
                'prop2'=> 'value2',
            ,

            // 명명 된 동작 구성 정보 배열
            'myBehavior4'=>
                'class'=> MyBehavior : className ()
                'prop1'=> 'value1',
                'prop2'=> 'value2',
            ]
        ];
    }
}
```

동작 구성에 대응하는 배열의 키를 지정하여 비헤이비어에 이름을 연결할 수 있습니다. 이 경우 비헤이비어는 * 명명 된 동작 *라고합니다. 위의 예에서는 2 개의 명명 된 동작
`myBehavior2`와`myBehavior4` 있습니다. 비헤이비어가 이름과 관련되지 않은 경우 * 이름 동작 *라고합니다.


비헤이비어를 동적으로 연결하려면 비헤이비어를 연결하려고하는 구성 요소의 [yii \ base \ Component :: attachBehavior ()]] 메소드를 호출합니다 :

```php
use app \ components \ MyBehavior;

// 비헤이비어 객체를 연결
$ component-> attachBehavior ( 'myBehavior1'new MyBehavior);

// 동작 클래스를 연결
$ component-> attachBehavior ( 'myBehavior2', MyBehavior : className ());

// 구성 정보 배열을 연결
$ component-> attachBehavior ( 'myBehavior3',
    'class'=> MyBehavior : className ()
    'prop1'=> 'value1',
    'prop2'=> 'value2',
]);
```
[yii \ base \ Component :: attachBehaviors ()]] 메소드를 사용하면 한 번에 여러 동작을 연결할 수 있습니다 :

```php
$ component-> attachBehaviors (
    'myBehavior1'=> new MyBehavior // 명명 된 동작
    MyBehavior : className () // 이름 동작
]);
```

다음과 같이 구성 정보 (concept-configurations.md)을 통해 비헤이비어를 연결할 수 있습니다 :

```php
[
    'as myBehavior2'=> MyBehavior : className ()

    'as myBehavior3'=>
        'class'=> MyBehavior : className ()
        'prop1'=> 'value1',
        'prop2'=> 'value2',
    ,
]
```

자세한 내용은 구성 정보 (concept-configurations.md # configuration-format) 섹션을 참조하십시오.

비헤이비어 사용 <span id = "using-behaviors"> </ span>
---------------

비헤이비어를 사용하려면 먼저 위의 방법에 따라 [yii \ base \ Component | 구성 요소에 연결합니다. 비헤이비어 구성 요소에 연결되면, 그 사용 방법은 간단합니다.

당신은 연결되어있는 구성 요소를 통해 비헤이비어 * 공개 * 멤버 변수 또는 getter와 setter에 의해 정의 된 속성에 액세스 할 수 있습니다 :

```php
// "prop1"는 동작 클래스에 정의 된 속성
echo $ component-> prop1;
$ component-> prop1 = $ value;
```

또한 마찬가지로 비헤이비어 * 공개 * 메소드도 호출 할 수 있습니다 :

```php
// foo ()가 동작 클래스에 정의 된 공용 메소드
$ component-> foo ();
```

보시다시피,`$ component`는`prop1`과`foo ()`를 정의하지 않음에도 불구하고
첨부 된 비헤이비어가 그들을 구성 요소 정의의 일부인 것처럼 사용할 수 있습니다.

만약 두 가지 동작이 같은 속성과 메소드를 정의하며 모두 동일한 구성 요소에 연결되어있는 경우
속성과 메소드의 액세스시 * 먼저 * 구성 요소에 첨부 된 비헤이비어가 우선됩니다.

동작은 구성 요소에 첨부 될 때, 이름과 연관된지도 모릅니다. 이 경우
그 이름을 사용하여 비헤이비어 객체에 액세스 할 수 있습니다 :

```php
$ behavior = $ component-> getBehavior ( 'myBehavior');
```

또한 구성 요소에 첨부 된 모든 비헤이비어를 얻을 수 있습니다 :

```php
$ behaviors = $ component-> getBehaviors ();
```


비헤이비어 분리 <span id = "detaching-behaviors"> </ span>
-------------------

비헤이비어를 분리하려면 동작에 붙여진 이름과 함께 [yii \ base \ Component :: detachBehavior ()를 호출합니다 :

```php
$ component-> detachBehavior ( 'myBehavior1');
```

* 모든 * 비헤이비어를 분리 할 수​​ 있습니다 :

```php
$ component-> detachBehaviors ();
```


`TimestampBehavior` 이용 <span id = "using-timestamp-behavior"> </ span>
-------------------------

마무리를 위해 [yii \ behaviors \ TimestampBehavior]를 살펴 보자. 이 동작은
저장시 (즉 삽입이나 업데이트) 또는 [[yii \ db \ ActiveRecord | 액티브 레코드] 모델
타임 스탬프 속성의 자동 업데이트를 지원합니다.

먼저 사용하려고 생각하고있다 [yii \ db \ ActiveRecord | 액티브 레코드] 클래스에이 비헤이비어를 첨부합니다 :

```php
namespace app \ models \ User;

use yii \ db \ ActiveRecord;
use yii \ behaviors \ TimestampBehavior;

class User extends ActiveRecord
{
    // ...

    public function behaviors ()
    {
        return [
            [
                'class'=> TimestampBehavior : className ()
                'attributes'=>
                    ActiveRecord :: EVENT_BEFORE_INSERT => 'created_at', 'updated_at',
                    ActiveRecord :: EVENT_BEFORE_UPDATE => 'updated_at',
                ,
            ,
        ];
    }
}
```

위의 동작 구성은 레코드가 :

* 삽입 될 때 비헤이비어는 현재 타임 스탬프를`created_at`와`updated_at` 속성에 할당합니다
* 갱신 될 때 비헤이비어는 현재 타임 스탬프를`updated_at` 속성에 할당합니다

제자리에 코드를 사용하면 만약`User` 객체를 마련하고, 그것을 저장하려고하면, 그래서
`created_at`와`updated_at`가 자동으로 현재의 타임 스탬프로 채워집니다.

```php
$ user = new User;
$ user-> email = 'test@example.com';
$ user-> save ();
echo $ user-> created_at; // 현재 타임 스탬프가 나타날
```

[yii \ behaviors \ TimestampBehavior | TimestampBehavior]]는 지정된 속성에 현재의 타임 스탬프를 할당
그것을 데이터베이스에 저장하는 편리한 방법 [yi​​i \ behaviors \ TimestampBehavior :: touch () | touch ()를 제공합니다.

```php
$ user-> touch ( 'login_time');
```

비헤이비어 토레이토 비교 <span id = "comparison-with-traits"> </ span>
----------------------

비헤이비어는 주가되는 클래스에 속성과 메소드를 "주입"이라는 점에서 토레이토 (http://www.php.net/traits)
비슷하지만 이들은 여러면에서 다릅니다. 다음에 설명하는 바와 같이, 그들은 서로 장단점을 가지고 있습니다.
그들은 대안보다는 오히려 상호 보완 관계 같은 것입니다.


### 비헤이비어를 사용하는 이유 <span id = "pros-for-behaviors"> </ span>

동작은 일반 클래스처럼 상속을 지원하고 있습니다. 한편 토레이토은
언어 지원 된 복사 및 붙여 넣기로 간주 될 수 있습니다. 토레이토는 상속을 지원하지 않습니다.

비헤이비어는 구성 요소 클래스의 변경을 필요로하지 않고, 구성 요소에 동적으로 연결하거나 분리 할 수​​ 있습니다. 토레이토를 사용하려면 토레이토를 사용하여 클래스의 코드를 작성해야합니다.

동작은 구성 가능하지만 토레이토 불가능합니다.

비헤이비어는 이벤트에 응답하여 구성 요소의 코드 실행을 사용자 정의 할 수 있습니다.

동일한 구성 요소에 연결된 다른 동작 사이에서 이름 충돌이있는 경우 충돌이 자동으로
먼저 구성 요소에 첨부 된 것을 우선하여 해소됩니다.
다른 토레이토이 일으킨 이름 충돌의 경우 영향을받는 속성이나 메소드의 이름 변경에 의한 수동으로 해결해야합니다.


### 토레이토를 사용하는 이유 <span id = "pros-for-traits"> </ span>

동작 시간도 메모리도 먹는다 객체이므로 토레이토는 동작보다 훨씬 효율적입니다.

토레이토는 언어 구조이기 때문에 IDE와 궁합이 우수합니다.
