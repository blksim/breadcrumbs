### 메이븐 빌드 라이프사이클이란
메이븐 프로젝트를 빌드하면, pom.xml 설정과 커맨드라인 옵션에 기반해 명확하게 정의된 일련의 태스크들을 실행한다.
이러한 표준 태스크들은 메이븐 빌드 라이프사이클을 생성한다.

명확하게 정의된 라이프사이클의 장점은 몇 가지 커맨드만 기억해놓으면, 프로젝트를 컴파일하고, 빌드하고, 인스톨하고, 배포할 수 있다는 것이다.


### 빌트인 라이프사이클
1. default : 프로젝트 빌드와 배포를 담당
2. clean : 프로젝트 초기화를 담당
3. site : 프로젝트 문서화 생성 담당

### 빌드 페이즈
빌드 라이프사이클은 일련의 스테이지를 거친다. 이를 build phases라고 부른다. 
* validate
* compile
* test
* package
* verify
* install
* deploy

빌드 페이즈는 순서대로 실행된다. 빌드 커맨드를 실행할 때, 구체적으로 어떤 페이즈를 실행할 건지 전달해야 한다. 
특정 페이즈 이전에 오는 페이즈들도 함께 실행된다. 예를 들어, `mvn package`는 앞선 valide, compile, test, 그리고 package를 실행할 것이다.

### 빌드 골 Build Goals
build phase는 여러 goals로 이루어져 있다. Maven goals는 프로젝트를 빌드하고 관리하는 데 기여하는 구체적인 태스크를 의미한다.
종종, maven goal은 build phase에 속하지 않는다. 이 goal들은 커맨드 라인으로 실행 가능하다.
```
$ mvn plugin-prefix:goal
$ mvn plugin-group-id:plugin-artifact-id[:plugin-version]:goal
```
이는 의존성인 tree라는 goal을 커맨드라인에서 실행하는 방법이다. 이는 어떤 build phase에도 속하지 않는다.
```
mvn dependency:tree
```

### POM이 빌드 주기에서 하는 역할
maven은 일련의 phase들과 goal들을 커맨드라인을 통해 실행한다. pom.xml 설정은 프로젝트 빌드 생명주기를 세팅하는 데 주요한 역할을 한다.
...
Packaging을 하는 가장 일반적인 방법은 POM 요소인 `packaging`을 통해서 하는 것이다. 유효한 패키징 값은 `jar`, `war`, `ear` 그리고 `pom`이 있다.
값이 정의되지 않으면, 기본값은 `jar`다.
각 패키징은 특정한 페이즈마다 속한 goal 목록을 포함하고 있다. 예를 들어, `jar` 패키징은 다음과 같은 goal들을 기본 라이프사이클의 빌드 페이즈에 연결한다.

plugin                :         goal
process-resources	      resources:resources
compile	                compiler:compile
process-test-resources	resources:testResources
test-compile	          compiler:testCompile
test	                  surefire:test
package	                jar:jar
install	                install:install
deploy	                deploy:deploy

마저 읽기
https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html
