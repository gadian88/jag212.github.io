---
layout: post
title:  "smart editer에서 text만 가져오기"
date:   2015-05-17 18:00:00
categories: tech
tags: smartEditer
---

이번 포스팅은 smart editer 2를 사용하다가 알게 된 작은 지식에 대해 적어보겠습니다.

---

최근 개발 중 네이버의 smart editer 2를 사용하게 된 경험이 있었습니다.
이를 사용 중, 사용자가 작성한 내용 중 순수한 text만을 가져와야 하는 경우가 생겼습니다.
하지만 smart editer에서는 작성한 내용을 가지고 html 형태의 Rich Text로 밖에 가져오는 방법이 없었습니다. 이를 해결하기 위해 네이버 개발자 센터에서 해결 방법을 찾다가 다음과 같은 방법을 알게 되었습니다.

### text만 가져오기

간단히 javascript 소스를 보여드리자면, 다음과 같습니다.

{% highlight html %}
    var text = data.replace(/[<][^>]*[>]/gi, "");
{% endhighlight %}
  
보시면 아시겠지만, 특별한 방법은 없었습니다. 단지 정규식을 가지고 <>로 이루어진 태그들만 분리하는 방법입니다. 
 
---

저처럼 smart editer를 사용하시다가 text만 분리해야하는 경우가 생기는 일은 많지 않으실지도 모르겠지만, 그런 경우가 발생하면 위와 같은 방법을 사용하세요. 또한 위의 정규식은 꼭 이런 상황이 아니어도 html에서 태그를 제외한 text만 분리하는 데도 사용할 수 있으므로, 웹 개발 종종 사용하는 경우가 생길 것이라고 생각합니다.