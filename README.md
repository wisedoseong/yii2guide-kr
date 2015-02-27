
Yii 2.0 결정판 가이드
====================

이 튜토리얼은 [Yii 문서 약관 (http://www.yiiframework.com/doc/terms/)에서 릴리즈 되고 있습니다.

All Rights Reserved.

2014 (c) Yii Software LLC.


머리말
------

* [Yii 란] (intro-yii.md)
* [버전 1.1에서 업그레이드] (intro-upgrade-from-v1.md)


시작하기
--------

* [Yii 설치](start-installation.md)
* [응용 프로그램을 실행시키기](start-workflow.md)
* [Hello를 출력해 보기](start-hello.md)
* [Form 양식 작업](start-forms.md)
* [데이터베이스 작업](start-databases.md)
* [Gii 를 이용해서 코드를 생성해보기](start-gii.md)
* [앞을 보면서](start-looking-ahead.md)


응용 프로그램의 구조
----------------------

* [개요](structure-overview.md)
* [항목 스크립트](structure-entry-scripts.md)
* [응용 프로그램](structure-applications.md)
* [응용 프로그램 구성 요소](structure-application-components.md)
* [컨트롤러](structure-controllers.md)
* [모델](structure-models.md)
* [뷰](structure-views.md)
* [모듈](structure-modules.md)
* [필터](structure-filters.md)
* [위젯](structure-widgets.md)
* [애셋](structure-assets.md)
* [확장](structure-extensions.md)


요청 처리
----------------

* [개요](runtime-overview.md)
* [부트 스트랩](runtime-bootstrapping.md)
* [라우팅 및 URL 생성](runtime-routing.md)
* [요청](runtime-requests.md)
* [응답](runtime-responses.md)
* [세션과 쿠키](runtime-sessions-cookies.md)
* [오류 처리](runtime-handling-errors.md)
* [로깅](runtime-logging.md)


핵심 개념
------------

* [구성 요소](concept-components.md)
* [속성](concept-properties.md)
* [이벤트](concept-events.md)
* [동작](concept-behaviors.md)
* [구성](concept-configurations.md)
* [별칭](concept-aliases.md)
* [클래스의 자동로딩](concept-autoloading.md)
* [서비스 찾기](concept-service-locator.md)
* [의속성 주입 컨테이너](concept-di-container.md)


데이터베이스 작업
----------------------

* [데이터 액세스 개체](db-dao.md) : 데이터베이스에 연결 기본적인 쿼리 트랜잭션 및 스키마 작업
* [쿼리 빌더](db-query-builder.md) : 간단한 추상 레이어를 사용하여 데이터베이스에 대해 쿼리를 수행
* [액티브 레코드](db-active-record.md) : 액티브 레코드 ORM 레코드의 읽기 및 운영, 관계 정의
* [마이그레이션](db-migrations.md) : 팀 개발 환경에서 데이터베이스에 버전 제어를 적용
* ** 미정 ** [Sphinx](db-sphinx.md)
* ** 미정 ** [Redis](db-redis.md)
* ** 미정 ** [MongoDB](db-mongodb.md)
* ** 미정 ** [ElasticSearch](db-elasticsearch.md)


사용자 데이터 사용
----------------------

* [양식 만들기](input-forms.md)
* [입력 유효성 확인](input-validation.md)
* [파일 업로드](input-file-upload.md)
* [표 입력 데이터 수집](input-tabular-input.md)
* [다중 모델 데이터 가져 오기](input-multiple-models.md)


데이터의 표시
------------

* [데이터 형식](output-formatter.md)
* [페이지 적용](output-pagination.md)
* [정렬](output-sorting.md)
* [데이터 공급자](output-data-providers.md)
* [데이터 위젯](output-data-widgets.md)
* [클라이언트 스크립트 작업](output-client-scripts.md)
* [테마](output-theming.md)


보안
------------

* [인증](security-authentication.md)
* [권한 부여](security-authorization.md)
* [암호 취급](security-passwords.md)
* [클라이언트 인증](security-auth-clients.md)
* [모범 사례](security-best-practices.md)


캐시
----------

* [개요] (caching-overview.md)
* [데이터 캐시](caching-data.md)
* [단편 캐싱](caching-fragment.md)
* [페이지 캐시](caching-page.md)
* [HTTP 캐시](caching-http.md)


RESTful 웹 서비스
----------------------

* [빠른 시작](rest-quick-start.md)
* [자원](rest-resources.md)
* [컨트롤러](rest-controllers.md)
* [라우팅](rest-routing.md)
* [응답 형식 설정](rest-response-formatting.md)
* [인증](rest-authentication.md)
* [속도 제한](rest-rate-limiting.md)
* [버전 관리](rest-versioning.md)
* [오류 처리](rest-error-handling.md)


개발 도구
----------

* [디버그 도구 모음 및 디버거](tool-debugger.md)
* [Gii를 사용하여 코드 생성](tool-gii.md)
* ** 미정 ** [API 문서 생성](tool-api-doc.md)


테스트
------

* [개요](test-overview.md)
* [테스트 환경 설정](test-environment-setup.md)
* [단위 테스트](test-unit.md)
* [기능 테스트](test-functional.md)
* [승인 테스트](test-acceptance.md)
* [고정](test-fixtures.md)


특별 주제
------------------

* [고급 응용 프로그램 템플릿](tutorial-advanced-app.md)
* [처음부터 응용프로그램 만들기](tutorial-start-from-scratch.md)
* [콘솔 명령](tutorial-console.md)
* [코어 검사기](tutorial-core-validators.md)
* [국제화](tutorial-i18n.md)
* [메일 전송](tutorial-mailing.md)
* [성능 튜닝](tutorial-performance-tuning.md)
* [공유 호스팅 환경](tutorial-shared-hosting.md)
* [템플릿 엔진](tutorial-template-engines.md)
* [타사 코드 작업](tutorial-yii-integration.md)


위젯
------------

* GridView : ** 미정 ** 데모 페이지에 링크
* ListView : ** 미정 ** 데모 페이지에 링크
* DetailView : ** 미정 ** 데모 페이지에 링크
* ActiveFor​​m : ** 미정 ** 데모 페이지에 링크
* Pjax : ** 미정 ** 데모 페이지에 링크
* Menu : ** 미정 ** 데모 페이지에 링크
* LinkPager : ** 미정 ** 데모 페이지에 링크
* LinkSorter : ** 미정 ** 데모 페이지에 링크
* [Bootstrap 위젯](widget-bootstrap.md)
* [jQuery UI 위젯](widget-jui.md)


도우미
------

* [개요](helper-overview.md)
* [ArrayHelper](helper-array.md)
* [Html](helper-html.md)
* [Url](helper-url.md)