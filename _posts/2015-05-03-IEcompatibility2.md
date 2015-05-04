---
layout: post
title:  "IE의 호환성 보기 설정시 발생하는 문제해결"
date:   2015-05-03 18:00:00
categories: tech
tags: IE
---

이번 포스팅은 지난 포스팅에 이어, IE에서 호환성 보기를 설정했을 경우 발생하는 문제를 방지하는 방법에 알아보고자 합니다.

---

지난 번 포스팅에서는 IE에서 호환성 보기를 설정하는 경우 IE의 문서모드가 고정되어 버린다는 문제를 말씀드렸습니다.
이는 meta 태그로는 해결이 안되고 서버의 Response Header를 알맞게 setting하여 해결해야만 하는데, 그 방법에 대해 알아보겠습니다.

### Apache HTTP Server

Apache 서버를 이용한다면 httpd.conf 파일에 다음과 같은 내용을 추가하므로써 Response Header를 setting 합니다.

{% highlight xml %}
LoadModule headers_module modules/mod_headers.so


<IfModule headers_module>
	Header set X-UA-Compatible:IE=Edge
</IfModule>
{% endhighlight %}
  
### Tomcat 서버만 단독으로 사용시

Tomcat 단독으로 사용한다면, JAVA Servlet Filter를 이용하여 다음과 같은 코드로 Response Header를 setting 할 수 있습니다.
  
{% highlight java %}
public class IEFilter implements Filter {

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
	}

	@Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
			throws IOException, ServletException {
		HttpServletResponse res = (HttpServletResponse) response;
		res.setHeader("X-UA-Compatible", "IE=Edge");
		chain.doFilter(request, response);
	}

	@Override
	public void destroy() {
	}
}
{% endhighlight %}
  
---

이상으로 server setting을 통해 Response Header의 내용을 변경하고, 이로써 호환성 보기를 설정하는 경우에도 개발자가 원하는 
IE의 모드로 화면이 출력되도록 하는 방법을 알아보았습니다. 수많은 사용자 혹은 여러 IE를 대상으로 하는 서비스라면 위와 같이
호환성 보기에서도 문제가 발생하는 않도록 해야 하겠습니다.