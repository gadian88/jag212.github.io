---
layout: post
title:  "DB MERGE INTO 구문 사용해보기"
date:   2015-03-29 18:00:00
categories: tech
tags: Database
---

이번 포스팅은 Database를 운용하다가 직접 겪어본 상황을 좀 더 효율적으로 사용하는 방법은 없을까를 찾아보던 중 알게된 MERGE INTO 구문을 소개해드리고자 작성하였습니다.
종종 DB를 운용하다보면 다음과 같은 상황을 구현해야하는 경우가 종종 있습니다.
'이미 해당하는 값이 존재하면 update, 값이 없다면 insert..'
저같은 경우 신입사원 프로젝트였던 SharpNote를 진행하면서 이와 동일한 상황을 맞게 되었고, 여러가지 방법을 고민하게 되었습니다.
  
가장 흔하면서 초보적으로 생각할 수 있는 방법은 바로 JAVA단에서 직접 처리하는 방법입니다. 먼저, select로 count를 가지고 온 뒤, count 값이 >0면 update를 실행하고,
count의 값이 =0이면 insert를 실행하는 방법으로 굉장히 효율이 떨어지는 방법입니다. 
  
두번째로 생각해볼 수 있는 방법은 위에서 처리하던 방법을 DB에게 맡기는 방법으로, pl/sql 등을 이용해 방법은 동일하지만 DB가 모두 처리하는 방식입니다. 혹은 mybatis의 동적 SQL을 이용하는 방법도 있을 수 있겠지요.
하지만 이 또한 단일 쿼리가 아니고 코드가 복잡해지는 단점이 있습니다. 그렇기 때문에 마지막으로 제가 웹 검색을 통해 알아낸 방법을 소개드리도록 하겠습니다. 

---

##1. ON DUPLICATE KEY UPDATE 사용해보기
이 구문은 mysql에서 자주 사용하는 구문으로 CUBRID에서도 사용가능합니다. 다음 예제를 보시겠습니다.

{% highlight sql %}
INSERT INTO students (NAME, email) VALUES ('apple', 'apple@naver.com') 
 ON DUPLICATE KEY UPDATE name='apple', email='apple@nhnent.com';
{% endhighlight %}

위와 같이 쿼리를 작성하면 'apple'이라는 key 값이 존재할 경우 email이 'apple@nhnent.com'로 update 되고 'apple'이라는 key가 없다면 'apple@naver.com'
로 새로운 값이 insert 되게 됩니다. 하지만 이 구문의 단점이 있다면 유니크 인덱스 또는 프라이머리 키와 중복되는 경우에만 사용을 할 수가 있었습니다.
SharpNote에서 제가 태그를 DB에 넣기 위해 사용한 테이블에는 key가 따로 존재하는 것이 아니었기에 이를 사용하기 위해서는 테이블을 다시 고쳐야하는 문제가 있었습니다.
그렇기 때문에 다른 방법을 고민하던 중 새로운 방법을 알게 되었습니다.

---

##2. MERGE INTO 사용해보기
다음으로 알아낸 방법은 바로 MERGE INTO 구문을 이용하는 방법으로, 이는 Oracle DB에서 위와 같은 상황에 이용하는 구문인데, CUBRID에서도 이를 지원하였기에 SharpNote 프로젝트 중에는 이를 이용한 적이 있습니다.
MERGE INTO는 다음과 같이 구문을 작성하시면 됩니다.

{% highlight sql %}
MERGE INTO table_name alias
            USING (table | view | subquery) alias                -- 하나의 테이블만 이용한다면 DUAL 활용
                 ON (join condition)                                     -- WHERE절에 조건 쓰듯이
            WHEN MATCHED THEN                                   -- ON 이하의 조건에 해당하는 데이터가 있는 경우 
                     UPDATE SET col1 = val1[, ...]                -- UPDATE 실행
            WHEN NOT MATCHED THEN                           -- ON 이하의 조건에 해당하는 데이터가 없는 경우
                     INSERT (column lists) VALUES (values);  -- INSERT 실행
{% endhighlight %}
  
간단히 예를 살펴보겠습니다.
  
####예1)
{% highlight sql %}
MERGE INTO copy_emp c
            USING employees e
            ON (c.employee_id = 200911)
            WHEN MATCHED THEN
                      UPDATE SET
                            c.first_name = e.first_name,
                            c.last_name = e.last_name,
                            c.email = e.email,
                            ......
            WHEN NOT MATCHED THEN
                      INSERT (c.employee_id, c.first_name, c.last_name, ...... ) 
                                 VALUES (e.employee_id, e.first_name, e.last_name, ...... );
{% endhighlight %}
  
####예2)
{% highlight sql %}
MERGE INTO emp
            USING DUAL
            ON (employee_id = 200911)
            WHEN MATCHED THEN
                      UPDATE SET
                            first_name = 'John',
                            last_name = 'Petrucci',
                            email = 'dream@johnpetrucci.com',
                            ......
            WHEN NOT MATCHED THEN
                      INSERT (first_name, last_name, email, ...... ) 
                                 VALUES ('John', 'Petrucci', 'dream@johnpetrucci.com', ...... );
{% endhighlight %}
  
예제를 살펴보시면 사용자체는 크게 어렵지 않다는 것을 알 수 있습니다. ON 조건절에 해당 조건이 만족하는지 아닌지를 판단하여 MATCHED 혹은 NOT MATCHED 아래 구문이 실행되게 됩니다.
ON 조건절을 어떻게 짜느냐에 따라 굳이 key가 아니어도 원하는 기능을 구현할 수 있습니다.

---

이상으로 '이미 해당하는 값이 존재하면 update, 값이 없다면 insert..'의 상황을 구현해야하는 상황에서 MERGE INTO 구문을 이용하는 방법을 알아보았습니다.
앞서도 말씀드렸다시피 이 구문은 Oracle과 CUBRID에서는 지원하지만, 지원되지 않는 DB도 상당히 많기때문에 상황에 맞게 이용하시면 되겠습니다 ^^