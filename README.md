# Django_mysite_books
for my project

Django의 MVT패턴
===============

### Model, View, Template 로 구분하여 한 요소가 다른 요소에 영향을 주지 않도록 설계한 방식.

### 예를 들면, 모델은 블로그의 내용을 데이터베이스로부터 가지고 오거나 저장, 수정하는 기능을, 뷰는 버튼을 눌렀을 때 어떤 함수를 호출하여 데이터를 어떻게 가공할 것인지 결정하는 역할을, 템플릿은 화면 줄력을 위해 디자인과 테마를 적용해서 보여지는 페이지를 만들어주는 역할을 담당한다.

### 웹 클라이언트의 요청을 받고, 장고에서 MVT패턴에 따라 처리하는 과정을 요약하면 다음과 같다.

* 클라이언트로 부터 요청을 받으면 URLconf를 이용하여 URL을 분석한다.
* URL 분석 결과를 통해 해당 URL에 대한 처리를 담당할 뷰를 결정한다.
* 뷰는 자신의 로직을 실행하면서, 데이터베이스 처리가 필요하면 모델을 통해 처리하고 결과를 반환 받는다.
* 뷰는 자신의 로직처리가 끝나면 템플릿을 사용하여 클라이언트에게 전송할 HTML파일을 생성한다.
* 뷰는 최종 결과로 HTML 파일을 클라이언트에세 보내 응답한다.

---------------------------------------

> ## Model - 데이터 베이스 정의
> ### 모델이란 사용될 데이터에 대한 정의를 담고 있는 장고의 클래스이다. 장고는 QRM 기법을 사용하여 애플리케이션에서 사용할 데이터베이스를 클래스로 매핑해서 코딩할 수 있다. 즉, 하나의 클래스는 하나의 테이블에 매핑되고, 모델 클래스의 속성은 테이블의 컬럼에 매핑된다.
> * QRM이란, 객체와 관계형 데이터베이스를 연결해주는 역할을 한다.
>
> Ex) Person 클래스 정의 
<pre>
<code>
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
</code>
</pre>
> 이 Person 모델은 다음의 SQL문과 같다.
<pre>
<code>
CREATE TABLE Person(
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOR NULL
);
</code>
</pre>
> Primary Key는 클래스에서 정의하지 않아도 장고에서 자동으로 부여해 준다. 굳이 하려면 직접 지정할 수도 있다.

-------------------------------------

>## URLconf - URL정의
>### 클라이언트로부터 요청을 받으면 장고는 가장 먼저 요청에 들어있는 URL을 분석한다. 즉 요청에 들어있는 URL이 urls.py에 정의된 URL패턴과 매칭되는지를 분석한다. 이러한 방식은 자바나 PHP보다 직관적이고 이해하기가 쉽다. URL을 정의하기 위해서는 아래 코드처럼 urls.py 파일에 URL과 처리함수(View라고 부름)를 매핑하는 파이썬 코드를 작성하면 된다.
<pre>
<code>
from django.urls import path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>', vies.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
</code>
</pre>
> 위 코드에서 articles/2003/ 부분이 URL이고 views.special_case_2003 부분이 처리함수(뷰)이다.
>### 웹 클라이언트가 웹서버에 페이지 요청 시, 장고에서 URL을 분석하는 순서를 간단히 요약하면 아래와 같다.
> * setting.py 파일의 ROOT_URLCONF 항목을 읽어 최상위 URLconf(urls.py)의 위치를 알아낸다.
> * URLconf를 코딩하여 urlpatterns 변수에 지정되어 있는 URL 리스트를 검사한다.
> * 위에서부터 순서대로 URL리스트의 내용을 검사하면서 URL 패턴이 매치되면 검사를 종료한다.
> * 매치된 URL의 뷰를 호출 한다. 여기서 뷰는 함수 또는 클래스의 메소드이다. 호출시 HttpRequest 객체와 매칭할 때 추출된 단어들을 뷰에 인자로 넘겨준다.
> * URL 리스트를 끝까지 검사했는데도 매칭에 실패하면 에러를 처리하는 뷰를 호출한다.
>
>### URL패턴을 정의할 때 <int:year>처럼 꺾쇠를 사용하는 부분이 있는데, 이것은 URL패턴의 일부 문자열을 추출하기 위한 것이먀, <Type:name>형식으로 사용된다. 매치된 경우에는 매치된 문자열을 인자명 year에 할당한다. 즉 views.year_archive(request, year=2018)처럼 호출한다.
>### 꺾쇠 부분을 장고에서는 Path Converter라고 부르는데, 여기에 사용되는 타입은 아래와 같다. 개발자가 추가로 타입을 등록할 수 있다.
> * str : /를 제외한 모든 문자열과 매치된다. 디폴트 값이다.
> * int : 0또는 양의 정수와 매치되고, 매치된 정수는 파이썬의 int타입으로 변환된다.
> * slug : slug형식의 문자열(ASCII, 숫자, 하이픈, 밑줄로만 구성됨)과 매치된다.
> * uuid : UUID형식의 문자열과 매치됨.
> * path : /를 포함한 모든 문자열과 매치됨. 이는 URL패턴의 일부가 아니라 전체를 추출하고자 할 때 많이 사용됨.
> ### 참고로 URL패턴에 정규 표현식을 사용할 수 있다. 
----------------------------------------------------------------
>## View - 로직 정의
>### Django는 웹요청에 있는 URL을 분석하고, 그 결과로 해당 URL에 매핑된 뷰를 호출한다. 
>### 일반적으로 뷰는 웹 요청을 받아서 데이터베이스 접속 등 해당 애플리케이션의 로직에 맞는 처리를 하고, 그 결과 데이터를 HTML로 변환하기 위하여 템플릿 처리를 한 후에, 최종 HTML로 된 응답 데이터를 웹 클라이언트로 반환하는 역할을 한다.
>### Django에서 뷰는 함수 또는 클래스의 메소드로 작성되며, 웹 요청을 받고 응답을 반환해준다. 응답은 HTML 데이터일 수도 있고, 리다이렉션 명령일 수도 있고, 404에러 메세지 일 수 도 있다. 아래 코드는 현재 날짜와 시간을 HTML로 반환해주는 views.py이다.
<pre>
<code>
from django.http import HttpResponse
import datetime # 시간

def current_datetime(request):
    now = datetime.datetime.now()
    html = "<html><body>It is now %s.</body></html>" % now
    return HttpResponse(html)
</code>
</pre>
>### 위에 코드는 함수로 작성한 예시이고, 첫번째 인자로 HttpRequest 객체를 받는다. 그리고 처리가 끝난 후 다시 반환해 준다. 에러를 반환해주려면 HttpResponseNotFound같은 에러 객체를 반환하면 된다.
----------------------------------------------------------------
>## Template - 화면 UI 정의
>### Django가 클라이언트에게 반환하는 최종응답은 HTML텍스트이다. 개발자가 응답에 사용할 ~.html 파일 작성하면, Django는 이를 해석해서 최종 HTML 텍스트 응답을 생성하고, 이를 클라이언트에게 보내줍니다. 클라이언트는 응답으로 받은 HTML 텍스트를 해석해서 우리가 보는 웹 브라우저 화면에 UI를 보여주는 것이다.
>### 이런 과정에서 개발자가 작성하는 ~.html 파일을 템플릿이라 부르며, 여기에 화면 UI 모습을 템플릿 문법에 맞게 작성한다.
----------------------------------------------------------------
>## MVT 코딩 순서
> * 프로젝트 뼈대 만들기 : 프로젝트 및 앱 개발에 필요한 디렉토리와 파일 생성
> * 모델 코딩하기 : 테이블 관련 사항을 개발(models.py, admin.py)
> * URLconf 코딩하기 : URL 및 뷰 매핑 관계를 정의(urls.py)
> * 템플릿 코딩하기 : 화면 UI 개발(templates.py)
> * 뷰 코딩하기 : 애플리케이션 로직 개발(views.py)



------------------------------

####장고에서 인공지능 모델을 공부하려면 https://gaussian37.github.io/ml-sklearn-saving-model/ 이분의 깃허브를 참고해서 모델을 피클파일로 저장하여 자신의 장고에서 load를 하면 된다.
-----------------------------------
보고 배운 책 : 파이썬 웹 프로그래밍
