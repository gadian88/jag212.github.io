---
layout: post
title:  "mybatis mapper JAVA 파일로 만들기"
date:   2015-03-01 18:00:00
categories: database
tags: mybatis
---
<br>
이번 포스팅에서는 mybatis의 mapper 파일을 JAVA파일로 작성해보는 것을 알아보고자 합니다.
사실 spring과 mybatis를 연동하는 프로젝트를 만들면서 대부분이 mapper는 xml로 작성하는 것이 기본적입니다.
물론 그렇게 만드는 것이 좀 더 수정도 편하게 할 수 있고, 가독적인 측면에서도 좀 더 보기 편한 장점 등 여러 장점이 있겠습니다.
그럼에도 이렇게 포스팅을 해보는 것은 이런 식으로도 mapper를 작성할 수 있다는 것을 알려드리고자 하는 작은 마음입니다.^^
<br>
사실 저도 웹 서핑을 하다가 mapper를 interface로 작성할 수 있다는 것을 알기 전까지는 당연히 xml 파일로 작성 한다고만
알고 있었고, 아마 저같은 많은 초보개발자 분들께서도 저와 비슷한 생각을 하고 계셨겠지요... 어쩌면 과거 ibatis를 계속 사용하다가
mybatis로 넘어오신 분들도 이렇게 생각하시는 분들이 많이 계실지도 모르겠습니다. 찾아보니 ibatis에서는 xml만으로만 sqlMap을
작성해왔고, 둘의 차이를 깊게 생각안하고 mybatis를 사용하셨던 분들이라면 저와 비슷한 생각을 가지고 계실 지도 모른다고 감히
추측해봅니다.
<br>
사설이 길었던 것 같습니다. 그럼 지금부터 간단히 interface로 mapper를 작성해보도록 하겠습니다.

---

##본격 작성하기
<br>
먼저, interface로 mapper를 작성하기 위해서는 xml에서 사용하는 설정이 어떻게 interface에서 적용되는지 알아야 합니다.<br>
<br>
+ interface의 패키지 이름과 interface의 이름은 mapping을 구분할 네임스페이스가 됩니다.
+ 어노테이션이 적용된 메소드의 이름은 mapping을 구분할 id값이 됩니다.
+ 어노테이션이 적용된 메소드의 리턴 타입은 resultType 속성이 됩니다.
+ 어노테이션이 적용된 메소드의 Parameter Type은 Parameter Type 설정이 됩니다.(써놓고 보니 같군요..)
<br>
그럼 간단한 예제를 보도록 하겠습니다.
![1]({{ http://jag212.github.io }}/assets/mapper1.PNG)
<br>
생각보다 어렵지 않습니다. select나 update 등 원하는 어노테이션만 추가하고 위와 같이 String으로 DML을 작성하시면 됩니다.
하지만 위와 같이 작성을 할 경우엔 조건문이나 반복문 등 동적 SQL을 작성하기는 어렵겠지요.
이를 위해서 mybatis에서는 다음과 같이 몇가지 어노테이션을 추가로 제공하고 있습니다.<br>
<br>
@SelectProvider<br>
@InsertProvider<br>
@UpdateProvider<br>
@DeleteProvider<br>
<br>
위의 Provider 어노테이션들을 가지고 DML을 작성해보도록 하겠습니다.
<br><br>
![1]({{ http://jag212.github.io }}/assets/mapper2.PNG)
<br><br>
간단한가요? 사실 제가 이렇게 포스팅을 남긴다고 해도 대부분이 mapper는 xml로 작성하고, 저 역시도 그렇게 하겠지요..
과연 이렇게 interface로 mapper를 작성하면 어떤 장점이 있을까를 인터넷에서 보니, 매핑 구문을 사용할 대 interface의 메소드를
그대로 사용하기 때문에 매핑 구문을 잘못 작성하는 경우가 없고, 타입 변환이 따로 필요없다는 장점이 있다는 군요.
하지만 저 역시 xml로 작성하는 것에 너무 익숙해져있다보니, 크게 와닿는 장점은 아니네요..

---

지금까지 mapper를 interface로 작성하는 방법을 소개해드렸습니다. 앞서 서론에서도 말씀드렸던 것처럼 사실 거의 사용하는 방법도
아니고, 앞으로도 거의 사용하지 않을 하찮은 지식일지도 모르겠습니다만, 이런 방법도 있네? 정도로만 생각해두시면 언젠가 도움이
될지도 모른다고 기대하면서 본 포스팅을 마치도록 하겠습니다. 감사합니다.