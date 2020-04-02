### 문제
* 사전 행위 - application-${production}.properties 파일의 spring.datasource.initialization-mode 값을 로컬 서버에서 구동 시 'always'로 주어 최초 스키마가 생성되도록 한 다음, 
개발 서버 packaging 전 'never'로 값을 바꾸어, 서버를 내렸다 올려도 데이터가 초기화되지 않도록 함.
* 문제 정의- 하지만 executable jar로 packaging하여 deploy시, db schema가 생성돼있지 않아 db 접근 기능 자체가 진입이 안 되는 문제

### 원인
- jar로 packaging 전 ide에서 구동해볼 때 생성되는 db 파일 경로와, packaging된 jar 실행 시 생성되는 db 파일 경로가 다르다는 걸 몰랐음.
- 개발서버에서 jar 구동해 보면, log 디렉토리와 db 파일이 jar 파일과 같은 레벨에 생성되므로 내부에 이미 생성됐던 db 파일은 아무 쓸모가 없었다.
- 당연히 jar과 같은 레벨의 db 파일은 비어있었다. 즉 스키마 생성이 안 되어 있었다.

### 해결
- packaging 시 always로 값을 준 상태로 개발 서버에 올리고 당연히 **실행** 까지 해야 스키마 생성된다.
- db 파일 제대로 초기화 됐는지 확인 후, spring.datasource.initialization-mode를 다시 never로 바꾼 후 다시 packging하여 서버에 올려줬다.
- 그러면 서버 내려도 데이터가 날아가지 않는다!

### 배운 점
- 리눅스 기본 명령어 숙지하기. 디렉토리 내 파일 정보 꼭 확인
- 어떤 포맷의 deploy든 log는 어디에, db 파일은 어디에 생성되는지, 디버깅은 어떻게 하는지 알아놓기
