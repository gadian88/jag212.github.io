---
layout: post
title:  "Oracle DB의 페이징 처리2"
date:   2015-04-05 18:00:00
categories: tech
tags: Database
---

이번 포스팅에서는 저번 포스팅에 이어서 Oracle DB에서의 페이징 처리에 대해 다루겠습니다. 저번 포스팅에서는 2가지 종류의 페이징 처리 기법에 대해
다루어보았는데, 이번 포스팅에서는 각 처리 기법의 성능과 CUBRID에서의 페이징 처리에 대해 살펴보겠습니다.

---

##1. CUBRID에서의 페이징처리
먼저, 저번 Oracle DB에서 페이징 처리에서 살펴본 것과 같이 ROWNUM을 이용하는 방법은 동일하게 사용가능 합니다. 즉, 다음 sql문은 CUBRID에서도 동일하게
사용할 수 있습니다.

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
{% endhighlight %}

다음으로 CUBRID 만의 페이징 처리 방법을 보겠습니다. CUBRID의 개발자 가이드에서는 원하는 id의 값을 indexing 하여 페이징 처리를 권하고 있습니다.
따라서 indexing이 이루어져 있고, 게시판에서와 같이 글이 순차적으로 증가(auto increment)하는 값이라면 최적의 성능을 내면서
다음과 같은 sql문으로 select 하는 것이 가능합니다.

{% highlight sql %}
--  10에서 30번까지 select
SELECT id, title, content FROM page_table
ORDER BY id for ORDERBY_NUM() BETWEEN 10 AND 30;
{% endhighlight %}

앞선 포스팅에서도 말씀드렸다시피, 페이징 처리는 DBMS마다 차이가 있으니, 각자 사용하시는 DBMS에서 가장 효율과 성능 좋은 방법을 사용하시면 됩니다.^^
그럼 이제 앞서 포스팅의 2가지 페이징 처리 방법의 성능에 대해 살펴보겠습니다.

---

##2. Oracle의 페이징 성능 비교
(이 성능 비교는 직접 테스트해볼 DB가 구성되어있지 않았기 때문에(!), 웹에서 참조하였습니다.)
  
먼저 ROW_NUMBER() 함수를 이용했을 때의 페이징 처리 성능을 보시겠습니다.

{% highlight sql %}
SELECT * FROM (
	SELECT A.*, ROW_NUMBER() OVER(ORDER BY ID DESC) AS NUM 
	FROM OP_SAMPLE A
)
WHERE NUM BETWEEN 999991 AND 1000000;
{% endhighlight %}
 
위의 경우 데이터가 100만 건인 경우 수행시간 : 1.136초, 10만 건인 경우 수행시간 : 0.253초가 소요되었습니다.
  
그럼, 다음으로 일반적인 방법인 ROWNUM을 이용해서 페이징 처리를 할 때의 성능을 살펴보겠습니다.

{% highlight sql %}
SELECT * FROM ( 
	SELECT ROWNUM AS RNUM, Z.* FROM ( 
		SELECT * FROM OP_SAMPLE ORDER BY ID DESC
	) Z WHERE ROWNUM <= 1000000 
)
WHERE RNUM >= 999991;
{% endhighlight %}

이 경우, 100만 건인 경우 수행시간 : 0.826초, 10만 건인 경우 수행시간 : 0.087초가 소요되었다고 합니다. 확실히 함수를 통하는 방법보다는 query문이 복잡하긴 하지만
10만건의 데이터의 경우 거의 1/3로 시간이 줄어들만큼, 성능면에서는 향상이 있는 것을 알 수 있습니다.
  그럼 마지막으로 ROWNUM과 index를 동시에 사용하여 가장 최적의 성능을 낼 경우의 시간을 보도록 하겠습니다.

{% highlight sql %}
SELECT * FROM ( 
	SELECT /*+ INDEX_DESC(Z OP_SAMPLE_PK) */ ROWNUM AS RNUM, Z.* FROM ( 
		SELECT * FROM OP_SAMPLE
	) Z WHERE ROWNUM <= 1000000 
)
WHERE RNUM >= 999991;
{% endhighlight %}

최적의 성능을 내는 쿼리문 답게 100만 건인 경우 수행시간 : 0.687초, 10만 건인 경우 수행시간 : 0.079초가 소요되었습니다. 사실 query문 자체는 위와 다를 것은
없지만 indexing의 중요성을 다시 한번 느끼게 되었습니다. 앞으로 DB를 사용하시면서 최적의 성능을 위해 indexing 처리를 적절하게 사용하시는 것이 좋겠습니다.

  
---

지금까지 Oracle DB에서의 페이징 처리와 각각의 성능에 대해 알아보았습니다. 웹을 하다보면 게시판 등에서 페이징 처리가 상당히 자주 쓰이는데,
이 포스팅이 DB 페이징 처리에 조금이나마 도움이 되었으면 좋겠습니다.^^