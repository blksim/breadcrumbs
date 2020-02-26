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

...업데이트중...
