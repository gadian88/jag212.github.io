---
layout: post
title:  "Oracle DB의 페이징 처리"
date:   2015-04-05 18:00:00
categories: tech
tags: Database
---

흔히들 게시판 류의 웹페이지를 만들다 보면 페이징 처리를 해야하는 경우가 많이 생깁니다. 주로 이는 DB에서 한 페이지에 들어갈만큼의 List를 가져다가
출력하는 방법으로 구현하는데, 이 포스팅에서는 Oracle에서의 페이징 처리를 위한 select 방법들에 대해 알아보겠습니다.

---

##1. ROWNUM 이용하기
이 방법은 Oracle에서 페이징 처리를 위해 주로 사용되는 방법입니다. 예를 보여드리자면 다음과 같습니다.(한 페이지에 20개씩 출력한다고 가정시)

{% highlight sql %}
-- 1페이지 
select *
from (SELECT *
		FROM (
  			SELECT rownum rnum, id, title, content
    		FROM page_table
			) pagetable
		where rnum <= 20
	)
where rnum >= 1;
-- 2페이지
select *
from (SELECT *
		FROM (
  			SELECT rownum rnum, id, title, content
    		FROM page_table
			) pagetable
		where rnum <= 40
	)
where rnum >= 21;
{% endhighlight %}

저도 이 방법을 주로 사용하여 페이징 처리를 구현해왔는데, 이 쿼리를 작성할 때마다 반드시 여러 subquery를 중첩할 수 밖에 없는 구조라
왠지 불편하다고 생각을 하게 되었습니다. 이유는 rownum이 갖는 특징 때문으로, 하나의 쿼리에서 rownum이 몇보다 크다(>)와 같은 조건을 걸어 select가 제대로 되지 않기
때문입니다. 그렇기 때문에 저는 좀 더 편한 방법이 없을까를 찾던 중 다음과 같은 방법을 찾았습니다.

---

##2. ROW\_NUMBER() 이용하기
위의 ROWNUM 방법 보다 좀 더 보기 좋은 query를 위해 찾아낸 방법은 바로 ROW\_NUMBER() 함수를 이용하는 방법으로,
이는 Oracle DB에서 rownum을 가지고 위와 비슷한 연산을 할때 이용하는 함수입니다. 예를 보겠습니다.

{% highlight sql %}
SELECT * FROM (
	SELECT p.*, ROW_NUMBER() OVER(ORDER BY id DESC) AS RNUM
	FROM page_table p
)
WHERE RNUM BETWEEN 1 AND 20;
{% endhighlight %}
  
처음의 방법보다 subquery가 하나는 줄어든 것을 알 수 있습니다. 또한 BETWEEN을 사용하니 좀 더 명확한 느낌도 드는군요. 
저는 처음에 ROW\_NUMBER()보다는 RANK()를 많이 사용했는데, 둘의 차이가 있다면 RANK()는 동일한 값이 있을 경우 같은 값 출력(공동 2등이 존재할 수도 있다).
ROW\_NUMBER()는 동일한 값이 존재해도 반드시 순위가 갈린다는 점이 차이점입니다. 따라서 주로 ROW\_NUMBER()을 사용하되, unique 속성과 같이 중복값이 없는
게시글 번호 등의 경우 어느 것을 사용해도 크게 문제가 되진 않습니다.
  
---

이상으로 Oracle에서 페이징 처리하는 방법을 2가지 살펴보았습니다. 2번째 방법이 좀 더 편리해보이지만, 사실 2번째 방법이 자주 이용되지 않는 이유는
성능상의 차이가 있기 때문입니다. 다음 포스팅에서는 위 둘의 성능상 차이점과 CUBRID에서의 페이징 처리 방법을 살펴보도록 하겠습니다.