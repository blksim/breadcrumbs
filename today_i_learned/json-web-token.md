https://en.wikipedia.org/wiki/JSON_Web_Token<br>
https://auth0.com/docs/tokens/concepts/jwt-claims<br>

### JWT(JSON Web Token)이란
>JSON Web Token (JWT, sometimes pronounced /dʒɒt/[1]) is an Internet standard for creating JSON-based access tokens that assert some number of claims. 
For example, a server could generate a token that has the claim "logged in as admin" and provide that to a client. 
The client could then use that token to prove that it is logged in as admin. 
The tokens are signed by one party's private key (usually the server's), so that both parties (the other already being, by some suitable and trustworthy means, in possession of the corresponding public key) are able to verify that the token is legitimate. The tokens are designed to be compact,[2] URL-safe,[3] and usable especially in a web-browser single-sign-on (SSO) context. 
JWT claims can be typically used to pass identity of authenticated users between an identity provider and a service provider, or any other type of claims as required by business processes.[4][5]
>JWT relies on other JSON-based standards: JSON Web Signature and JSON Web Encryption.
1. JSON 포맷 기반의
2. 몇가지 claim들을 단언하는
3. 인터넷 표준 액세스 토큰.

**Token에서의 Claim이란**
>JSON Web Token (JWT) claims are pieces of information asserted about a subject. 
For example, an ID Token (which is always a JWT) may contain a claim called name that asserts that the name of the user authenticating is "John Doe".
In a JWT, a claim appears as a name/value pair where the name is always a string and the value can be any JSON value. 
Generally, when we talk about a claim in the context of a JWT, we are referring to the name (or key). For example, the following JSON object contains three claims (sub, name, admin):
- 어떤 subject를 단언하기 위한 정보 모음들을 말한다.
- 예를 들어, ID Token은 유저 인증에서 이름이 "John Doe"라는 것에 대한 이름을 단언하는 claim을 포함할 수 있다.
- JWT에서 claim은 키/값 쌍으로 나타나고 키는 항상 문자열, 모든 JSON value가 값이 될 수 있다.
- 대개 우리가 JWT context의 claim에 대해서 얘기한다면 '키'에 대한 얘기다. 
- 아래와 같은 JSON 객체는 세 개의 claim을 갖고 있다.
```
{
    "sub": "1234567890",
    "name": "John Doe",
    "admin": true
}
```

### 구조
https://itnext.io/so-what-the-heck-is-jwt-or-json-web-token-dca8bcb719a6 참고<br>

JWT는 비밀키를 생성한다. 클라이언트로부터 JWT를 받으면, 서버에서 JWT를 이 비밀키로 확인해볼 수 있다. JWT에 가해지는 어떤 변형도 검증 실패를 부른다.
JWT는 간단히 말하면 문자열이나 온점(.)으로 구분되는 세 파트로 나뉘는데, 각 파트는 base64 인코딩되어 있다.
```
var HEADER_HASH = base64(header);
var PAYLOAD_HASH = base64(payload);
var SIGNATURE_HASH = base64(signature);

var JWT = HEADER_HASH + '.' + PAYLOAD_HASH + '.' + SIGNATURE_HASH;
```

**header**
`header`는 JWT 암호화 알고리즘에 대한 정보를 담고 있는 JSON 문자열이다. 중요한 필드는 `type`과 `alg'. `type`은 항상 JWT이다. 알고리즘을 의미하는 'alg'는 `HS256`, `RS256` 중 선택 가능<br>

**payload**
`payload`는 담고자 하는 모든 데이터. 마찬가지로 JSON 문자열이다. 데이터는 base64 암호화되므로, 이메일과 비밀번호 같은 민감 정보는 아무나 복호화해서 읽을 수 있으므로 포함하지 않는 게 좋다.
`claims`라고도 불림

**signature**
암호화된 문자열이다. 어떤 알고리즘을 헤더 파트에서 고르던, 첫 두 파트는 base64 암호화해야 하는데, 이 부분이 유일하게 공개적으로 읽을 수 없는 부분이다. 비밀키로 암호화되므로, 이 정보는 복호화 불가능하다.
