---
layout: post
title:  "chrome에서 table의 style display 문제"
date:   2015-05-10 18:00:00
categories: tech
tags: chrome
---

이번 포스팅은 chrome에서 table의 display를 다루다가 알게 된 문제점을 공유하고자 작성하였습니다.

---

흔히들 웹 개발을 하다보면 display의 값을 none으로 했다가 다시 block으로 하여, 화면에서 감추거나 나타내는 기능을 자주 구현하게 됩니다.  
저는 최근 table의 tr을 전체적으로 display: none; 으로 바꿨다가 다시 block으로 바꾸는데 table이 정상적으로 출력되지 않고, 이상하게 깨져서 화면에 출력되는 현상을 경험하였습니다.

### 예시

제가 당시 짰던 소스를 간단히 보여드리자면, 다음과 같습니다.

{% highlight html %}
	$("#reductionDate, #reduceTotal").css("display", "none");
// .. 몇가지 작업 후, 이벤트 발생시
	$("#reductionDate, #reduceTotal").css("display", "block");
{% endhighlight %}
  
위와 같이 구현을 하니 table이 일그러져 출력되는 상황이 발생하였습니다.  
이를 해결하기 위해 웹 검색을 해본 결과, 이는 chrome에서만 발생하는 문제라고 하더군요.
그리고 이를 해결하는 방법은 다음과 같습니다.
 
### 해결책

해결법은 간단합니다. 다음과 같이 block이라고 쓴 css를 table-row라고 바꾸어주시면 손쉽게 해결 가능합니다. 
  
{% highlight html %}
	$("#reductionDate, #reduceTotal").css("display", "table-row");
{% endhighlight %}
  
---

지금까지 주로 div태그에 display를 없앴다가 출력했다 하는 경우는 많았지만, 한번도 tr태그에서 이를 사용해본 경험이 없었기에, 최근에서야 이를 알게 되었습니다. 혹시라도 저처럼 table의 row에 직접적으로 display 속성을 변경하고자 하시는 분들을 chrome에서 위와 같이 사용해주시면 되겠습니다.