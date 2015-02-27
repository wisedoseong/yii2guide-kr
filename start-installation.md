Yii 설치
======================

Yii는 두 가지 방법으로 설치할 수 있습니다. 즉, [Composer](http://getcomposer.org/)를 사용하거나 아카이브 파일을 다운로드 하거나 입니다.
전자가 추천하는 방법 이며, 단 하나의 명령을 실행하여 새로운 [확장](structure-extensions.md)을 설치하고, Yii를 업데이트 할 수 있기 때문입니다.

Yii의 표준 설치를 수행하면 프레임 워크와 애플리케이션 템플릿이 모두 다운로드되고 설치됩니다.
응용 프로그램 템플릿은 몇 가지 기본적인 기능, 예를 들어 로그인이나 연락처 양식 등 작동을 구현한  Yii 응용 프로그램입니다.
그 코드는 권장하는 방법에 따라 구성되어 있습니다.
따라서 응용 프로그램 템플릿은 당신의 프로젝트를위한 출발점으로서의 좋은 역할을 할 수 있을 것입니다.

이 절과 이후의 여러 절에서 소위 *기본 응용 프로그램 템플릿* 함께 Yii를 설치하는 방법 이 템플릿에서 새 기능을 구현하는 방법을 설명합니다.
Yii는 또 다른, [고급 응용 프로그램 템플릿](tutorial-advanced-app.md)도 제공합니다.
이곳은 팀 개발 환경에서 다중 구조의 응용 프로그램을 개발할 때 사용하는 것이 바람직합니다.

> Info: 기본 응용 프로그램 템플릿은 웹 애플리케이션의 90 %를 개발하는 데 적합합니다.
  고급 응용 프로그램 템플릿과의 주요 차이점은 코드가 어떻게 구성되어 있는가하는 점입니다.
  당신이 Yii가 처음이라면 단순하면서도 충분한 기능을 가지고있는 기본 응용 프로그램 템플릿에 머무는 것을 적극 권장합니다.


Composer 의한 설치 <span id = "installing-via-composer"></span>
---------------------------

아직 Composer를 설치하지 않은 경우 [getcomposer.org](https://getcomposer.org/download/)의 가이드에 따라 설치 할 수 있습니다.
Linux 및 Mac OS X에서 다음 명령을 실행합니다.

    curl -s http://getcomposer.org/installer | php
    mv composer.phar /usr/local/bin/composer

Windows에서 [Composer-Setup.exe](https://getcomposer.org/Composer-Setup.exe)을 다운로드하고 실행합니다.

문제가 생겼을 때나 Composer 사용법에 대해 더 배우고 싶은 경우에는 [Composer 문서](https://getcomposer.org/doc/)를 참조하십시오.

이전 Composer를 설치 한 적이 있다면 가장 최신 버전을 사용하도록하십시오.
Composer는`composer self-update` 명령을 실행하여 업데이트 할 수 있습니다.

Composer가 설치되면 웹에서 액세스 할 수있는 폴더에서 다음 명령을 실행하여 Yii를 설치할 수 있습니다.

    composer global require "fxp/composer-asset-plugin:1.0.0"
    composer create-project --prefer-dist yiisoft/yii2-app-basic basic

첫 번째 명령은 [composer 에셋 플러그인](https://github.com/francoispluchino/composer-asset-plugin/)을 설치합니다.
그러면 Composer를 통해 bower와 npm의 의존 패키지를 관리 할 수​​있게합니다.
이 명령은 한 번만 실행하면 충분합니다.
두 번째 명령은`basic`라는 디렉토리에 Yii를 설치합니다.
필요하다면 다른 디렉토리 이름을 선택할 수 있습니다.

> Note : 설치 중에 Composer가 당신의 Github의 로그인 인증 정보를 요구할 수 있을지도 모릅니다.
> 이것은 Comoser 의존 패키지 정보를 Github에서 읽기 위해 충분한 API 속도를 필요로하는 위한 일렬의 순서입니다.
> 자세한 내용은 [Composer 문서](https://getcomposer.org/doc/articles/troubleshooting.md#api-rate-limit-and-oauth-tokens)를 참조하십시오.

> Tip : Yii의 최신 개발 버전을 설치 하려는 경우 [st​​ability option](https://getcomposer.org/doc/04-schema.md#minimum-stability)를 추가 후 다음 명령을 대신 에 사용할 수 있습니다.
>
> composer create-project --prefer-dist --stability=dev yiisoft/yii2-app-basic basic
>
> 개발 버전은 정상적으로 실행되는 당신의 코드를 불능으로 만들수 있으므로, 실제 개발 환경에서 사용되지 않도록 주의 해 주세요.

아카이브 파일에서 설치할 경우 <span id = "installing-from-archive-file"></span>
--------------------------------------

아카이브 파일에서 Yii를 설치하려면 세 단계를 밟습니다.

1. [yiiframework.com](http://www.yiiframework.com/download/)에서 아카이브 파일을 다운로드한다.
2. 다운로드 한 파일을 웹에서 액세스 할 수있는 폴더에 압축을 풀어줍니다..
3.`config/web.php` 파일을 편집하여`cookieValidationKey`라는 구성 정보 항목에 비밀 키를 입력
   (Composer를 사용하여 Yii를 설치하는 때에는이를 자동으로 실행됩니다).

   ```php
   // !!! 아래에 (만약 공백이라면) 비밀 키를 입력 - 이것은 쿠키 확인을 위해 필요
   'cookieValidationKey'=> '여기에 비밀키를 입력'
   ```


기타 설치 옵션 <span id = "other-installation-options"></span>
--------------------------

위의 설치 방법에 대한 설명은 Yii의 설치 방법을 나타내는 것이지만, 그것은 동시에 즉시 동작하는 기본적인 웹 애플리케이션을 작성하는 것도 있습니다.
이것은 규모에 관계없이 대부분의 프로젝트를 시작하기에 좋은 방법입니다.
특히 Yii 학습을 시작한지​​ 얼마 안된 경우에는이 방법이 적합합니다.

하지만 기타 설치 옵션도 사용할 수 있습니다.

* 코어 프레임 워크 만 설치후 전체 응용 프로그램을 처음부터 구축하려면 [처음부터 응용 프로그램을 구축](tutorial-start-from-scratch.md)
  에 설명 된 내용에 따라 생성 할수 있습니다.
* 더 정교한 팀 개발 환경에 따라 적합한 응용 프로그램에서 시작하려면 [고급 응용 프로그램 템플릿](tutorial-advanced-app.md)
  를 설치하는 것을 고려 할 수 있습니다.


설치 확인 <span id = "verifying-installation"></span>
----------------------

설치가 완료되면 다음 URL에서 설치된 Yii 응용프로그램에 접속 할 수 있습니다.

```
http://localhost/basic/web/index.php
```

이 URL은 당신이 Yii를 웹 서버의 루트 디렉토리 바로 아래의`basic`라는 디렉토리에 설치 한 것 이며,
웹 서버가 로컬 컴퓨터 (`localhost`)에서 작동되고 있다는 것을 보여주는 것입니다.
당신의 설정 환경에 맞게 URL을 변경할 필요가 있을수도 있습니다.

![Yii가 정상으로 설치된 화면](images/start-app-installed.png)

브라우저에 위와 같은 "Congratulations!"페이지가 나타날 것입니다.
만약 나타나지 않으면, PHP의 설치가 Yii의 요구 사항을 충족하는지 확인하십시오.
최소한의 요구 사항을 충족하는지 여부는 다음 방법 중 하나를 통해 확인하실 수 있습니다.

* 브라우저를 사용하여 URL`http://localhost/basic/requirements.php`에 액세스한다.
* commands 에서 다음 명령을 실행한다.

  ```
  cd basic
  php requirements.php
  ```

Yii의 최소 요구 사항을 충족후에 PHP 설치를 구성해야합니다.
가장 중요한 것은 PHP 5.4 이상이어야한다는 것입니다.
또한 응용 프로그램이 데이터베이스를 필요로하는 경우 [PDO PHP 확장](http://www.php.net/manual/ja/pdo.installation.php) 및 해당 데이터베이스 드라이버 (MySQL 데이터베이스를위한` pdo_mysql` 등)을 설치해야합니다.


웹 서버 구성 <span id = "configuring-web-servers"></span>
----------------------

> Info : 만약 Yii를 테스르만 하고 있을뿐 , 운영 서버에 배포 할 의도가 없다면, 지금은 이 섹션을 생략해도 괜찮습니다.

위의 설명에 따라 설치된 응용 프로그램, [Apache HTTP 서버](http://httpd.apache.org/) 및 [Nginx HTTP 서버](http://nginx.org/) 또는 Windows , Mac OS X, Linux에서 PHP 5.4 이상을 실행하고있는 환경이면 상관없이 그대로 작동합니다.
또한 Yii 2.0은 facebook에서 [HHVM](http://hhvm.com/)와 호환이 가능합니다.
그러나 HHVM가 네이티브 PHP와 다른 행동을하는 특수한 경우도 일부 있으므로 HHVM을 사용할 때는 약간 특별히 주의를 기울여야합니다.

운영 서버에서는`http://www.example.com/basic/web/index.php` 대신에`ttp://www.example.com/index.php` URL에서 응용 프로그램에 액세스 할 수 같이 웹 서버를 설정하고 싶은 것입니다.
그러한 설정을하기 위해서는 웹 서버의 문서 루트를`basic/web` 폴더로 향하는 것이 필요합니다.
또한 [라우팅 및 URL 생성](runtime-routing.md) 절에서 언급 된 바와 같이, URL에서`index.php`을 숨기고 싶다고 생각 하겠지​​요.
이 절에서는 이러한 목적을 달성하기 위해 Apache 또는 Nginx 서버를 어떻게 설정하면 좋은가를 배우고 있습니다.

> Info: `basic/web`를 문서 루트로 설정하는 것은`basic/web` 형제 디렉토리에 저장된 개인 응용 프로그램 코드 나 공개 할 수없는 데이터 파일에 최종 사용자가 액세스하는 것을 방지하기 위해 사용되기도 합니다.
`basic/web` 이외의 폴더에 대한 액세스를 거부하는 것은 보안 강화 중 하나입니다.

> Info: 당신은 웹 서버의 설정을 수정할 권한이없는 공유 호스팅 환경에서 응용 프로그램이 운영되는  경우에도 보안 강화를 위해 응용 프로그램의 구조를 조정할 할수 있습니다.
자세한 내용은 [공유 호스팅 환경](tutorial-shared-hosting.md) 절을 참조하십시오.


###  Apache 구성 권장 <span id = "recommended-apache-configuration"></span>

아래의 설정을 Apache의`httpd.conf` 파일 또는 가상 호스트 설정에서 사용합니다.
`path/to/basic/web` 부분을`basic/web`의 실제 경로로 대체해야한다는 것에주의하십시오.

```
# 문서 루트를 "basic/web"로 설정
DocumentRoot "path/to/basic/web"

<Directory "path/to/basic/web">
    # 깨끗한 URL을 지원하기 위해 mod_rewrite를 사용
    RewriteEngine on
    # 디렉토리 나 파일이 존재하는 경우 요청을 그대로 통과
    RewriteCond % {REQUEST_FILENAME}! -f
    RewriteCond % {REQUEST_FILENAME}! -d
    # 그렇지 않으면 요청을 index.php에 보낸다
    RewriteRule. index.php

    ... 기타 설정 ...
</Directory>
```


### Nginx 구성 권장 <span id = "recommended-nginx-configuration"></span>

[Nginx](http://wiki.nginx.org/)를 사용하기 위해서는 PHP를 [FPM SAPI](http://jp1.php.net/install.fpm)로 설치해야합니다 .
아래의 Nginx 설정을 사용할 수 있습니다.
`path/to/basic/web` 부분을`basic/web`의 실제 경로로 바꾸고`mysite.local`를 실제 서버의 호스트 이름으로 대체하십시오.

```
server {
    charset utf-8;
    client_max_body_size 128M;

    listen 80; ## listen for ipv4
    #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

    server_name mysite.local;
    root        /path/to/basic/web;
    index       index.php;

    access_log  /path/to/basic/log/access.log main;
    error_log   /path/to/basic/log/error.log;

    location / {
        # 실제 파일이 아닌 것은 모두 index.php로 리디렉션
        try_files $uri $uri/ /index.php?$args;
    }

    # Yii가 존재하지 않는 정적 파일을 호출 하여 처리 하지 않게 하려면 아래 주석을 삭제
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #    try_files $uri =404;
    #}
    #error_page 404 /404.html;

    location ~ \.php$ {
        include fastcgi.conf;
        fastcgi_pass   127.0.0.1:9000;
        #fastcgi_pass unix:/var/run/php5-fpm.sock;
        try_files $uri =404;
    }

    location ~ /\.(ht|svn|git) {
        deny all;
    }
}
```

이 구성을 사용하면 다수의 불필요한`stat ()`시스템 호출을 피하기 위해`php.ini` 파일에서`cgi.fix_pathinfo=0`을 동시에 설정해야한다.

또한 HTTPS 서버를 실행하고있는 경우에는 안전한 연결임을 Yii가 제대로 감지 할 수 있도록`fastcgi_param HTTPS on;`를 추가해야 한다는 것에도 주의를 기울여야합니다.