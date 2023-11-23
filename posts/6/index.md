# RFC-2616: HTTP Method

<!--more-->

# 서론
본문에서는 HTTP에 대한 RFC-2616, 그 중에서도 HTTP Method에 대한 내용을 요약하여 HTTP에 대한 이해 향상을 목적으로 한다.

GET, POST 메소드 이외에도 다양한 메소드가 사용될 수 있기에 알아둬서 나쁠게 없다고 생각된다. 따라서 본문을 작성한다.

# RFC-2616
- HTTP/1.1 1999년 6월

## Request

클라이언트에서 서버로의 요청 메시지 첫 번째 줄에는 다음이 포함된다:

- 리소스에 사용할 메서드

- 리소스 식별자(URI)

- 사용중인 프로토콜 버전

```
Request = Request-Line *(general-header | request-header | entity-header) CRLF

CRLF

Message-body
```

For example:

```HTTP
POST /login HTTP/1.1
Connection: keep-alive
User-Agent: Mozila/5.0 (Macintosh: Intel Mac OS X 10_14_6)
AppleWebKit/537.36 (KHTML. like Gecko) Chrome/79.0.3945.88 Safari/537.36
content-length: 67
content-type: application/json

{email: kim@dreamhack.io, password: "dreamhack"}
```

## Request-line
Requst-line은 메서드 토큰으로 시작하고, 그 뒤에 요청-URI 및 프로토콜 버전이 오며 CRLF로 끝난다.

element는 SP문자로 구분된다. 마지막 CRLF 시퀀스를 제외하고 CR이나 LF는 허용되지 않는다.

Request-line = Method SP Request-URI SP HTTP-Version CRLF

### Method
Method token은 수행할 메서드를 나타낸다.<br>메서드는 대소문자를 구분한다.

Method:

- [OPTIONS]()
- [GET]()
- [HEAD]()
- [POST]()
- [PUT]()
- [DELETE]()
- [TRACE]()
- [CONNECT]()

- extension-method

extension-method = token

리소스에서 허용하는 메서드 목록은 Allow 헤더 필드에서 지정할 수 있다.

허용된 메서드 집합은 변경될 수 있으므로 Response의 반환 코드는 메서드가 현재 리소스에 허용되는지 여부를 항상 클라이언트에 알린다.

메서드가 서버에 알려져있지만, 요청된 리소스에 허용되지 않는 경우, 405(허용되지 않는 리소스)를 반환해야 하며, 서버가 메서드를 인식하지 못하거나 구현하지 않은 경우 501(구현되지 않음)을 반환해야 한다.

GET 및 HEAD 메서드는 모든 범용 서버에서 지원되어야 한다. <br>다른 모든 메서드는 선택사항이다. 그러나 위의 메서드가 구현되는 경우. 후술할 메서드와 동일한 의미로 구현되어야 한다.

## Method Definitions
HTTP/1.1의 일반적인 메서드는 아래에 정의된다. 이 세트는 확장될 수 있지만, 추가 메소드가 별도로 확장된 클라이언트 및 서버에 대해 동일한 의미를 공유한다고 가정할 수는 없다.<br>호스트 요청 헤더 필드는 HTTP/1.1 요청과 함께 제공되어야 한다.

### Safe and Idempotent Methods
#### Safe Methods
소프트웨어는 인터넷을 통해 사용자를 대표한다. 따라서 개발자들은 사용자가 예상치 못한 중요한 행동을 취할 수 있다는 점을 인식하고, 사용자가 이러한 행동을 할 때 그것이 어떤 의미를 가지는지 알 수 있도록 주의해야 한다.

일반적으로, GET과 HEAD 메소드는 단순히 정보를 검색하는 데 사용되며, 다른 행동을 취하는 데 사용되어서는 안 된다. 이러한 메소드는 "안전하다"고 간주되어야 한다.<br>
이는 사용자 에이전트가 POST, PUT, DELETE와 같은 다른 메소드를 특별한 방식으로 표시할 수 있도록 하여, 사용자가 보다 위험할 수 있는 행동을 요청하고 있다는 것을 인지하게 만든다.

서버가 GET 요청을 수행함에 따라 부수적인 효과를 발생시킬 수 있음을 인식하는 것이 중요하다.<br>
실제로 일부 동적 리소스는 이를 기능으로 간주한다. 여기서 중요한 구분은 사용자가 이러한 부수 효과를 요청하지 않았기 때문에, 이로 인한 결과에 대해 책임을 지지 않는다는 것이다.

#### Idempotent Methods


