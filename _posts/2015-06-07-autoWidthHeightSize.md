---
layout: post
title:  "jquery로 창크기에 맞게 사이즈 조절하기"
date:   2015-06-07 18:00:00
categories: tech
tags: jsp
---

지난 번, IE의 호환성 보기로 인해 겪었던 문제에 대해 블로그에 글을 쓴 적이 있었습니다. 이번 포스팅은 지난 번의 조치 사항에서 파생된 문제를 겪었던 경험을 작성해보고자 합니다.  
저는 지난 번 호환성보기 문제를 해결 후, IE11에서 많은 테스트 이후 아무런 문제가 없다고 생각한 후, 이를 서비스에 배포하였습니다.
하지만 오늘 IE10 사용자에게서 flex 화면이 작게 보인다는 문의가 들어왔습니다. 웹 검색을 통해 원인이 무엇인가를 찾아보던 중, flex가 IE8에서는 '%'로 width나 height를 줄 시, 제대로 출력이 안된다는 것을 알게 되었습니다. 이 문제가 어째서 호환성보기 문제를 조치하고 나서 IE10 사용자에게 발생했는지는 알 수 없지만, 이를 조치하기 위해서 무엇이든 방법을 찾아야 했습니다.   
### 해결책 
사실 저희 팀이 아니고서야 최근 flex를 사용하여 웹 개발을 하는 경우가 드물지만, 간단히 flex에 대해 설명 드리자면, 껍데기 역할을 하는 jsp파일이 존재하고, 이 안에서 flash 파일이 동작하는 형태입니다. 따라서 flash가 작게 출력되는 문제를 해결하기 위해서는, 이 껍데기 역할을 하는 jsp파일의 width와 height를 동적으로 브라우저의 창 크기에 맞게 조절되도록 해야했습니다. 물론 '%'를 주지 않고 말이죠.  
이는 생각보다 간단하게 해결 되었습니다. 바로 window 사이즈가 변경될 때마다 해당되는 width와 height도 이에 맞게 변경하면 되었습니다. 해결 소스는 다음과 같습니다.

{% highlight html %}
<script language="JavaScript" type="text/javascript">
$(document).ready(function(){ 
	$('selector').css('width', $(window).width()); 
	$('selector').css('height', $(window).height()); 
	$(window).resize(function() { 
		$('selector').css('width', $(window).width()); 
		$('selector').css('height', $(window).height()); 
	}); 
});
</script>
{% endhighlight %} 

위와 같이 jquery의 resize 함수를 이용하여 창의 크기가 변경될 때마다 widht와 height 값을 변경할 수 있습니다.

---

이 글을 읽는 많은 분들께서는 웹에서 flex를 사용하지 않으실테지만, 웹개발 중 브라우저나 어떤 다른 이슈로 '%'로 width나 height가 변경되지 않는 문제를 겪으실 경우 위의 방법으로 처리를 하는 것도 하나의 방법임을 알아두시면 좋을 것 같습니다.