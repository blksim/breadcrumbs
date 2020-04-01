- <자바와 JUnit을 이용한 실용주의 단위 테스트>를 읽고 정리한 내용입니다.

### [F]IRST
- 외부 시스템(데이터베이스, 파일, 네트워크 호출)에 의존하지 않는 테스트가 빠르다.
- 외부 시스템에 너무 의존하지 않도록 만든 로직 = 객체지향적인 설계 필요
- 확실히 이건 훈련이 많이 필요할 것 같다.

```
//p103~107 예제
public class StatCompiler {
  private QuestionController controller = new QuestionController();
  
  // 테스트 대상 
  public Map<String, Map<Boolean, AtomicInteger>> responsesByQuestion(List<BooleanAnswer> answers) {
    Map<Integer, Map<Boolean, AtomicInteger>> responses = new HashMap<>();
    answers.stream().forEach(answer -> incrementHistogram(responses, answer));
    return convertHistogramIdsToText(responses);
  }
  
  private Map<String, Map<Boolean, AtomicInteger>> convertHistogramIdsToText(Map<Integer, Map<Boolean, AtomicInteger>> responses,
  Map<Integer, String> questions) { // questions 추가
    Map<String, Map<Boolean, AtomicInteger>> textResponses = new HashMap<>();
 // 변경 이전 - 메소드 호출 시 어쩔 수 없이 영속성 레이어에 접근. 테스트 시 시간 많이 소요
 // responses.keySet().stream().forEach(id -> textResponses.put(controller.find(id).getText(), responses.get(id)); // find()에서 db에 접근
 // 변경 이후 - 외부 인자로 들어온 questions 맵의 id를 바로 textResponses 맵에 넣음으로써 속도 향상
   responses.keySet().stream().forEach(id -> textResponses.put(questions.get(id), responses.get(id)); // 새로 생성한 questionText의 리턴값
   return textResponses;
  }
  
  private void incrementHistogram(Map<Integer, Map<Boolean, AtomicInteger>> responses, BooleanAnswer answer) {
    Map<Boolean, AtomicInteger> histogram = getHistogram(responses, answer.getQuestionId());
    histogram.get(Boolean.valueOf(answer.getValue())).getAndIncrement();
  }
  ....  
  // 새로운 메소드를 추가해서 영속성 레이어에 접근하는 로직을 분리시킴.
  public Map<Integer, String> questionText(List<BooleanAnswer> answers) {
    Map<Ineger, String> questions = new HashMap<>();
    answers.stream().forEach(answer -> { 
      if (!questions.containsKey(answer.getQuestionId(), controller.find(answer.getQuestionId()).getText()); });
      return questions;
    }
  }
  
  // 테스트 대상 메소드에 questions 파라미터 추가
  public Map<String, Map<Boolean, AtomicInteger>> responsesByQuestion(List<BooleanAnswer> answers, Map<Integer, String> questions) {
    Map<Integer, Map<Integer, String> questions) {
      Map<Integer, Map<Boolean, AtomicInteger>> responses = new HashMap<>();
      answers.stream().forEach(answer -> incrementHistogram(responses, answer));
      return convertHistogramIdsToText(responses, question);
    }
  }
 }

메소드 내의 코드는 영속성 레이어에 의존하지만, 일단 responsesByQuestion() 메소드를 메모리상의 해시맵만으로 테스트 가능하게 됨.

@Test
public void responsesByQuestionAnswersCountsByQuestionText() {
  StatCompiler stats = new StatCompiler();
  List<BooleanAnswer> answers = new ArrayList<>();
  answers.add(new BooleanAnswer(1, true));
  answers.add(new BooleanAnswer(1, true));
  answers.add(new BooleanAnswer(1, true));
  answers.add(new BooleanAnswer(1, false));
  answers.add(new BooleanAnswer(2, true));
  answers.add(new BooleanAnswer(2, true));
  Map<Integer, String> questions = new HashMap<>();
  questions.put(1, "Tuition reimbursement?");
  questions.put(2, "Relocation package?");
  
  Map<String, Map<Boolean, AtomicInteger>> responses = stats.responsesByQuestion(answers, questions); // db 조회 없이 메소드 테스트 가능.
  
  assertThat(responses.get("Tuition reimbursement?").get(Boolean.TRUE).get(), equalTo(3));
  assertThat(responses.get("Tuition reimbursement?").get(Boolean.FALSE).get(). equalTo(1));
  assertThat(responses.get("Relocation package?").get(Boolean.TRUE).get(), equalTo(2));
  assertThat(responses.get("Reloacation package?").get(Boolean.FALSE).get(), equalTo(0));
}
```

### F[I]RST
> 좋은 단위 테스트는 검증하려는 **작은 양의 코드**에 집중합니다. 이것은 우리가 **단위**라고 말하는 정의와 부합합니다. 직접적 혹은 간접적으로 테스트 코드와 상호작용하는 코드가 많을수록 문제가 발생할 소지가 늘어납니다.

> 따라서 테스트 코드는 어떤 순서나 시간에 관계없이 실행할 수 있어야 합니다. .... 테스트에 두 번째 단언을 추가할 때 다음과 같이 스스로 질문해야 합니다. "이들 단언이 단일 동작을 검증하도록 돕는가, 아니면 새로운 테스트 이름으로 기술할 수 있는 어떤 동작을 대표하는가?"

> 테스트 메서드가 하나 이상의 이유로 깨진다면 테스트를 분할하는 것도 고려해 보세요. 집중적인 단위 테스트가 깨지면 대개 그 원인은 분명합니다.
