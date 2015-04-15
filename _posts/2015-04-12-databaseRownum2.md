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
따라서 indexing이 이루어져 있고, 게시판에서와 같이 글이 순차적으로 증가(auto increment)하는 값이라면 다음과 같은 sql문으로 select 하는 것이 가능합니다.

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
 ) WHERE RNUM >= 999991;
{% endhighlight %}

처음의 방법보다 subquery가 하나는 줄어든 것을 알 수 있습니다. 또한 BETWEEN을 사용하니 좀 더 명확한 느낌도 드는군요. 
저는 처음에 ROW\_NUMBER()보다는 RANK()를 많이 사용했는데, 둘의 차이가 있다면 RANK()는 동일한 값이 있을 경우 같은 값 출력(공동 2등이 존재할 수도 있다).
ROW\_NUMBER()는 동일한 값이 존재해도 반드시 순위가 갈린다는 점이 차이점입니다. 따라서 주로 ROW\_NUMBER()을 사용하되, unique 속성과 같이 중복값이 없는
게시글 번호 등의 경우 어느 것을 사용해도 크게 문제가 되진 않습니다.
  
---

이상으로 Oracle에서 페이징 처리하는 방법을 2가지 살펴보았습니다. 2번째 방법이 좀 더 편리해보이지만, 사실 2번째 방법이 자주 이용되지 않는 이유는
성능상의 차이가 있기 때문입니다. 다음 포스팅에서는 위 둘의 성능상 차이점과 CUBRID에서의 페이징 처리 방법을 살펴보도록 하겠습니다.