---
layout: post
title:  "Improved Type Inference for Generic Instance Creation"
date:   2015-04-05 18:00:00
categories: tech
tags: Database
---

이번 포스팅에서는 저의 신입사원 교육기간 중 알게 된 짤막한 지식을 공유하겠습니다.

---

##경험담
신입사원 교육기간 중 저와 동기들은 JAVA와 관련된 간단한 쪽지시험(?)을 보게 된 적이 있습니다.
여러 문제 중 하나에서 저와 몇몇 동기들이 이상하다 생각한 것이 있었는데, 그 문제에 제가 적어낸 답은 다음과 같았습니다.

{% highlight java %}
Map<String, List<String>> map = new HashMap<String, List<String>>();
{% endhighlight %}

제가 생각하기에 답은 분명히 맞았다고 생각했는데, 채점된 시험지에는 △ 표시가 되어있었습니다.
이상하다고 여긴 저는 조원들과 답을 비교해보았지만, △ 표시를 받게된 동기들은 저와 마찬가지로 스스로가 왜 △를 받았는지를 모르고 있었습니다.
처음에는 < 혹은 > 괄호가 잘못되었나 세어보기까지도 했지만 왜 틀렸는지를 알 수는 없었습니다.
그리고 웹 검색을 통해 답을 알게 된 저는, 곧 제 스스로에게 부끄러움을 느끼게 되었습니다. 이 문제의 답은 다음과 같습니다.

{% highlight java %}
Map<String, List<String>> map = new HashMap<>();
{% endhighlight %}

무엇이 달라졌을까요? 네. 객체 생성시 generic 표현이 간결해졌습니다. 저는 그동안 앞 뒤의 generic을 맞춰서 객체를 생성해야 한다고만 알고 있었지만,
JAVA 7부터는 그럴 필요가 없었습니다. Improved Type Inference for Generic Instance Creation 라는 것으로 좀 더 generic 표현을 간결하게 사용하기 위해
JAVA 7부터 지원되는 기능입니다.

---

위의 경험을 통해 제 스스로 아직 공부가 많이 부족함을 느꼈고, 얼마나 게으르게 JAVA 공부를 해왔는지 반성하게 되었습니다 ㅠㅠ.. 앞으로 JAVA가 발전되면서
새로 생긴 여러 기능들에 대해 좀 더 관심을 가지고, 공부를 해야겠습니다.