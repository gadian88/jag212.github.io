---
layout: post
title:  "eclipse에서 jsp파일 대소문자 변경시 재컴파일 에러"
date:   2015-05-31 18:00:00
categories: tech
tags: jsp
---

며칠 전, 팀에서 웹 개발을 하던 중 갑자기 java.lang.ClassNotFoundException이 발생해 난감한 경험이 있었습니다. 에러 내용은  
##### java.lang.ClassNotFoundException:org.apache.jsp.WEB_INF.jsp.XXXXX\_jsp.class  
와 같은 에러로 jsp로부터 컴파일된 java class 파일을 찾을 수 없다는 내용이었습니다.
하지만 분명히 해당 경로에는 jsp파일이 있었고, 빌드에 문제가 있을거라는 생각에 clean도 수차례 해보았지만, 해당 Exception은 여전히 사라지지 않았습니다. 결국 제가 이 Exception이 발생하기 전에 마지막으로 한 행동이 무엇이었을까를 생각하던 중, jsp파일의 대소문자를 변경했다는 것을 떠올렸습니다. 즉,  
#####abc.jsp → ABC.jsp  
로 바꾸었던 것이죠. 이런 경우, ABC.jsp는 컴파일될때 기존의 abc\_jsp.class라는 이름과 같게 컴파일이 되고, ABC\_jsp.class는 찾을 수가 없게 되기 때문에 계속 ClassNotFoundException이 발생하게 됩니다. 따라서 이런 경우 해결법은 아래와 같습니다. 

---

### 간단히 해결하기

위와 같은 경우 XXXXX\_jsp.class 파일이 생성된 폴더로 가서 해당 파일을 삭제해주어야 다시 해당 파일이 원하는 이름으로 다시 생성됩니다. 따라서 톰캣이 XXXX_jsp.class 파일을 생성하는 폴더를 찾아야 합니다. 이는 톰캣의 설정에 따라 생성되는 위치가 다르기 때문에 특정 경로를 알려드리기는 어렵습니다.  
#####\Workspace\\.metadata\\.plugins\org.eclipse.wst.server.core\  
만약, 특별히 tomcat에서 경로를 지정한 것이 아니라면 위와 같이 workspace 아래의 경로에서 tmp로 시작하는 폴더들 중 하나에서 해당 파일을 찾을 수 있을 것입니다. 

---

사실 위의 에러는 별것도 아닌 Exception으로 생각할 수도 있겠지만, 저같은 초보개발자에게는 저런 Exception 하나때문에 몇 십분씩 시간을 빼앗기는 일도 발생합니다. 더구나 파일의 대소문자를 변경했다고 해서 이런 문제를 겪게될 줄은 상상도 못했기 때문에 더욱 멘붕에 빠지게 만들었던 경험이었죠... 많은 초보개발자 분들에게도 이 글이 조금이나마 도움이 되셨으면 좋겠습니다.