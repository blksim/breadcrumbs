### [Right] : 결과가 올바른가?

'내가 작성한 코드의 정상 동작 여부를 어떻게 알 수 있는가?'
코드가 의도한 대로 결과를 산출하는 것은 중요하지만, '행복 경로 테스트'에 해당.

더 많은 숫자나 큰 수를 넣는다고 테스트의 완성도가 높아지는 건 아니다.
역으로, 어떤 작은 부분의 코드에 대해 행복 경로 테스트를 할 수 없다면 해당 코드를 이해하지 못한 것.
위 질문에 대한 답이 나올 때까지 추가 개발은 보류해야 한다.

### [B]ICEP : 경계 조건은 맞는가?
모서리 사례에 대한 테스트가 중요하다.
> 모호하고 일관성 없는 입력 값. 예를 들어 특수문자가 포함된 파일 이름
> 잘못된 양식의 데이터. 예를 들어 최상위 도메인이 빠진 이메일 주소
> 수치적 오버플로를 일으키는 계산
> 비거나 빠진 값
> 이성적 기댓값을 훨씬 벗어나는 값
> 중복 값
> .....
> 클래스가 외부에서 호출하는 api이고 클라이언트를 완전히 믿을 수 없다면 나쁜 데이터에 대한 보호가 필요

```
public class ScoreCollection {
  private List<Scorable> scores = new ArrayList<>();
  
  public void add(Scorable scorable) {
    scores.add(scorable);
  }
  
  public int arithmeticMean() {
    int total = scores.stream().mapToInt(Scorable::getScore).sum();
    return total / scores.size();
  }
}
```

```
@Test(expected=IllegalArgumentException.class)
public void throwsExceptionWhenAddingNull() {
  colection.add(null);
}
```
테스트 코드에서 먼저 null 걸러내도록 한 다음, add() 메서드에 보호절 추가

```
public void add(Scorable scorable) {
  if (scorable == null) throw new IllegalArgumentException();
  scores.add(scorable);
}
```

```
@Test
public void answersZeroWhenNoElementsAdded() {
  assertThat(collection.arithmeticMean(), equalTo(0));
}
```
0으로 나누는 오류 시의 예외 발생. 보호절 추가

```
public int arithmeticMean() {
  if (scores.size() == 0) return 0;
  //....
}
```

```
@Test
public void dealsWithIntegerOverflow() {
  collection.add(() -> Integer.MAX_VALUE);
  collection.add(() -> 1);
  
  assertThat(collection.arithmeticMean(), equalTo(1073741824));
}
```
입력값이 int 범위 벗어나는 경우

```
long total = scores.stream().mapToLong(Scorable::getScore).sum();
return (int)(total / scores.size());
```
long으로 받아서 int으로 다운캐스팅하면 int 범위 넘어가지 않음.

api 호출하는 클라이언트가 내부 팀 소속이라면 
보호절을 제거하고 주석을 남기던가, 
코드 제한 사항 자체를 문서화하기 위한 테스트를 남기는 게 좋다.
