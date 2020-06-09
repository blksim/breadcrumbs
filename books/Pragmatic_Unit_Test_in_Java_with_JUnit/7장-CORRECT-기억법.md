### [C]ORRECT : Conformance(준수)
- 양식 있는 문자열 데이터가 규칙을 준수하는 지를 검증.
- 브레인스토밍할 수록 많은 경계 조건을 만들어낼 수 있다.
- 핵심은 여러번 검증하지 않는 것. 예를 들어 UI에서 입력시 넘어간 데이터가 공개된 API 인자로 쓰인다면, 그 단계에서만 검증하면 그 필드를 인자로 넘길 때마다 검사하지 않아도 된다.
- 컨트롤러에서 인자 validation 하고 나면 서비스나 영속성 계층에서는 굳이 또 검증할 필요 없는 그런 경우

### C[O]RRECT : Ordering(순서)
```
@Test
public void answerResultsInScoredOrder() {
  smeltInc.add(new Answer(doTheyReimburseTution, Bool.FALSE)); // smeltInc사에 대한 부정답변 추가
  pool.add(smeltInc);
  langrsoft.add(new Answer(doTheyReimburseTutioin, Bool.TRUE)); // langrsoft사에 긍정답변 추가
  pool.add(langrsoft);
  
  pool.score(soleNeed(doTheyReimburseTution, Bool.TRUE, Weight.Important)); // 가중치 계산
  List<Profile> ranked = pool.ranked(); //
  
  assertThat(ranked.toArray(), equalTo(new Profile[]{ langrsoft, smeltInc })); // 실패; 
}
```

```
private Criteria soleNeed(Question question, int value, Weight weight) {
  Criteria criteria = new Criteria();
  criteria.add(new Criterion(new Answer(question, value), weight));
  return criteria;
}
```

```
public void score(Criteria criteria) {
  for (Profile profile: profiles) {
    profile.matches(criteria);
  }
}

public List<Profile> ranked() { //----> 실패 원인
  Collections.sort(profiles, (p1, p2) -> ((Integer)p1.score()).compareTo(p2.score());
  return profiles;
}
```

```
public List<Profile> ranked() {
  Collections.sort(profiles, (p1, p2) -> ((Integer)p2.score()).compareTo(p1.score()); // compareTo 인자 순서 변경
  return profiles;
}
```

### CO[R]RECT : Range(범위)
자바의 기본형을 너무 자주 사용할 경우 지나치게 크거나 음수인 값이 넘어갈 여지를 주게 된다.
이 때 범위를 제약하는 로직을 클래스로 추상화할 수 있다.
```
public class BearingTest {
  @Test(expected=BearingOutOfRangeException.class)
  public void throwsOnNegativeNumber() {
    new Bearing(-1);
  }
  
  @Test(expected=BearingOutOfRangeException.class)
  public void throwsWhenBearingTooLarge() {
    new Bearing(Bearing.MAX + 1);
  }
  
  @Test
  public void answersValidBearing() {
    assertThat(new Bearing(Bearing.MAX).value(), equalTo(Bearing.MAX));
  }
  
  @Test
  public void answersAngleBetweenItAndAnotherBearing() {
    assertThat(new Bearing(15).angleBetween(new Bearing(12)), equalTo(3));
  }
  
  @Test
  public void angleBetweenIsNegativeWhenThisBearingSmaller() {
    assertThat(new Bearing(12).angleBetween(new Bearing(15)), equalTo(-3));
  }
}
```
```
public class Bearing { 
  public static final int MAX = 359;
  private int value;
  
  public Bearing(int value) {
    if (value < 0 || value > MAX) throw new BearingOutOfRangeException();
    this.value = value;
  }
  
  public int value() {
    return value;
  }
  
  public int angleBetween(Bearing bearing) {
    return value - bearing.value;
  }
}
```

### COR[R]ECT : Reference(참조)
- 테스트 범위를 넘어서는 것을 참조하고 있지는 않은지
- 외부 의존성은 무엇인지
- 특정 상태에 있는 객체를 의존하고 있는지 여부
- 반드시 존재해야 하는 그 외 다른 조건들.

### CORR[E]CT : Existence(존재)
> 우리는 프로그래머로서 보통 행복 경로를 만드는 데 무엇보다 주력합니다. 예상하는 데이터가 없을 때 발생하는 불행 경로는 그 다음에 생각하고는 합니다.
> null값, 0, 빈 문자열과 다른 무정부주의자들의 덫들로 충분히 테스트하세요.

### CORRE[C]T : Cardinality(기수)
- zero-one-many 디자인
- 테스트할 작업 목록을 도출하는 데 도움이 된다.
> JJ 팬케이크 가게에서 상위 열 개의 음식 목록을 유지해야 한다고 합니다. 주문이 나올 때마다 이 상위 목록을 갱신하여 실시간으로 팬케이크 보스 아이콘 앱에 결과를 표시합니다. 개수의 개념은 테스트할 작업 목록을 도출하는 데 도움을 줍니다.
* 목록에 항목이 하나도 없을 때 보고서 출력하기
* 목록에 항목이 하나만 있을 때 한 항목 추가하기
* 목록에 항목이 없을 때 한 항목 추가하기
* 목록에 항목이 아직 열 개 미만일 때 한 항목 추가하기
* 목록에 항목이 이미 열 개가 있을 때 한 항목 추가하기

이 때 상위 음식 목록 갯수가 바뀌어도 바뀌어야 할 코드는 상수 값에 해당하는 한 줄 뿐이다.
> 테스트 코드는 0, 1, n이라는 경계 조건에만 집중하고 n은 비즈니스 요구 사항에 따라 바뀔 수 있습니다.

### CORREC[T] : Time(시간)
- 상대적 시간(시간 순서)
- 절대적 시간(측정된 시간)
> 메서드들의 호출 순서가 맞지 않았을 때 어떤 일이 일어날 지 생각해 봅시다. 다양한 대안 순서를 생각해 보세요.
> 상대적인 시간은 타임아웃 문제도 포함할 수 있습니다. 수명이 짧은 자원에 대해 코드가 얼마나 기다릴 수 있는지 결정해야 합니다.
시간에 민감한 테스트들의 경우 하위 라이브러리에 의존하지 말고 시스템 시계에 의존하는 테스트를 작성할 것.

> 모든 경계를 알 필요가 있습니다. 테스트에는 더욱 그렇습니다. 경계 조건들은 자주 고약하고 작은 결함들을 만들어 내는 곳입니다.
