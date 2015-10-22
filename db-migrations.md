데이터베이스 마이그레이션
============================

> Note |주의 :이 절은 아직 작성 중입니다.

소스 코드와 같이 데이터베이스의 구조도 데이터베이스 기반의 응용 프로그램이 개발되고 유지 보수됨에 따라 점차 발전하고 있습니다.
예를 들어, 개발 중에 새로 테이블이 추가 될 수도있을 것이고, 응용 프로그램을 실서비스 한후 성능 개선을 위해 인덱스를 추가해야 할수도 있습니다.
이러한 데이터베이스의 구조적인 변경 (** 마이그레이션 **라고합니다)은 종종 약간의 소스코드 변경이 필요 합니다.
이러한 이유로 인해 Yii는 버전 제어 소스 코드와 함께 데이터베이스 마이그레이션의 관점에서 데이터베이스 변경을 추적 할 수있는 이른바 데이터베이스 마이그레이션 기능을 지원합니다.

다음 단계는 개발 중에 팀에 의해 데이터베이스 마이그레이션이 사용되는 예를 보여줍니다.

1. Tim이 새로운 마이그레이션을 만든다.(예를 들어, 새 테이블을 만들거나 열 정의를 변경 하는 등등)
2. Tim이 새로운 마이그레이션을 소스 컨트롤 시스템 에 등록한다. (예를 들어 Git이나 Mercurial)
3. Doug이 소스 컨트롤 시스템에서 자신의 저장소를 업데이트하여 새 마이그레이션을 받는다.
4. Doug이 자신의 로컬 개발 데이터베이스에 적용하고 Tim이 변경 사항을 반영하여 자신의 데이터베이스를 동기화한다.

그리고 다음 단계는 데이터베이스 마이그레이션과 새로운 릴리스를 배포하는 방법을 보여줍니다

1.Scott은 몇 가지 새로운 데이터베이스 마이그레이션을 포함한 프로젝트 저장소에 릴리스 태그를 만듭니다.
2.Scott은 자료 태그 프로덕션 서버의 소스 코드를 업데이트합니다.
3.Scott은 프로덕션 데이터베이스에 쌓인의 데이터베이스 마이그레이션을 적용합니다.

Yii는 데이터베이스 마이그레이션을 명령 줄 도구에서 다음과 같은 기능을 지원합니다.

* 새로운 마이그레이션을 만들기
* 마이그레이션 적용;
* 마이그레이션 복원;
* 마이그레이션 다시 적용;
* 마이그레이션 히스토리와 상태를 표시.

이러한 모든 명령은 Yii 마이그레이션을 통해 엑세스 할수 있습니다.이 섹션에서 우리는 이러한 기능를 사용하여 다양한 작업을 수행하는 방법을 자세히 설명합니다. 또한 help 명령과 yii 도움말 전환을 통해 각 기능의 사용 방법을 받을수 있습니다.

> Tip |힌트 : 마이그레이션뿐만 아니라 데이터베이스 스키마에 영향을 주지만 새로운 스키마에 맞춰 RBAC의 hierarcy을 만들거나 캐시를 정리하기 위해 기존 데이터를 조정할 수 있습니다.


마이그레이션 만들기
--------------------------

새로운 마이그레이션을 작성하기 위해서는 다음 명령을 실행합니다.

```
yii migrate/create <name>
```

`name` 인수는 새 마이그레이션을 만드는 방법에 대한 간략한 설명을 지정합니다. 예를 들어, 당신은 이름 create_news_table을 사용하여 다음 명령을 실행 할 수 있습니다.

```
yii migrate/create create_news_table
```

> Note | 주의: name 인수는 생성 된 마이그레이션 클래스 이름의 일부로 사용되기 때문에, 알파벳, 숫자 및 / 또는 밑줄 만 포함해야합니다.


위의 명령은`m101129_185401_create_news_table.php`라는 새 파일을 만듭니다.
이 파일은`@app/migrations` 디렉토리에 생성됩니다.
초기 상태에서의 `m101129_185401_create_news_table` 마이그레이션 파일은 다음의 코드를 포함하고 있습니다.

```php
<?php

use yii\db\Migration;

class m150101_185401_create_news_table extends Migration
{
    public function up()
    {

    }

    public function down()
    {
        echo "m101129_185401_create_news_table cannot be reverted.\n";

        return false;
    }

    /*
    // Use safeUp/safeDown to run migration code within a transaction
    public function safeUp()
    {
    }

    public function safeDown()
    {
    }
    */
}
```

클래스 이름은 파일 이름과 동일하며`m<YYMMDD_HHMMSS>_<Name>`패턴을 따릅니다. 여기에서

* '<YYMMDD_HHMMSS>` 마이그레이션 작성 명령이 실행되는 UTC의 날짜를 의미한다.
*`<name>`명령에 제공`NAME` 인수의 값과 동일합니다.

클래스 중에서는`up()`메소드가 실제 데이터베이스 마이그레이션을 구현하는 코드를 포함 할 메소드입니다.
즉,`up()`메소드가 실제로 데이터베이스를 변경하는 코드를 실행합니다.
`down()`메쏘드는`up()`에 의해 변경된 내용을 취소하는 코드를 포함 할 수 있습니다.

다음 코드는`news` 테이블을 만들 수있는 마이그레이션 클래스를 구현할 수 방법을 보여줍니다

```php
<?php

use yii\db\Schema;
use yii\db\Migration;

class m150101_185401_create_news_table extends Migration
{
    public function up()
    {
        $this->createTable('news', [
            'id' => Schema::TYPE_PK,
            'title' => Schema::TYPE_STRING . ' NOT NULL',
            'content' => Schema::TYPE_TEXT,
        ]);
    }

    public function down()
    {
        $this->dropTable('news');
    }
}
```

>  Info | 정보 : 모든 마이그레이션을 취소 할 수 있는 이유는 없습니다.
예를 들어, `up()` 메소드가 테이블에서 행을 삭제하는 것 인 경우, `down()` 메소드에서 해당 행을 복구 할 수 없습니다.
또한 데이터베이스 마이그레이션을 취소 하는게 일반적이지 않기 때문에, 경우에 따라서는 귀찮다라는 이유만으로 `down()`를
구현하지 않을 때도 있을 것입니다. 그런 경우 마이그레이션이 취소 불가능 함을 나타 내기 위해 `down()` 메소드에서 false를 반환 해야 합니다.


기초 마이그레이션 클래스 [[yii\db\Migration]]는 [[yii\db\Migration::db|db]] 속성을 사용하여 데이터베이스 연결에 액세스하는 것을 허용하고 있습니다. 이 데이터베이스 연결은 데이터베이스 스키마를 처리하고 설명하는 방법을 사용하여 데이터베이스 스키마를 조작 할 수 있습니다.


기본 마이그레이션 클래스 yii의 \ DB를 \ 마이그레이션은 DB 속성을 통해 데이터베이스 연결을 제공합니다.
 사용자는 데이터베이스 스키마 작업에 기재된 방법을 사용하여 데이터베이스 스키마를 조작하는 데 사용할 수있다.

테이블이나 컬럼을 만들 때 물리적 형태를 사용하는 것이 아니라 추상를 사용하여 당신의 마이그레이션이 특정 DBMS에 의존하지 않도록합니다. [yii \ db \ Schema] 클래스가 지원되는 추상 형을 나타내는 일련의 상수를 정의하고 있습니다. 이러한 상수는 TYPE_ <Name> 형식의 이름을 가지고 있습니다. 예를 들어, TYPE_PK는 자동 증가 기본 키 형이며, TYPE_STRING은 문자열입니다. 이러한 추상은 이주가 특정 데이터베이스에 적용되는 경우 해당 물리적?? 형태로 번역됩니다. MySQL의 경우 TYPE_PK은 int (11) NOT NULL AUTO_INCREMENT PRIMARY KEY로 변환되어 TYPE_STRING은 varchar (255)입니다.

추상 형식을 사용할 때 부수적 인 제약을 추가 할 수 있습니다. 위의 예에서는 Schema : TYPE_STRING에 NOT NULL을 추가하여이 컬럼이 null을 허용하지 않도록 지정합니다.

    Info | 정보 : 추상과 물리적 형태의 대응 관계는 각각의 QueryBuilder 구상 클래스의 [yii \ db \ QueryBuilder : $ typeMap | $ typeMap] 속성에 의해 정의되어 있습니다.

버전 2.0.6 이후는 열 스키마를 정의하기위한 더욱 편리한 방법을 제공하는 스키마 빌더가 새롭게 도입되어 있기 때문에 위의 마이그레이션은 다음과 같이 쓸 수 있습니다.

<? php

use yii \ db \ Migration;

class m150101_185401_create_news_table extends Migration
{
    public function up ()
    {
        $ this-> createTable ( 'news',
            'id'=> $ this-> primaryKey ()
            'title'=> $ this-> string () -> notNull ()
            'content'=> $ this-> text ()
        ]);
    }

    public function down ()
    {
        $ this-> dropTable ( 'news');
    }
}

컬럼의 형태를 정의하는 데 사용할 수있는 모든 메소드의 목록은 [[yii \ db \ SchemaBuilderTrait]]의 API 문서에서 참조 할 수 있습니다.
트랜잭션을 사용 마이그레이션

복잡한 일련의 DB 마이그레이션을 수행 할 때는 일반적으로 데이터베이스의 일관성과 무결성을 유지하기 위해 각 마이그레이션이 전체적으로 성공 또는 실패하는 것을 보장해야합니다. 이 목적을 달성하기 위해 각 마이그레이션 DB 작업을 트랜잭션으로 묶을 수 권장됩니다.

트랜잭션을 사용 마이그레이션을 구현하기위한 쉬운 방법은 이주의 코드를 safeUp ()와 safeDown () 메소드에 넣는 것입니다. 이 두 메소드가 up ()과 down ()과 다른 점은 이들이 암묵적으로 트랜잭션에 둘러싸여있는 것입니다. 결과적으로 이러한 방법 중 뭔가 조작이 실패했을 경우는 선행하는 모든 작업이 자동으로 롤백됩니다.

다음 예제에서는 news 테이블을 만들뿐만 아니라이 테이블에 초기 값이되는 행을 삽입합니다.

<? php

use yii \ db \ Migration;

class m150101_185401_create_news_table extends Migration
{
    public function safeUp ()
    {
        $ this-> createTable ( 'news',
            'id'=> $ this-> primaryKey ()
            'title'=> $ this-> string () -> notNull ()
            'content'=> $ this-> text ()
        ]);

        $ this-> insert ( 'news',
            'title'=> 'test 1',
            'content'=> 'content 1',
        ]);
    }

    public function safeDown ()
    {
        $ this-> delete ( 'news', 'id'=> 1);
        $ this-> dropTable ( 'news');
    }
}

일반적으로 safeUp ()에서 여러 DB 작업을 수행하려면 safeDown ()는 실행의 순서를 반대로해야한다는 점에주의하십시오. 위의 예에서는 safeUp ()는 먼저 테이블을 만들고 다음 행을 삽입하고 safeDown ()에서는 먼저 행을 삭제 한 다음 테이블을 삭제하고 있습니다.

    Note |주의 : 모든 DBMS가 트랜잭션을 지원하고있는 것은 아닙니다. 또한 트랜잭션에 넣을 수없는 DB 쿼리 수 있습니다. 몇 가지 예를 암시 적 커밋에서 볼 수 있습니다. 이 경우에는 대신에, up ()과 down ()를 구현해야합니다.

데이터베이스 액세스 메소드

기초 마이그레이션 클래스 [yii \ db \ Migration] 데이터베이스에 액세스하고 조작하기위한 일련의 메소드를 제공합니다. 당신은 이러한 방법이 [yii \ db \ Command] 클래스에서 제공하는 DAO 메소드와 같은 이름을 붙일 수 있음을 알 수 있습니다. 예를 들어, [yii \ db \ Migration : createTable ()] 메서드는 [yii \ db \ Command : createTable ()]와 동일하게 새 테이블을 만듭니다.

[yii \ db \ Migration]에 의해 제공되는 메소드를 사용하는 장점은 [yii \ db \ Command] 인스턴스를 명시 적으로 만들 필요가없는 것, 그리고 각 메소드를 실행하면 어떤 데이터베이스 작업이 얼마나 시간이 지남에 실행되었는지를 가르쳐주는 유익한 메시지가 자동으로 표시되는 것입니다.

다음이 그러한 데이터베이스 액세스 메서드 목록입니다.

    [yii \ db \ Migration :: execute () | execute ()] : SQL 문을 실행
    [yii \ db \ Migration :: insert () | insert () : 줄을 삽입
    [yii \ db \ Migration :: batchInsert () | batchInsert ()]] : 여러 행을 삽입
    [yii \ db \ Migration :: update () | update () : 행을 업데이트
    [yii \ db \ Migration :: delete () | delete () : 행을 삭제
    [yii \ db \ Migration : createTable () | createTable ()]] : 테이블을 생성
    [yii \ db \ Migration :: renameTable () | renameTable () : 테이블의 이름을 변경
    [yii \ db \ Migration :: dropTable () | dropTable ()]] : 테이블을 삭제
    [yii \ db \ Migration :: truncateTable () | truncateTable () : 테이블의 모든 행을 삭제
    [yii \ db \ Migration :: addColumn () | addColumn () : 컬럼을 추가
    [yii \ db \ Migration :: renameColumn () | renameColumn () : 컬럼의 이름을 변경
    [yii \ db \ Migration :: dropColumn () | dropColumn () : 컬럼을 삭제
    [yii \ db \ Migration :: alterColumn () | alterColumn () : 컬럼의 정의를 변경
    [yii \ db \ Migration :: addPrimaryKey () | addPrimaryKey () : 기본 키를 추가
    [yii \ db \ Migration :: dropPrimaryKey () | dropPrimaryKey () : 기본 키를 제거
    [yii \ db \ Migration :: addForeignKey () | addForeignKey () : 외부 키 추가
    [yii \ db \ Migration :: dropForeignKey () | dropForeignKey () : 외부 키를 삭제
    [yii \ db \ Migration :: createIndex () | createIndex () : 인덱스를 생성
    [yii \ db \ Migration :: dropIndex () | dropIndex () : 인덱스를 삭제

    Info | 정보 : [yii \ db \ Migration] 데이터베이스 쿼리 메소드를 제공하지 않습니다. 이것은 일반적으로 데이터베이스에서 데이터를 검색에 대한 메시지를 추가하여 표시 할 필요가 없기 때문입니다. 더욱이 복잡한 쿼리를 작성하고 실행하기 위해서는 강력한 쿼리 빌더를 사용할 수 있기 때문입니다.

    Note |주의 : 마이그레이션을 사용하여 데이터를 조작하는 경우에 당신은 당신의 액티브 레코드 클래스를 데이터 조작에 사용하면 편리 않을까,라고 볼 수도 없습니다. 왜냐하면 어떤 논리는 이미 액티브 레코드에서 구현 된 이니까,라고. 그러나 마이그레이션에서 쓰는 코드가 영구 불변하다는 것을 본질로하는 것과는 대조적으로 응용 프로그램의 로직은 변화에 노출 될 것이라는 것을 명심해야합니다. 따라서 마이그레이션 코드 활성 레코드를 사용하는 활성 레코드 레이어의 로직의 변경이 뜻하지 않게 기존의 마이그레이션을 파괴 할 수있을 수 있습니다. 이러한 이유 때문에 마이그레이션 코드는 활성 레코드와 같은 응용 프로그램의 다른 로직으로부터 독립을 유지해야합니다.

마이그레이션을 적용

데이터베이스를 최신 구조로 업그레이드하기 위해 사용할 수있는 모든 새로운 마이그레이션을 적용하기 위해 다음 명령을 사용해야합니다.

yii migrate

명령을 실행하면 아직 적용되지 않은 모든 마이그레이션이 나열됩니다. 나열된 마이그레이션을 적용하는 것을 당신이 확인하면 타임 스탬프 값의 순서대로 하나씩 모든 새로운 마이그레이션 클래스의 up () 또는 safeUp () 메소드가 실행됩니다. 마이그레이션 중 하나가 실패 할 경우, 명령은 나머지 마이그레이션을 적용하지 않고 종료합니다.

적용이 성공적으로 마이그레이션 하나 하나에 대한 migration라는 데이터베이스 테이블에 행을 삽입 이주의 성공이 기록됩니다. 이 기록하여 마이그레이션 도구는 어떤 마이그레이션이 적용되고 어떤 마이그레이션이 적용되지 않았는지를 확인할 수 있습니다.

    Info | 정보 : 마이그레이션 도구는 명령 [yii \ console \ controllers \ MigrateController :: db | db] 옵션에서 지정된 데이터베이스에 migration 테이블을 자동으로 작성합니다. 기본적으로이 데이터베이스는 db 응용 프로그램 구성 요소에 의해 지정됩니다.

어떠한 경우에는, 사용 가능한 모든 마이그레이션이 아니라 하나 또는 여러 개의 새로운 마이그레이션 만 적용 할 수 있습니다. 명령을 수행 할 때 적용 할 마이그레이션의 수를 지정함으로써 그렇게 할 수 있습니다. 예를 들어 다음 명령은 다음의 세 개의 유효한 마이그레이션을 적용하려고하는 것입니다.

yii migrate 3

또한, 마이그레이션까지 데이터베이스에 적용하는 특정 마이그레이션을 명시 적으로 지정할 수도 있습니다. 따라서에는 migrate / to 명령을 다음 중 하나의 형식으로 사용합니다.

yii migrate / to 150101_185401 # 타임 스탬프를 사용하여 마이그레이션을 지정
yii migrate / to "2015-01-01 18:54:01"# strtotime ()에 의해 해석 할 수있는 문자열을 사용
yii migrate / to m150101_185401_create_news_table # 성명을 사용
yii migrate / to 1392853618 # UNIX 타임 스탬프를 사용

지정된 마이그레이션보다 이전이 적용되지 않고 남아있는 경우는 지정된 것이 적용되기 전에 모든 적용됩니다.

지정된 마이그레이션이 이미 적용되어있는 경우보다 최근의 것이 적용되어 있으면 취소됩니다.
마이그레이션을 취소

적용된 이주를 한 개 또는 복수 취소하려면 다음 명령을 사용할 수 있습니다.

yii migrate / down # 최근에 적용된 마이그레이션 한 개를 취소
yii migrate / down 3 # 최근에 적용된 마이그레이션 세 개를 취소

    Note |주의 : 모든 마이그레이션을 취소 할 수는 없습니다. 그런 마이그레이션을 취소하려고하면 오류가 발생하고 취소 과정이 종료됩니다.

마이그레이션을 다시 적용

마이그레이션을 다시 적용은 지정된 마이그레이션을 먼저 취소하고 다시 적용하는 것을 의미합니다. 이것은 다음의 명령으로 수행 할 수 있습니다.

yii migrate / redo # 마지막으로 적용된 한개의 마이그레이션을 다시 적용
yii migrate / redo 3 # 마지막으로 적용된 세 개의 마이그레이션을 다시 적용

    Note |주의 : 마이그레이션이 취소 불가능한 경우는 그것을 다시 적용 할 수 없습니다.

마이그레이션을 나열하려면

어떤 마이그레이션이 적용되어 있으며, 어떤 마이그레이션이 적용되지 않은인지를 나열하려면 다음 명령을 사용할 수 있습니다.

yii migrate / history # 마지막으로 적용된 10 개의 마이그레이션을 표시
yii migrate / history 5 # 마지막으로 적용된 5 개의 마이그레이션을 표시
yii migrate / history all # 적용된 모든 마이그레이션을 표시

yii migrate / new # 적용 가능한 최초의 10 개 이주보기
yii migrate / new 5 # 적용 가능한 최초의 5 개의 마이그레이션을 표시
yii migrate / new all # 적용 가능한 모든 이주보기

마이그레이션 내역을 수정

어떠한 경우에는, 실제로 마이그레이션을 적용하거나 취소하는 것이 아니라 데이터베이스가 특정 마이그레이션까지 업그레이드 된 것으로 표시하고 싶은 것 뿐이라는 경우가 있습니다. 이런 일이 자주 일어나는 데이터베이스를 수동으로 특정 상태로 변경 한 후 그 변화를위한 하나 이상의 이주를 기록하지만 다시 적용하고 싶지 않다는 경우입니다 . 다음 명령으로이 목적을 달성 할 수 있습니다.

yii migrate / mark 150101_185401 # 타임 스탬프를 사용하여 마이그레이션을 지정
yii migrate / mark "2015-01-01 18:54:01"# strtotime ()에 의해 해석 할 수있는 문자열을 사용
yii migrate / mark m150101_185401_create_news_table # 성명을 사용
yii migrate / mark 1392853618 # UNIX 타임 스탬프를 사용

이 명령은 일정한 행을 추가하거나 삭제하여 migration 테이블을 수정하고 데이터베이스가 지정된 것까지 마이그레이션이 적용되어 있는지를 나타냅니다. 이 명령은 마이그레이션이 적용되거나 취소되거나는하지 않습니다.
마이그레이션을 사용자 정의 할

마이그레이션 명령을 사용자 지정하는 방법이 몇 가지 있습니다.
명령 줄 옵션을 사용

마이그레이션 명령은 그 동작을 사용자 지정하는 데 사용할 수있는 명령 줄 옵션이 몇 가지 있습니다.

    interactive : 논리 값 (기본값은 true). 마이그레이션을 대화식 모드로 실행할지 여부를 지정합니다. true 인 경우는 명령이 어떤 작업을 수행하기 전에 사용자 확인을 요청합니다. 명령이 백그라운드 프로세스에서 사용되는 경우이 옵션을 false로 설정합니다.

    migrationPath : 문자열 (기본값은 @ app / migrations). 모든 마이그레이션 클래스 파일을 저장하고있는 디렉토리를 지정합니다. 이 값은 디렉토리 경로 또는 경로 별칭으로 지정할 수 있습니다. 디렉토리가 존재해야하며, 그렇지 않으면 명령이 오류를 발생시키는 점에 유의하십시오.

    migrationTable : 문자열 (기본값은 migration). 마이그레이션 이력 정보를 저장하는 데이터베이스 테이블 이름을 지정합니다. 테이블이 존재하지 않으면 명령에 의해 자동으로 생성됩니다. version varchar (255) primary key, apply_time integer라는 구조의 테이블을 수동으로 작성해도 상관 없습니다.

    db : 문자열 (기본값은 db). 데이터베이스 응용 프로그램 구성 요소의 ID를 지정합니다. 이 명령은 마이그레이션 적용되는 데이터베이스를 나타냅니다.

    templateFile : 문자열 (기본값은 @ yii / views / migration.php). 해골의 마이그레이션 클래스 파일을 생성하는 데 사용되는 템플릿 파일의 경로를 지정합니다. 이 값은 파일 경로 또는 경로 별칭으로 지정할 수 있습니다. 템플릿 파일은 PHP 스크립트이며, 그 속에서 마이그레이션 클래스의 이름을 얻기 위해 $ className이라는 사전 정의 된 변수를 사용할 수 있습니다.

다음의 예는 이러한 옵션의 사용법을 보여줍니다.

예를 들어, forum 모듈로 마이그레이션을 적용하려고하고 있으며, 그 마이그레이션 파일이 모듈 migrations 디렉토리에 배치되어있는 경우 다음 명령을 사용할 수 있습니다.

# forum 모듈의 마이그레이션을 비대화 적으로 적용
yii migrate --migrationPath = @ app / modules / forum / migrations --interactive = 0

명령을 전역으로 구성

마이그레이션 명령을 실행할 때마다 동일한 옵션의 값을 입력하는 대신 다음과 같이 응용 프로그램 구성 정보에 명령을 한 번만 구성하고 끝 마칠 수 있습니다.

return [
    'controllerMap'=>
        'migrate'=>
            'class'=> 'yii \ console \ controllers \ MigrateController'
            'migrationTable'=> 'backend_migration'
        ,
    ,
];

상기와 같이 구성 해두면, migrate 명령을 실행 할 때마다 backend_migration 테이블 이주 역사를 기록하기 위해 사용되게됩니다. 이제 migrationTable 명령 줄 옵션을 사용하여 테이블을 지정할 필요가 없습니다.
여러 데이터베이스로 마이그레이션을 적용

기본적으로 마이그레이션은 db 응용 프로그램 구성 요소에 의해 지정된 동일한 데이터베이스에 적용됩니다. 마이그레이션을 다른 데이터베이스에 적용하려면 다음과 같이 db 명령 줄 옵션을 지정할 수 있습니다.

yii migrate --db = db2

위의 명령은 이주 db2 데이터베이스에 적용합니다.

경우에 따라서는 일부 마이그레이션은 데이터베이스에 적용하고 다른 일부 마이그레이션은 또 다른 데이터베이스에 적용하겠다는 것이 있습니다. 이 목적을 달성하기 위해 이주 클래스를 구현할 때 그 이주가 사용하는 DB 구성 요소의 ID를 명시 적으로 지정해야합니다. 예를 들어, 다음과 같이합니다.

<? php

use yii \ db \ Migration;

class m150101_185401_create_news_table extends Migration
{
    public function init ()
    {
        $ this-> db = 'db2';
        parent :: init ();
    }
}

위의 마이그레이션은 db 명령 줄 옵션에서 다른 데이터베이스를 지정한 경우에도 db2에 적용됩니다. 그러나 이주 역사는 db 명령 줄 옵션에 지정된 데이터베이스에 기록되는 점에 유의하십시오.

베이스를 사용하는 여러 마이그레이션이있는 경우에는 위의 init () 코드를 가진 기초 마이그레이션 클래스를 생성 할 것을 권장합니다. 그러면 각각의 마이그레이션 클래스는 기본 클래스에서 확장 할 수 있습니다.

이렇게하면 특정 데이터베이스에 적용되어야 마이그레이션을 작성하기 위해서는 해당 기초 마이그레이션 클래스에서 확장하는 것만으로 끝납니다. 이제 yii migrate 명령을 실행하면 모든 이주 해당 데이터베이스에 적용되게됩니다.

    Tip | 팁 : 다른 데이터베이스를 조작하기 위해서는, [yii \ db \ Migration :: db | db] 속성을 설정하는 것 외에도 마이그레이션 클래스에서 새로운 데이터베이스 연결을 생성하는 방법이 있습니다 . 그러면 데이터베이스 연결에서 DAO 메소드를 사용하여 다른 데이터베이스를 조작 할 수 있습니다.

여러 데이터베이스에 대해 이주를 적용하기 위해 채용 할 또 다른 전략으로는 다른 데이터베이스에 대한 마이그레이션은 다른 마이그레이션 경로를 유지한다는 것입니다. 그러면 다음과 같이 서로 다른 데이터베이스 이주를 별도의 명령에 적용 할 수 있습니다.

yii migrate --migrationPath = @ app / migrations / db1 --db = db1
yii migrate --migrationPath = @ app / migrations / db2 --db = db2
...

첫 번째 명령은 @ app / migrations / db1의 마이그레이션을 db1 데이터베이스에 적용하고 두 번째 명령은 @ app / migrations / db2에있는 이주 db2 데이터베이스에 적용하기는 식입니다.