---
layout: post
title:  "inputmask 사용해보기"
date:   2015-03-08 18:00:00
categories: tech
tags: inputmask
---
<br>
이번 포스팅은 jquery inputmask를 통해 좀 더 편하게 입력값을 원하는 형태로 input에 담을 수 있는 방법을 소개합니다.
제가 inputmask를 사용하게 된 계기는 과제를 맡아 수행하던 중 input으로 반드시 숫자만 입력할 것, 천단위로 ',' 사용하여 구분을 쉽게 할 것.
이라는 요구 사항이 있어서 이를 어떻게 하면 더 편하게 만들 수 있을까를 고민하던 중, inputmask를 사용하면 좀 더 편하게 이를 구현할 수 있다는 것을
듣게 되어 이를 활용해 보고자 이렇게 포스팅을 합니다.

---
#첫 번째 나의 시도
<br>
처음 이 요구사항을 듣고 가장 먼저 정규식을 사용하자는 생각을 하게 되었습니다. 그리고 이를 다음과 같이 구현하기로 하였습니다.
<br>
{% highlight java %}
//채워넣을 것
{% endhighlight %}
<br>
위에서는 먼저 정규식을 통해 숫자가 아닌 타입은 빈문자로 바꾸고, 다시 이를 천 단위로 찾아 ','를 찍는 방식입니다.
물론 위의 방식을 통해 구현을 한다고 해도 문제없이 원하는 기능을 수행하지만, 이 글은 inputmask의 활용을 공부해보고자 작성하였으므로, 본격적으로 inputmask를 사용해보도록 하겠습니다.

---

#inputmask 활용하기
그럼 이제부터 간단한 예제를 통해 inputmask를 활용하는 방법을 보겠습니다.

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
        groupSeparator: ",",
        autoGroup: true,
        integerDigits: 15
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