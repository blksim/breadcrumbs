https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html 을 번역한 글

Maven은 build lifecycle이라는 중심 개념에 기반한다. 이는 특정한 artifact(project)를 빌드하거나 distribute하는 것이 명확하게 정의돼있음을 뜻한다.

프로젝트를 빌드하려는 이에게 필요한 건 어떤 메이븐 프로젝트도 빌드할 수 있는 몇몇 명령어들이다. 그리고 POM은 원하는 결과를 얻도록 해준다.

세 가지의 내장 빌드 생명주기가 존재한다:default, clean 그리고 site가 그것이다. `default` 생명주기는 프로젝드 배포를 관리하고, `clean` 생명주기는 프로젝트 초기화를, `site` 생명주기는 프로젝트의 사이트 문서를 생성한다.

예를 들어, default 생명주기는 다음과 같은 단계를 거친다.
* `validate` - 프로젝트의 유효성과 모든 필요 정보가 포함돼있는지 여부를 검증한다.
* `compile` - 프로젝트의 소스 코드를 컴파일한다.
* `test` - 컴파일한 소스 코드를 적절한 단위 테스트 프레임워크를 사용해 테스트한다. 이 테스트들은 같이 패키징되거나 배포될 필요가 없다.
* `package` - 컴파일된 코드와 JAR와 같은 distributable한 규격으로 패키징한다. 
* `verify` - quality criteria가 충족되었는 지 확인하기 위한 통합 테스트 결과를 검증한다.
* `install` - 다른 프로젝트의 의존성을 사용하기 위해 패키지를 로컬 저장소에 설치한다.
* `deploy` - 빌드 환경이 갖춰지면, 최종 패키지를 다른 개발자와 프로젝트가 공유할 수 있도록 원격 저장소에 복사한다.

이 생명주기 단계들 (여기에 나열돼있지 않은 단계들도) `default` 생명주기를 완수하기 위해 순서대로 실행된다. 위에 주어진 생명주기 단계는, default 생명주기가 사용될 때, 메이븐은 처음으로 프로젝트 유효성을 검증하고, 그런 다음 소스 코드를 컴파일하고, 테스트하고,  바이너리를 jar와 같은 형식으로 패키징하고, 통합 테스트를 수행한 다음, 통합 테스트를 검증하고, 로컬 저장소에 검증된 패키지를 설치한 다음, 원격 저장소로 배포한다는 것을 의미한다.

### 흔히 사용하는 커맨드 명령어
원하는 결과에 걸맞는 단계를 선택해야 한다. jar가 필요하면 `package`를, 단위 테스트 실행을 원하면 `test`를 입력한다.
만약 뭘 해야할 지 확실하지 않다면, 권장하는 단계는 
```
mvn verify
```
이 명령어는 `verify`를 수행하기 전, 각각의 default 생명주기 단계를 순서대로 실행한다.(`validate`, `compile`, `package`, etc). `verify`의 경우 별도로 호출해야 할 명령어는 마지막 빌드 단계 뿐이다.
대부분의 경우 `package`와 효과가 유사하다. 하지만, 통합 테스트가 존재할 경우, 같이 실행된다. `verify` 단계 동안 몇몇 추가적인 확인이 이루어질 수 있다. 
빌드 환경에서는, 다음의 명령 호출이 공유 저장소에 프로젝트를 cleanly하게 빌드하고 배포해준다.
```
mvn clean deploy
```
멀티 모듈 시나리오(프로젝트 당 하나 이상의 하위 프로젝트가 있는 경우) 같은 명령이 사용될 수 있다. 메이븐은 모든 하위 프로젝트를 탐색하며 각각 `clean`을 실행하고, 그런 다음 `deploy`를 실행한다. (모든 빌드 단계를 포함하여)

### build phase는 plugin goals로 이루어져 있다.
그러나, 빌드 페이즈가 빌드 생명주기에서 특정한 단계에 관련있다고 해도, 그 책임은 달라질 수 있다. 그리고 이는 빌드 페이즈에 관련된 plugin goals를 선언함으로써 이루어진다.

plugin goal은 프로젝트 빌드와 관리에 기여하는 구체적인 태스크를 나타낸다. 이는 아예 없거나 더 많은 빌드 페이즈와 관련돼있다. 어떤 빌드 페이즈에도 관련돼있지 않은 goal은 직접 호출을 통해 빌드 생명주기 외부에서도 실행될 수 있다.
실행 순서는 어떤 goal과 빌드 페이즈가 먼저 호출되느냐에 달려있다. 예를 들어, 아래와 같은 명령어를 보자. 
`clean`과 `package` 인자는 빌드 페이즈에 해당하고, `dependency:copy-dependencies`는 plugin의 goal에 해당한다.
```
mvn clean dependency:copy-dependencies package
```
이것이 실행되고 있었다면, `clean` 페이즈는 먼저 실행될 것이고(이는 선행하는 clean 생명주기와 `clean` 페이즈 자체를 모두 실행할 것임을 의미한다.), 그런 다음
`dependency:copy-dependencies` goal이, 마지막으로 `package` 페이즈(default 생명주기의 모든 선행 빌드 페이즈를 포함)가 실행될 것이다.

게다가, goal이 하나 이상의 빌드 페이즈에 연관돼있다면, goal은 관련된 모든 페이즈에서 호출될 것이다.

더 나아가, 빌드 페이즈는 또한 아예 없거나 더 많은 goal과 연관될 수 있다. 만약 빌드 페이즈가 goal을 갖고 있지 않다면, 그 빌드 페이즈는 실행되지 않을 것이다. 하지만 하나 혹은 하나 이상의 goals가 바인딩돼있다면, 모든 goal이 실행될 것이다.
참고 : 메이븐 2.0.5 이상에서는, 페이즈 하나에 바인드된 여러 개의 goal들은 POM에 선언된 순서대로 실행된다. 하지만 같은 플러그인에 대한 다수의 인스턴스는 지원되지 않는다
같은 플러그인의 다수 인스턴스들은 메이븐 2.0.11 이상부터 그룹핑되어 실행되고 순서가 정해진다.

### 어떤 페이즈들은 명령어로 잘 쓰지 않는다.
하이픈화된 말들(`pre-*`, `post-*`, 혹은 `process-*`)을 포함하는 페이즈들은 대개 커맨드라인에서 직접적으로 쓰이지 않는다.
이 페이즈들은 외부적으로 빌드에 유용한 즉각적인 결과를 제공하진 않는다. `integration-test`를 호출할 경우, 환경은 대기 상태에 남겨질 수 있다.

Jacoco나 Tomcat, Cargo, Docker같은 실행 컨테이너 플러그인은 통합 테스트 컨테이너 환경을 준비하기 위해 goal을 `pre-integration-test` 페이즈에 바인드한다. 플러그인들은
커버리지 통계와 통합 테스트 컨테이너의 해제하기 위한 `post-integration-test` 페이즈에도 goal을 바인드한다.

Failsafe와 코드 커버리지 플러그인은 `integration-test`와 `verify` 페이즈에 goal을 바인딩한다. `verify` 페이즈 다음에 테스트 및 커버리지 리포트가 가능하다.만약 
`integration-test`가 커맨드라인에서 호출된다면, 리포트가 생성되지 않는다. 더 안좋은 것은 통합 테스트 컨테이너 환경이 그대로 대기 상태에 걸리게 된다는 것이다.
Tomcat 웹서버나 Docker 인스턴스는 여전히 실행중이고, 메이븐은 스스로 중단하지 못할 수도 있다.

### 빌드 생명주기를 사용하기 위한 프로젝트 설정
빌드 생명주기를 사용하는 건 단순하지만, 프로젝트를 메이븐으로 빌드할 때, 각각의 빌드 페이즈에 어떻게 태스크를 할당할 것인가?

#### 패키징하기
먼저, 가장 흔한 방식은 프로젝트를 POM 요소인 `<packaging>`과 같은 이름으로 정하는 것이다.
몇몇 유효한 패키지 값은 `jar`, `war`, `ear` 그리고 `pom`이다. 만약 패키징 값이 정해지지 않았다면 기본값은 `jar`다.

각 패키징은 특정한 페이즈와 바인드되는 goal 목록을 갖고 있다. 예를 들어, `jar`로 패키징할 경우 다음과 같은 goal들이 기본 생명주기의 빌드 페이즈에 바인드된다.

| *Phase*                  | *plugin:goal*             |
|------------------------|-------------------------|
| `process-resources`      | `resources:resources`     |
| `compile`                | `compiler:compile`         |
| `process-test-resources` | `resources:testResources` |
| `test-compile`           | `compiler:testCompile`    |
| `test`                   | `surefire:test`           |
| `package`                | `jar:jar`                 |
| `install`                | `install:install`         |
| `deploy`                 | `deploy:deploy`           |

이는 거의 표준적인 바인딩 셋이다. 하지만, 몇몇 패키징은 다르게 handle한다. 예를 들어, 프로젝트 자체가 순수하게 메타데이터인 경우(패키징 값이 `pom`일 때)에는 goal들이 `install`과 `deploy` 페이즈에만 바인드된다.

몇몇 패키징 타입이 사용 가능하려면, POM의 `build` 부분에 특정한 플러그인을 추가한 다음, `<extensions>true</extensions>` 구문으로 플러그인을 구체화시켜주어야 한다. 

#### 플러그인들
프로젝트에 goal들을 추가하는 두 번째 방법은 플러그인을 설정하는 것이다. 플러그인은 메이븐에 goal들을 전달해주는 artifacts를 말한다. 더 나아가, 플러그인은 하나 혹은 그 이상의 그 플러그인의 가용성을 나타내는 각각의 goal들을 갖고 있을 수도 있다. 예를 들어, 컴파일러 플러그인은 두 개의 goal을 갖고 있다 : `compile` 그리고 `testCompile`. 전자는 소스코드를 컴파일하고, 후자는 테스트콛르르 컴파일한다.

다음 섹션에서 보면 알듯이, 플러그인은 어떤 생명주기 페이즈에 goal을 바인드할 지를 암시하는 정보를 포함할 수 있다. 플러그인만을 추가하는 것으로는 충분하지 않으며, 빌드 시 실행돼야 할 goal들을 반드시 명시해주어야 한다.

이러한 goal들은 선택한 패키징의 생명주기에 이미 바인드된 goal들에 더해진다. 하나 이상의 goal이 특정한 페이즈에 바인드되면, 순서는 POM에 설정된 대로 따른다. `<executions>` 요소는 특정한 goal들의 순서에 대한 제어권을 다룰 수 있는 태그다.

예를 들어, Modello 플러그인은 `generate-sources` 페이즈의 `modello:java`라는 goal에 기본으로 바인드된다. 그러므로 Modello 플러그인을 사용하는 것은 model에서 소스를 생성하고 빌드 시 통합하게 하려면, POM의 `<plugins>` 섹션의 `<build>` 내에 다음과 같은 내용을 추가하면 된다.

```
...
<plugin>
  <groupId>org.codehaus.modello</groupId>
  <artifactId>modello-maven-plugin</artifactId>
  <version>1.8.1</version>
  <executions>
    <execution>
      <configuration>
        <models>
          <model>src/main/mdo/maven.mdo</model>
        </models>
        <version>4.0.0</version>
      </configuration>
    </execution>
  </executions>
</plugin>
...
```
`<executions>` 요소가 왜 저기에 있는지 궁금할 것이다. 같은 goal을 여러 번 각기 다른 설정과 함께 실행할 수 있어서다. 각각의 실행에는 ID가 주어지는데 inheritance에 있는 동안이나, 애플리케이션 프로필이
goal 설정이 병합되거나 추가적인 실행으로 전환될 지를 결정할 수 있다.
여러 개의 executions가 특정한 페이즈에 주어질 경우, 물려받은 execution들이 먼저 실행되며, POM에 정의된 순서대로 실행된다.
`modello:java`의 경우, 이는 `generate-sources` 페이즈에만 해당된다. 하지만 몇몇 goal들은 하나 이상의 페이즈에서 사용될 수 있고, 이는 기본값이 아닐 수도 있다. 이런 경우 페이즈를 직접 정의할 수 있다. 예릃 들어, `display:time`은 커맨드라인에 현재 시간을 표시해주는 goal을 갖고 있다고 가정하고 이를 `process-test-resource' 페이즈를 테스트가 시작됐을 때 실행되도록 하고 싶다면, 이렇게 설정하면 된다.
```
...
  <plugin>
    <groupId>com.mycompany.example</groupId>
    <artifactId>display-maven-plugin</artifactId>
    <version>1.0</version>
    <executions>
      <execution>
        <phase>process-test-resources</phase>
        <goals>
          <goal>time</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
  ...
```

### 생명주기, 내장 생명주기 바인딩 참고
다음의 목록은 `default`, `clean` 그리고 `site` 생명주기의, 각기 순서대로 실행되는 빌드 페이즈 목록이다.
링크를 참고. 너무 길어서 다 못옮김


