개요
====

Yii 응용 프로그램은 [모델-뷰-컨트롤러 (MVC)](http://wikipedia.org/wiki/Model-view-controller)디자인 패턴에 따라 구성되어 있습니다. [모델](structure-models.md)은 데이터, 비지니스 로직, 규칙을 표현합니다. [뷰](structure-views.md)는 모델의 출력 표현입니다. 그리고 [컨트롤러](structure-controllers.md)는 입력을 받아서 [모델](structure-models.md) 과 [views](structure-views.md)로 명령을 리턴합니다.

MVC외에 Yii 응용프로그램은 다음의 요소를 가지고 있습니다.
Besides MVC, Yii applications also have the following entities:

* [시작 스크립트](structure-entry-scripts.md): 최종 사용자가 직접 엑세스 할수 있는 PHP 스크립트 입니다.
  이것은 요청 처리 주기를 시작하는 역활을 가지고 있습니다.
* [응용프록램](structure-applications.md): 전체적으로 엑세스 가능한 개체에서 응용 프로그램 구성 요소를 관리하고 사용하여 요청을 처리합니다.
* [응용프로그램 구성](structure-application-components.md): 응용 프로그램에 등록된 개체 요청에 부응하기 위해 다양한 서비스를 제공합니다.
* [모듈](structure-modules.md): 자체적으로 완전한 MVC를 포함한 독립적인 패키지입니다. 
  응용 프로그램은 여러개의 모듈로 구성할 수 있습니다.
* [필터](structure-filters.md): 각 요청이 처리되기기 전과 후에, 컨트롤에서 호출해야 하는 코드를 표시합니다.
* [위젯](structure-widgets.md): [뷰](structure-views.md)에 포함될수 있는 개체 입니다. 컨트롤러 논리를 포함할수 있으며 다른 보기에서 다시 사용 할수 있습니다.

아래 그램은 응용 프로그램의 정적 구조를 보여줍니다.

![Static Structure of Application](images/application-structure.png)
