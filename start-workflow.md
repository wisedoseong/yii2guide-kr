응용 프로그램을 실행
====================

Yii의 설치가 끝나면, 실제로 동작하는 Yii 응용 프로그램에 접속 할 수 있습니다.
그 URL은`http://hostname/basic/web/index.php` 또는`http://hostname/index.php` 등 설정에 따라 다릅니다.
이 절에서는 응용 프로그램에 내장 된 기능을 소개하고, 코드가 어떻게 구성되어 있는지, 그리고 일반적으로 응용 프로그램이 요청을 어떻게 처리 하는지를 설명합니다.

> Info: 이야기를 쉽게하기 위해이 "시작하기"튜토리얼을 통해`basic/web`을 웹 서버의 document  루트로 설정했다고 가정합니다.
  그리고 응용 프로그램에 액세스하기위한 URL은`http://hostname/index.php` 또는 이와 유사한 것이되도록 설정했다고 가정합니다.
  필요에 따라 설명의 URL을 바꾸십시오.


기능 <span id = "functionality"></span>
----

설치된 기본적인 응용 프로그램은 네 개의 페이지가 있습니다.

* 홈페이지 :`http://hostname/index.php`의 URL에 접속 할 때 표시됩니다.
* "정보"페이지.
* "연락처"페이지 : 최종 사용자가 이메일을 통하여 당신에게 연락을 취할 수있는 연락처 양식이 표시됩니다.
* "로그인"페이지 : 최종 사용자를 인증하기위한 로그인 양식이 표시됩니다.
  "admin/admin"으로 로그인하여보십시오.
  "로그인"주 메뉴 항목이 "로그 아웃"에 바뀌는 것을 볼 것입니다.

이 페이지는 공통의 상단과 하단을 가지고 있습니다.
헤더는 다른 페이지 사이를 오가는 것을 허용하는 주 메​​뉴 바 가  있습니다.

브라우저 창 하단에 도구 모음을 볼수가 있습니다.
이것은 Yii에서 제공하는 유용한 디버그 도구 모음 (tool-debugger.md)이며, 많은 디버깅 정보, 예를 들어, 로그 메시지 응답의 상태, 실행 된 데이터베이스 쿼리 등을 기록하여 표시하는 것 입니다.



응용 프로그램의 구조 <span id = "application-structure"></span>
----------------------

응용 프로그램에서 가장 중요한 디렉토리와 파일 (응용 프로그램 루트 디렉토리가`basic`이라고 가정하면) 다음과 같습니다.

```
basic/                  응용 프로그램의 기본 경로
    composer.json       패키지정보를 설명하며 Composer에 의해 사용됩니다
    config/             응용 프로그램 기타 구성 정보를 저장
        console.php     콘솔 응용 프로그램의 구성 정보
        web.php         웹 응용 프로그램의 구성 정보
    commands/           콘솔 명령 클래스를 저장
    controllers/        컨트롤러 클래스를 포함
    models/             모델 클래스를 포함
    runtime/            실행시 Yii 의해 생성 된 파일 (로그 및 캐시 등)를 저장
    vendor/             설치된 Composer 패키지 (Yii 프레임 워크 자체 포함)를 저장
    views/              뷰 파일을 저장
    web/                응용 프로그램의 웹 루트. 웹에서 액세스 할 수있는 파일을 저장
        assets/         Yii 의해 발행되는 에셋 파일 (javascript와 CSS)를 저장
        index.php       응용 프로그램의 시작 스크립트 (부트 스트랩 스크립트)
    yii                 Yii 콘솔 명령 실행 스크립트
```


일반적으로 응용 프로그램 파일은 두 종류로 나눌 수 있습니다. 즉`basic/web` 아래에있는 파일 및 기타 디렉토리 아래에있는 파일입니다.
전자는 HTTP (즉 브라우저에서) 직접 액세스 할 수 있지만, 후자는 직접 액세스 할 수 없으며, 허용해서는 안됩니다.

Yii는 [모델-뷰-컨트롤러 (MVC)](http://wikipedia.org/wiki/Model-view-controller) 디자인 패턴을 구현하고 있으며, 위의 디렉토리 구성에도 반영되어 있습니다 .
`models` 디렉토리가 모든 [모델 클래스](structure-models.md)를 포함,`views` 디렉토리가 모든 [뷰 스크립트](structure-views.md)를 포함,
`controllers` 디렉토리가 모두의 [컨트롤러 클래스](structure-controllers.md)를 포함하고 있습니다.

다음 그림이 응용 프로그램의 정적 구조를 보여줍니다.

![프로그램 정적 구조](images / application-structure.png)

각 응용 프로그램은 하나의 시작 스크립트`web/index.php`이 있습니다.
이것은 응용 프로그램 중 유일 웹에서 액세스 할 수있는 PHP 스크립트입니다.
시작 스크립트는 입력 된 요청을받은 다음 [응용 프로그램](structure-applications.md)의 인스턴스를 만듭니다.
[응용 프로그램](structure-applications.md)은 [구성 요소](concept-components.md)의 도움을 받아 요청을 해결하고 요구를 MVC에 송부합니다.
[위젯](structure-widgets.md)은 복잡하고 역동적 인 유저 인터페이스 요소를 구축하기 위해 [뷰](structure-views.md)에서 사용됩니다.


요청 라이프 사이클 <span id = "request-lifecycle"></span>
--------------------------

다음 그림은 응용 프로그램이 어떻게 요청을 처리하는 방법을 보여줍니다.

![요청 라이프 사이클](images / request-lifecycle.png)

1. 사용자가 [시작 스크립트](structure-entry-scripts.md)`web/index.php`에 요청을하고 있습니다.
2. 시작 스크립트는 응용 프로그램의 [구성 정보](concept-configurations.md)를 읽어 요청을 처리하는 [응용 프로그램](structure-applications.md)의 인스턴스를 만듭니다.
3. 응용 프로그램은 [요청](runtime-requests.md)된 응용 프로그램 구성 요소의 도움을 받아 [요청](runtime-routing.md)를 처리합니다.
4. 응용 프로그램이 요청을 처리하는 [컨트롤러](structure-controllers.md)의 인스턴스를 만듭니다.
5. [컨트롤러 액션](structure-controllers.md)의 인스턴스를 생성하고 작업에 대한 필터를 실행합니다.
6. 하나라도 필터가 실패했을 때 동작이 취소됩니다.
7. 모든 필터를 통과 한 때 액션이 실행됩니다.
8. 작업은 데이터 모델을 어쩌면 데이터베이스에서 읽습니다.
9. 작업은 데이터 모델을 뷰에 제공하여 뷰를 렌더링합니다.
10. 렌더링 결과가 [응답](runtime-responses.md) 응용 프로그램 구성 요소에 반환됩니다.
11. 응답 구성 요소가 렌더링 결과를 사용자의 브라우저에 보냅니다.