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



