---
layout: post
title:  "iBatis resultMap 정리"
date:   2015-06-14 18:00:00
categories: tech
tags: iBatis
---

이번 포스팅에서는 저번 포스팅에 이어서 iBatis에서 resultMap을 다루는 방법을 써볼까 합니다. 최근 개발 중 두 가지의 경험을 들어서 관련된 내용에 대해 작성을 해보고자 하는데요. 먼저 첫째로, resultMap으로 boolean 변수 받기. 둘째로, resultMap의 select 옵션 사용해보기에 대해 말해볼까 합니다.  

### resultMap에서 boolean 값 받기  
가끔 iBatis로 DB에서 값을 받아와 처리하다보면 가끔씩 boolean으로 값을 받아오면 좀 더 편했을텐데... 라는 생각을 하는 경우가 있었습니다. 굳이 값을 가져와서 이 값이 null은 아닌지, 혹은 유효한 값인지를 비교하는 코드를 작성하기보다는 처음부터 boolean을 가져와서 이를 가지고 비교를 하면 좀 더 코드가 효율적으로 작성되는 경우가 분명히 존재한다고 생각합니다. 그래서 이를 사용하는 방법을 간단히 설명드리겠습니다. 먼저 resultMap은 다음과 같이 작성합니다.
  
{% highlight xml %}
<resultMap id="resultMapId" class="Class">
	<result property="propertyName" column="COLUMN_NAME"
                 javaType="boolean"
	/>
</resultMap>
{% endhighlight %} 
  
위와 같이 javaType 옵션에 boolean이라고 선언합니다. 이렇게 하면 resultMap에서 할 일은 끝났습니다. 물론, property에 해당하는 값은 boolean 타입의 값이어야겠죠? 다음은 DB에서 어떻게 boolean 값을 select 해올 것이냐 하는 문제인데, 이는 간단합니다. 0 혹은 1 로 값을 select 해오면 됩니다. result set의 값이 0이라면 false로, 1이라면 true로 알아서 property에 set 되게 됩니다. 따라서 DB쿼리만 조금 잘 작성한다면, 보다 손쉽게 유효성을 판단하는 데이터를 select 해올 수 있을 거라고 생각합니다.


### resultMap select 옵션 이용하기  
며칠 전, DB 쿼리를 작성하다가 굉장히 복잡한 두 개의 쿼리문을 합쳐야 하는 상황이 있었습니다. 몇 시간을 고민을 하다가 결국 두 쿼리를 합치는 것은 불가능에 가깝다는 결론에 다달았고, 쿼리를 조금 분리하여 하나의 쿼리에서 나온 결과를 가지고 다른 쿼리를 다시 실행해야겠다고 생각했습니다. 하지만 이를 DAO단으로 들고와서 한 번 실행된 결과 list를 다시 for문을 돌리면서 쿼리를 다시 실행할 생각을 하니, 뭔가 굉장히 비효율적이라는 생각이 들었습니다. 그래서 resultMap의 select 옵션을 사용하기에 이르렀습니다. 물론 방식이야 쿼리를 2번씩 날린다는 점에서는 DAO단으로 들고 와서 하는 것과 같지만, 좀 더 보기에 깔끔하고 DAO단 쪽에서는 아무 처리를 안해도 된다는 장점이 있었습니다. select의 대략적인 사용법은 다음과 같습니다. 

{% highlight xml %}
<resultMap id="getResult" class="ResultClass">
	<result property="catid" column="employee_id" javaType="int"/>
	<result property="deptNum" column="employee_id" select="getDeptByEmpId"/>
</resultMap>
    
<statement id="selectEmployee" resultMAP="getResult">
	select * from employee
</statement>

<statement id="getDeptByEmpId" parameterClass="String" resultClass="int">
   select deptNum from department where employee_id = #employee_id#
</statement>
{% endhighlight %} 

위의 예를 보시면, 간단히 사용법을 이해하실 수 있을 거라고 생각합니다. select의 value에 해당하는 statement 쿼리에 selectEmployee의 결과 column인 employee_id가 들어가게 되겠죠. 이를 통해 다시 한번 getDeptByEmpId의 select 쿼리가 수행되고 이의 결과가 getResult라는 resultMap에 deptNum에 들어가게 됩니다. 이를 사용하실때 주의점이 있다면 parameterClass와 resultClass 타입에 주의를 하셔야 합니다. 이를 제대로 명시해주지 않으면 값이 안넘어가거나 이상한 유니코드의 값이 parameter로 넘어가는 경우가 있더군요. 또 다른 주의점이 있다면, getDeptByEmpId에서 나온 결과인 deptNum은 getResult의 resultMap에 있는 deptNum property와 이름이 같아야 합니다. 이미 column을 사용하고 있기 때문에 어쩔 수 없이 이름이 다르다면 alias를 이용해서 쿼리에서 이름을 바꾸도록 합시다. 

---

지금까지 iBatis의 resultMap에서 자주 사용해볼만한 몇 가지 옵션들에 대해 설명드렸습니다. 지난 번 포스팅에서도 말씀드렸던 것과 같이 이는 조금만 응용하면 myBatis에서도 그대로 사용할 수 있는 것이기 때문에 알아두시면 분명 도움이 될 것이라고 생각합니다. 복잡한 쿼리는 억지로 합치는 것보다 조금씩 분리하여 처리하는 것도 성능적으로 크게 이슈를 일으키지 않는다는 이야기도 최근 종종 나오는 것 같은데, 한번의 쿼리로 모든 것을 처리하려고 고생하는 것보다 조금씩 분리하여 select를 이용해 처리해보는 것은 어떨까도 고민해보시면 좋을 것 같습니다. 