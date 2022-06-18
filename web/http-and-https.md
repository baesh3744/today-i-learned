# HTTP와 HTTPS

## HTTP의 문제점

### 1. HTTP는 평문 통신이기 때문에 도청이 가능합니다.

TCP/IP 구조의 통신은 전부 통신 경로 상에서 엿볼 수 있습니다. 평문으로 통신할 경우, 메시지의 의미를 파악할 수 있기 때문에 암호화하여 통신해야 합니다.

#### 보완 방법

1. 통신 자체를 암호화
    - SSL(Secure Socket Layer)이나 TLS(Transport Layer Scurity)라는 다른 프로토콜을 조합함으로써 HTTP의 통신 내용을 암호화할 수 있습니다. SSL을 조합한 HTTP를 HTTPS(HTTP Secure) 또는 HTTP over SSL이라고 부릅니다.
2. 컨텐츠를 암호화
    - HTTP를 사용해서 운반하는 내용인 HTTP Message에 포함되는 컨텐츠만 암호화하는 것입니다. 컨텐츠를 암호화해서 전송하면, 수신자는 그 암호를 해독하는 작업이 필요합니다.

### 2. 통신 상대를 확인하지 않기 때문에 위장이 가능합니다.

HTTP에 의한 통신에는 상대가 누구인지를 확인하는 처리가 없기 때문에 누구든지 request를 보낼 수 있습니다. IP 주소나 포트 등에서 그 웹 서버에 엑세스 제한이 없는 경우, request가 오면 상대가 누구든지 response를 반환합니다. 이러한 특징은 아래와 같은 문제점을 유발할 수 있습니다.

1. Request를 보낸 곳의 웹 서버가 원래 의도한 response를 보내야 하는 웹 서버인지를 확인할 수 없습니다.
2. Response를 반환한 곳의 클라이언트가 원래 의도한 request를 보낸 클라이언트인지를 확인할 수 없습니다.
3. 통신하고 있는 상대가 접근이 허가된 상대인지를 확인할 수 없습니다.
4. 어디에서 누가 request했는지 확인할 수 없습니다.
5. 의미없는 request도 수신합니다. -> DoS 공격을 방지할 수 없습니다.

#### 보완 방법

SSL로 상대를 확인할 수 있습니다. SSL은 상대를 확인하는 수단으로 증명서를 제공합니다. 증명서는 신뢰할 수 있는 제 3자 기관에 의해 발행되는 것이기 때문에 서버나 클라이언트가 실재함을 증명합니다. 이 증명서를 이용함으로써 통신 상대가 내가 통신하고자 하는 서버임을 나타내고 이용자는 개인 정보 누설 등의 위험성이 줄어들게 됩니다. 클라이언트는 이 증명서로 본인 확인을 하고 웹 사이트 인증에도 이용할 수 있습니다.

### 3. 완전성을 증명할 수 없기 때문에 변조가 가능합니다.

여기서 완전성이란 정보의 정확성을 의미합니다. 서버나 클라이언트에서 수신한 내용이 송신 측에서 보낸 내용과 일치한다라는 것을 보장할 수 없는 것입니다. Request/response가 발신된 후에 상대가 수신하기 전에 누군가에 의해 변조되더라도 이 사실을 알 수 없습니다. 이와 같이 공격자가 request/response를 빼앗아 변조하는 공격을 중간자 공격(man-in-the-middle)이라고 부릅니다.

#### 보완 방법

MD5, SHA-1 등의 해시 값을 확인하는 방법과 파일의 디지털 서명을 확인하는 방법이 존재하지만 확실히 확인할 수 있는 것은 아닙니다. 확실히 방지하기 위해서는 HTTPS를 사용해야 합니다. SSL에는 인증이나 암호화, 그리고 다이제스트 기능을 제공하고 있습니다.

<br>

## HTTPS

> HTTP + 암호화, 인증, 완전성 보호

HTTPS는 SSL의 껍질을 덮어쓴 HTTP라고 할 수 있습니다. 즉, HTTPS는 새로운 애플리케이션 계층의 프로토콜이 아니라, HTTP 통신하는 소켓 부분을 SSL(Secure Socket Layer)이나 TLS(Transport Layer Security)라는 프로토콜로 대체하는 것 뿐입니다. HTTP는 원래 TCP와 직접 통신했지만, HTTPS에서 HTTP는 SSL과 통신하고 SSL이 TCP와 통신합니다. SSL을 이용한 HTTPS는 암호화와 증명서, 완전성 보호를 이용할 수 있게 됩니다.

HTTPS의 SSL에서는 공통키 암호화 방식과 공개키 암호화 방식을 혼합한 하이브리드 암호 시스템을 사용합니다. 공통키를 공개키 암호화 방식으로 교환한 다음에 다음 통신부터는 공통키 암호를 사용하는 방식입니다.

### 모든 웹 페이지에서 HTTPS를 사용해도 될까?

평문 통신에 비해서 암호화 통신은 CPU나 메모리 등 리소스를 더 많이 요구합니다. 통신할 때마다 암호화를 하면 추가적인 리소스를 소비하기 때문에 서버 한 대당 처리할 수 있는 request의 수가 상대적으로 줄어들게 됩니다.

하지만 최근에는 하드웨어의 발달로 인해 HTTPS를 사용하더라도 속도 저하가 거의 일어나지 않으며, 새로운 표준인 HTTP 2.0을 함께 사용한다면 오히려 HTTPS가 HTTP보다 더 빠르게 동작합니다. 따라서 과거에는 민감한 정보를 다룰 때만 HTTPS에 의한 암호화 통신을 사용했지만, 현재는 모든 웹 페이지에서 HTTPS를 적용하고 있습니다.

<br>

## Reference

https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Network#http%EC%99%80-https