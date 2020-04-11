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
