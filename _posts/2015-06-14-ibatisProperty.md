---
layout: post
title:  "iBatis resultMap의 옵션 정리"
date:   2015-06-14 18:00:00
categories: tech
tags: iBatis
---

사실 요즘 대부분의 웹 개발에서 iBatis를 사용해 개발을 하지는 않지만, 저희 팀에서 맡고 있는 서비스나 좀 오래 전 개발된 프로젝트 같은 경우엔 iBatis를 사용하여 개발된 소스가 아직 많이 존재하고 있을 거라고 생각합니다. 저도 팀에서 아직 iBatis를 많이 사용하고 있다보니 이번 포스팅을 통해 iBatis의 resultMap의 옵션들에 대해 정리를 해보고자 합니다.   

{% highlight xml %}
<resultMap id="resultMapId" class="Class" [extends="parent-resultMap"]>
       <result property="propertyName" column="COLUMN_NAME"
                 [columnIndex="1"] [javaType="int"] [jdbcType="NUMERIC"]
                [nullValue="-999999"] [select="otherSatement"] />
    </resultMap>
{% endhighlight %} 

많은 분들이 알고 계시겠지만 resultMap의 구조는 위와 같지요. 그럼 resultMap의 속성들에 대해 알아보겠습니다.  

#####1. property  
 resultMap의 property 속성은 statement에 의해 반환된 result 객체의 프로퍼티 이름이죠. 즉 model 객체의 get 메소드 이름을 가르킵니다.

#####2. column  
column 속성 값은 결과 쿼리 결과 ResultSet 내의 컬럼의 이름입니다.

#####3. columnIndex    
위의 column 대신 index를 지정하여 ResultSet의 몇 번째 인자인지를 가르킬 수도 있습니다. 결과가 하나나 두개라면 이름 대신 사용하는 경우도 있을수 있지만, 좀 더 명확한 결과를 위해 주로 이름을 사용하는 편이죠.

#####4. jdbcType    
jdbcType 속성은 ResultSet 컬럼의 데이터베이스 컬럼 타입을 명시적으로 정의해줍니다. 특히, Date 타입 같은 경우 명시적으로 타입을 선언할 필요가 있습니다. JAVA는 하나의 Date 타입을 가지지만, SQL 데이터베이스는 여러 타입의 Date를 가지게 됩니다. 이것을 jdbcType 을 통해 명시적으로 지시하는 것이 가능합니다. 또한 String 타입은 VARCHAR, CHAR 또는 CLOB에 의해 생성될테지요. 이런 경우에도  jdbcType을 통해 명시적 선언이 가능합니다.

#####5. javaType
javaType 속성은 객체에 set되는 변수 타입을 명시적으로 정의하기 위해 사용합니다. XML mapping은 프레임워크를 위한 타입을 제공할 수 없기 때문에 필요하다면 타입을 선언해주어야 합니다. 만약 javaType이 설정되지 않고 프레임워크가 그 타입을 구분할 수 없다면 타입은 객체로 가정되어 처리되게 됩니다.

#####6. nullValue
nullValue 속성은 데이터베이스의 NULL 값 반환을 대신하여 사용하기 위해 사용합니다. ResultSet으로 부터 결과가 NULL이 나온 변수는 nullValue에 설정된 값으로 NULL 대신 set됩니다.

#####7. select
사실 이 포스팅을 쓰게 된 가장 큰 이유가 바로이 select 인데요. 이는 간단히 설명만 드리고, 다음 포스팅을 통해 사용법을 다뤄보도록 하겠습니다. select 속성은 result set의 결과로 나온 값을 다른 statement에 전달하여 select를 하기 위해 이용됩니다. 말이 조금 어렵지만, 한 번의 쿼리에서 나온 결과를 가지고 다른 쿼리를 다시 호출한다고 생각하시면 이해가 되실 겁니다. 

---

지금까지 iBatis의 resultMap에서의 옵션들을 설명드렸습니다. 많은 분들이 iBatis나, 혹은 myBatis를 쓰더라도 비슷하게 옵션이 주어지기 때문에 잘 알고 계실 수도 있지만, 이번 기회로 한번 더 정리하는 시간을 가졌으면 좋겠습니다.  