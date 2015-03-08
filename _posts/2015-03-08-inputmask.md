---
layout: post
title:  "inputmask ����غ���"
date:   2015-03-08 18:00:00
categories: tech
tags: inputmask
---
<br>
�̹� �������� jquery inputmask�� ���� �� �� ���ϰ� �Է°��� ���ϴ� ���·� input�� ���� �� �ִ� ����� �Ұ��մϴ�.
���� inputmask�� ����ϰ� �� ���� ������ �þ� �����ϴ� �� input���� �ݵ�� ���ڸ� �Է��� ��, õ������ ',' ����Ͽ� ������ ���� �� ��.
�̶�� �䱸 ������ �־ �̸� ��� �ϸ� �� ���ϰ� ���� �� ����� ����ϴ� ��, inputmask�� ����ϸ� �� �� ���ϰ� �̸� ������ �� �ִٴ� ����
��� �Ǿ� �̸� Ȱ���� ������ �̷��� �������� �մϴ�.

---
#ù ��° ���� �õ�
<br>
ó�� �� �䱸������ ��� ���� ���� ���Խ��� ������ڴ� ������ �ϰ� �Ǿ����ϴ�. �׸��� �̸� ������ ���� �����ϱ�� �Ͽ����ϴ�.
<br>
{% highlight java %}
//ä������ ��
{% endhighlight %}
<br>
�������� ���� ���Խ��� ���� ���ڰ� �ƴ� Ÿ���� ���ڷ� �ٲٰ�, �ٽ� �̸� õ ������ ã�� ','�� ��� ����Դϴ�.
���� ���� ����� ���� ������ �Ѵٰ� �ص� �������� ���ϴ� ����� ����������, �� ���� inputmask�� Ȱ���� �����غ����� �ۼ��Ͽ����Ƿ�, ���������� inputmask�� ����غ����� �ϰڽ��ϴ�.

---

#inputmask Ȱ���ϱ�
�׷� �������� ������ ������ ���� inputmask�� Ȱ���ϴ� ����� ���ڽ��ϴ�.

<br>
{% highlight java %}
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<script src="http://code.jquery.com/jquery-1.11.0.js"></script>
<script src="./dist/jquery.inputmask.bundle.js"></script>		<!-- inputmask lib �߰� -->
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