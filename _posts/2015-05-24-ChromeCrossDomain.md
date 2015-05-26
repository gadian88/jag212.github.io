---
layout: post
title:  "Chrome에서 cross domain 문제 간단히 해결하기"
date:   2015-05-24 18:00:00
categories: tech
tags: cross domain
---

웹개발을 하면서 ajax를 통해 통신을 하다보면 종종 겪는 문제 중 하나가 바로 cross domain으로 인한 문제입니다. AJAX 호출을 필요로 하는 기능에서 보안상 이유로 동일 서버 이외에는 접근할 수 없도록 한 것인데요. Javascript 보안 정책 중에 하나인 동일근원정책(Same-Origin Policy) 정책에 걸리는 부분이 바로 크로스 도메인을 할때 일어납니다. 이는 샌드박스(SandBox) 정책이라고 합니다. 저같은 경우 팀에서 flex를 사용하다가 flex의 actionscript와 javascript 간의 통신을 구현하던 중, cross domain 문제가 발생했는데요. 간단히 해결법들을 알아보도록 하겠습니다.

---

### document.domain를 사용하는 방법

이는 서브 도메인이 다른 경우 사용하는 방법입니다. actionscript와 javascript 간의 통신에서 발생하는 문제점도 이를 통해서 해결가능합니다. 서브도메인이 다르다는 것은 다음과 같은 상황을 말합니다.
  
http://A.nhnent.com -> http://B.nhnent.com
  
이는 nhnent.com이라는 공통의 도메인을 사용하고 있기 때문에, 다음과 같이 javascript에 추가하여 해결가능합니다.
  
document.domain = "naver.com";
  
이는 도메인이 같은 경우에만 사용가능하기 때문에, cross domain 문제를 항상 해결해주는 것은 아닙니다. 
 
---

### jQuery의 JSONP를 사용

다음은 jQuery의 JSONP를 사용하는 것으로, ajax 통신을 할때 jQuery 1.2부터 지원되는 JSONP로 데이터를 주고 받도록 구현하는 방법입니다. 이는 가장 보편적으로 사용하는 방법 중 하나이므로, 많은 분들이 아는 방법있을 것이라고 생각합니다. 이는 웹에도 예제가 상당히 많이 있기 때문에 따로 예를 들지는 않겠습니다.

---

### Chrome 옵션값으로 해결하기

사실 저같은 경우, 위의 방법을 사용하기가 조금 어려웠습니다. javascript와 actionscript 간 통신을 구현하다보니 javascript 단에서는 위의 방법을 사용할 수 있었지만, 반대로 데이터를 전송하는 경우에는 jQuery도 사용할 수 없기에 어떻게 해결해야할지 난감했습니다. 그러던 중 chrome에서는 간단히 옵션값으로 이를 해결할 수 있다고 하여, 이를 이용하기로 하였습니다.  
사실 cross domain 문제가 실제로 도메인이 다른 경우에만 나타나는 것이 아니고, 저와 같이 같은 도메인을 사용하고 있음에도 발생하는 경우가 있습니다. 이는 실서버에 배포되었을 경우에는 문제가 없지만 local에서 테스트를 하는 경우에 발생하여 종종 저같은 초급 개발자들을 난감하게 하지요. 따라서 간단히 chrome의 옵션값만을 바꾸는 방법으로 보다 쉽게 이 문제를 해결할 수 있습니다.  
  
--disable-web-security
  
위와 같은 옵션을 chrome 실행시 붙여 사용하면 됩니다.
  
![1]({{ http://jag212.github.io }}/assets/chromeCrossDomain.PNG)
  
참고로, Mac을 사용하시는 분들은 터미널에서,  
  
open -a Google\ Chrome --args --disable-web-security
    
위와 같이 입력하여 실행하시면 된다고 합니다.

---

사실 위의 방법들이 cross domain을 해결하는 근본적인 해결책은 아니지만, 간편히 사용할 수 있기 때문에 이 포스팅에 담아보았습니다. 사실 웹에서는 보다 다양하고, 확실한 해결책 또한 찾아볼 수 있지만, 이는 상황에 따라서 적절한 해결책을 사용하시면 되겠습니다.^^