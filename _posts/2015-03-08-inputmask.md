---
layout: post
title:  "inputmask 사용해보기"
date:   2015-03-08 18:00:00
categories: tech
tags: inputmask
---
<br>
이번 포스팅은 jquery inputmask를 통해 좀 더 편하게 입력값을 원하는 형태로 input에 담을 수 있는 방법을 소개합니다.
제가 inputmask를 사용하게 된 계기는 과제를 맡아 수행하던 중 "input으로 반드시 숫자만 입력할 것, 천단위로 ',' 사용하여 구분을 쉽게 할 것."
이라는 요구 사항이 있어서 이를 어떻게 하면 더 편하게 만들 수 있을까를 고민하던 중, inputmask를 사용하면 좀 더 편하게 이를 구현할 수 있다는 것을
듣게 되어 이를 활용해 보고자 이렇게 포스팅을 합니다.

---
##첫 번째 나의 시도
<br>
처음 이 요구사항을 듣고 가장 먼저 정규식을 사용하자는 생각을 하게 되었습니다. 그리고 이를 다음과 같이 구현하기로 하였습니다.
<br>
{% highlight html %}
<!-- 채워넣을것 -->
{% endhighlight %}
<br>
위에서는 먼저 정규식을 통해 숫자가 아닌 타입은 빈문자로 바꾸고, 다시 이를 천 단위로 찾아 ','를 찍는 방식입니다.
물론 위의 방식을 통해 구현을 한다고 해도 문제없이 원하는 기능을 수행하지만, 이 글은 inputmask의 활용을 공부해보고자 작성하였으므로, 본격적으로 inputmask를 사용해보도록 하겠습니다.

---

##inputmask 활용하기
그럼 이제부터 간단한 예제를 통해 inputmask를 활용하는 방법을 보겠습니다.
먼저 다음 link에서 jquery inputmask를 다운받습니다.<br><br>
[https://github.com/RobinHerbots/jquery.inputmask] (https://github.com/RobinHerbots/jquery.inputmask)
<br><br>
그리고 다음 예제를 살펴보겠습니다.

<br>
{% highlight html %}
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<script src="http://code.jquery.com/jquery-1.11.0.js"></script>
<script src="./dist/jquery.inputmask.bundle.js"></script>		<!-- inputmask lib 추가 -->
</head>
<body>

<div>
	<input type="text" id="testNumber">
	<input type="text" id="testNumber2">
</div>

<script type="text/javascript">
$("#testNumber").keyup(function () {
    $("#testNumber").inputmask("numeric", {
        groupSeparator: ",",	//separator 설정
        autoGroup: true,		//천단위 그룹
        integerDigits: 15		//자리수 설정
    });
    $("#testNumber")[0].focus();
});

$(document).ready(function(){
    $('#testNumber2').inputmask('999,999,999,99', { numericInput: true });    //123456  =>  ___.__1.234,56
});
</script>
</body>
</html>
{% endhighlight %}
<br>
위를 보면 사용법이 어렵지 않다는 것을 알 수 있습니다. 간단히 가장 처음의 요구사항을 위한 2개의 input을 만들어 보았습니다.
우선 첫번째 keyup을 통해 처리하는 function을 보시면 간단히 input을 selector로 잡아 inputmask를 통해 원하는 설정을 통해
값을 바꾸는 것이 가능합니다.<br>
두번째 방법으로 좀 더 간략하게 설정을 작성하는 방법도 있습니다. 위에서 '9'는 숫자를 의미하기 때문에 원하는 자리까지 9와 , 기호를 통해 inputmask를 씌우는 방법도 있습니다.
하지만 두번째 방법을 선택하는 경우, 크롬에서는 바로 값이 바뀌는 것이 아니라 반응이 상당히 느린 이상 현상이 나타났습니다(IE에서는 정상적 작동. 여러 시도를 해본 결과, 
이는 keyup에 이벤트를 거는 방식으로 처리를 해야할 것 같다는 결론에 도달했습니다..)
또한 오른쪽에 붙은 주석과 같이 마스크가 input에 나타나게 됩니다. 이는 캡쳐를 통해 확인하겠습니다.<br>
<br>
![1]({{ http://jag212.github.io }}/assets/inputmask.png)
<br>
저는 이 mask가 나타나는것을 보고 싶진 같진 않은데, 기본 설정이 true로 되어있어 이를 없애기 위해서 다음과 같은 설정도 가능합니다.
<br>
{% highlight html %}
$(selector).inputmask({
                mask: '9999 9999 9999 9999',
                placeholder: ' ',
                showMaskOnHover: false, //마우스를 올렸을때 나타나는 mask 안보이게
                showMaskOnFocus: false, //focus 시 나타나는 mask 안보이게
		... 생략
{% endhighlight %}
<br>
그런데 위의 방식에 조금 문제가 있는 것 같습니다. 제 브라우저가 잘못된 것인지는 모르지만, 저의 경우  showMaskOnHover와 showMaskOnFocus 두 옵션 모두를 false로 하면
값의 입력이 아예 안되는 문제가 있습니다. 결국 크롬에서의 문제와 mask 때문에 저는 위의 2개 방식 중 첫번째 keyup 이벤트에서 처리하는 방법을 채택하였습니다. 이는 좀 더 알아보고 해결하는 방법이 있다면 내용을 추가하겠습니다.

---

지금까지 inputmask를 통해 "input으로 반드시 숫자만 입력할 것, 천단위로 ',' 사용하여 구분을 쉽게 할 것."이라는 요구사항을 구현해보았습니다. 사실 이는 숫자를 담는 input에서는 상당히 많은 필요가 있는 기능이므로, inputmask를 활용하여
모두들 한번쯤 구현을 해보는 것도 도움이 될 것이라고 생각합니다. 또한 위에서는 단지 숫자만 예를 들어 설명했지만, inputmask의 경우 날짜, 정규식 등 다양한 mask를 지원하기 때문에 때에 따라서 자신에게 필요한 mask를 골라 사용하시면 좀 더
편리하게 input의 값을 변형하는 것도 가능합니다. 그럼 이상으로 포스팅은 마치도록 하겠습니다.