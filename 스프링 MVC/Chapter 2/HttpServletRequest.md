# 역할

- HTTP 요청 메세지를 개발자가 직접 파싱해서 사용해도 되지만, 매우 불편할 것. 
- 서블릿은 개발자가 HTTP 요청 메세지를 편리하게 사용할 수 있도록 대신 파싱해줌!
- 파싱 결과를 `HttpServletRequest` 객체에 담아서 제공한다

## HTTP 요청 메세지

```
POST /save HTTP/1.1 #START LINE
Host: localhost:8080
Content-Type: application/x-www-for-urlencoded

username=koo&age=20
```

- START LINE
	- HTTP 메소드
	- URL
	- 쿼리 스트링
	- 스키마, 프로토콜
- 헤더
	- 호스트
	- Content-Type
- 바디
	- form 파라미터 형식 조회
	- message body 데이터 직접 조회

## 임시 저장소 기능

- HTTP 요청이 시작부터 끝날 때까지 유지되는 임시 저장소 기능
	- 저장: `request.setAttribute(name, value)`
	- 조회: `request.getAttribute(name)`

## 세션 관리 기능

- `request.getSession(create: true)`

# 중요

- HttpServletRequest, HttpServletResponse를 사용할 때, 이 객체들이 HTTP 요청/응답 메세지를 편리하게 사용할 수 있게 해주는 객체라는 것을 명심하자.
- 깊이 있는 이해를 위해서는 **HTTP 스펙이 제공하는 요청, 응답 메세지 자체**를 이해해야 한다

# 기본 사용법

```
@WebServlet(name = "requestHeaderServlet", urlPatterns = "/request-header")  
public class RequestHeaderServlet extends HttpServlet {  
    @Override  
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {  
  
        printStartLine(request);  
  
    }  
  
    private static void printStartLine(HttpServletRequest request) {  
        System.out.println("--- REQUEST-LINE - start ---");  
        System.out.println("request.getMethod() = " + request.getMethod()); //GET  
        System.out.println("request.getProtocol() = " + request.getProtocol()); //HTTP/1.1  
        System.out.println("request.getScheme() = " + request.getScheme()); //http  
        // http://localhost:8080/request-header        System.out.println("request.getRequestURL() = " + request.getRequestURL());  
        // /request-header  
        System.out.println("request.getRequestURI() = " + request.getRequestURI());  
        //username=hi  
        System.out.println("request.getQueryString() = " +  
                request.getQueryString());  
        System.out.println("request.isSecure() = " + request.isSecure()); //https 사용유무  
        System.out.println("--- REQUEST-LINE - end ---");  
        System.out.println();  
    }  
}
```

위와 같이 서블릿 코드를 작성하고 URL 패턴에 맞게 요청을 보내면 
![](https://i.imgur.com/FAYKPWO.png)
요렇게 뜬다

## 모든 헤더 정보 꺼내기

```
//Header 모든 정보  
private void printHeaders(HttpServletRequest request) {  
    System.out.println("--- Headers - start ---");  
  
    Enumeration<String> headerNames = request.getHeaderNames(); //모든 헤더 정보  
    while (headerNames.hasMoreElements()) { //다음 요소가 있는지 확인  
        String headerName = headerNames.nextElement();  
        System.out.println(headerName + ": " + request.getHeader(headerName));  
    }  
    System.out.println("--- Headers - end ---");  
    System.out.println();  
}
```

![](https://i.imgur.com/j45WL7s.png)


```
//Header 모든 정보  
private void printHeaders(HttpServletRequest request) {  
    System.out.println("--- Headers - start ---");  
	request.getHeaderNames().asIterator()  
	        .forEachRemaining(headerName -> System.out.println(headerName + ": " + request.getHeader(headerName))); //람다식
	System.out.println("--- Headers - end ---");  
    System.out.println();  
}
```

요게 최신식

더 편리한 조회

```
//Header 편리한 조회  
private void printHeaderUtils(HttpServletRequest request) {  
    System.out.println("--- Header 편의 조회 start ---");  
    System.out.println("[Host 편의 조회]");  
    System.out.println("request.getServerName() = " +  
            request.getServerName()); //Host 헤더  
    System.out.println("request.getServerPort() = " +  
            request.getServerPort()); //Host 헤더  
    System.out.println();  
    System.out.println("[Accept-Language 편의 조회]");  
    request.getLocales().asIterator()  
            .forEachRemaining(locale -> System.out.println("locale = " +  
                    locale));  
    System.out.println("request.getLocale() = " + request.getLocale());  
    System.out.println();  
    System.out.println("[cookie 편의 조회]");  
    if (request.getCookies() != null) {  
        for (Cookie cookie : request.getCookies()) {  
            System.out.println(cookie.getName() + ": " + cookie.getValue());  
        }  
    }  
    System.out.println();  
    System.out.println("[Content 편의 조회]");  
    System.out.println("request.getContentType() = " +  
            request.getContentType());  
    System.out.println("request.getContentLength() = " +  
            request.getContentLength());  
    System.out.println("request.getCharacterEncoding() = " +  
            request.getCharacterEncoding());  
    System.out.println("--- Header 편의 조회 end ---");  
    System.out.println();  
}
```

![](https://i.imgur.com/Xy1zjBX.png)

- 특정 헤더 정보만 원하는 경우 - `request.getHeader("헤더이름")`
## 기타 정보


```
/기타 정보  
private void printEtc(HttpServletRequest request) {  
    System.out.println("--- 기타 조회 start ---");  
  
    System.out.println("[Remote 정보]"); // 요청한 클라이언트 정보
    System.out.println("request.getRemoteHost() = " + request.getRemoteHost());  
    System.out.println("request.getRemoteAddr() = " + request.getRemoteAddr());  
    System.out.println("request.getRemotePort() = " + request.getRemotePort());  
    System.out.println();  
  
    System.out.println("[Local 정보]"); // 서버 정보
    System.out.println("request.getLocalName() = " + request.getLocalName());  
    System.out.println("request.getLocalAddr() = " + request.getLocalAddr());  
    System.out.println("request.getLocalPort() = " + request.getLocalPort());  
  
    System.out.println("--- 기타 조회 end ---");  
  
    System.out.println();  
}
```

![](https://i.imgur.com/71LgQeM.png)


