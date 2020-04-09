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

### 경계 조건에서는 CORRECT를 기억하라
> 
* [C]onformance(준수): 값이 기대한 양식을 준수하고 있는가?
* [O]rdering(순서) : 값의 집합이 적절하게 정렬되거나 정렬되지 않았나?
* [R]ange(범위) : 이성적인 최솟값과 최댓값 안에 있는가?
* [R]eference(참조) : 코드 자체에서 통제할 수 없는 어떤 외부 참조를 포함하고 있는가?
* [E]xistence(존재) : 값이 존재하는가(non-null), 0이 아니거나, 집합이 존재하는가 등
* [C]ardinality(기수) : 정확히 충분한 값들이 있는가?
* [T]ime(절대적 혹은 상대적 시간) : 모든 것이 순서대로 일어나는가? 정확한 시간에? 정시에?
(127p)

### B[I]CEP - 역 관계를 검사할 수 있는가?

```
public class Profile {
  private Map<String, Answer> answers = new HashMap<>();
  //...
  
  public void add(Answer answer) {
    answers.put(answer.getQuestionText(), answer);
  }
  
  //...
  int[] ids(Collection<Answer> answers) {
    return answers.stream().mapToInt(a -> a.getQuestion().getId().toArray());
  }
  
  public List<Answer> find(Predicate<Answer> pred) {
    return answers.values().stream().filter(pred).collect(Collectors.toList());
  }
}

@Test
public void findsAnswersBasedOnPredicate() {
  profile.add(new Answer(new BooleanQuestion(1, "1"), Bool.FALSE));
  profile.add(new Answer(new PercentileQuestion(2, "2", new String[]{}), 0));
  profile.add(new Answer(new PercentileQuestion(3, "3", new String[]{}), 0));
  
  List<Answer> answers = profile.find(a -> a.getQuestion().getClass() == PercentileQuestion.class);
  
  assertThat(ids(answers), equalTo(new int[] {2, 3});
```

```
List<Answer> answersComplement = profile.find(a -> a.getQuestion().getClass() != PercentileQuestion.class);
List<Answer> allAnswers = new ArrayList<Answer>();
allAnswers.addAll(answersComplement);
allAnswers.addAll(answers);

assertThat(ids(allAnswers), equalTo(new int[] { 1, 2, 3 } )); // 긍정 답변과 역 답변을 합하면 전체가 돼야 함. 보어 관계를 활용한 교차 검사
```

### BI[C]EP
다른 수단을 이용한 교차 검증도 가능해야 한다. 프로덕션 수준에 쓸 수 없더라도, 다른 라이브러리나 클래스의 조각 데이터를 통해 같은 결과가 나오는 지 테스트할 수 있다면 마다할 필요가 없음.
