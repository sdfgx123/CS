# Java Fix 레퍼런스

## 1. LocalDate 말일 이슈
기존 진행했던 스프링 부트 프로젝트에서 테스트 코드를 추가하려고 서버를 시작했으나 UnsatisfiedDependencyException 과 함께 서버가 실행되지 않았다.
StackTrace를 따라가 보니 Service 클래스에서 DateTimeException이 터졌다. 해당 코드를 찾아봤다.

``` java
private static final LocalDate endOfLastMonth = today.minusMonths(1).withDayOfMonth(today.lengthOfMonth());
```

문제가 된 부분은 위의 코드이다. 문제의 원인은 LocalDate 객체 생성 시 유효하지 않은 날짜 11월 31일을 사용하려 했기 때문이다.
2023년에는 11월 31일이 존재하지 않는다.

위의 코드의 진행 순서는 다음과 같다:
* today의 월을 하나 줄임
* endOfLastMonth를 today의 월의 마지막 날짜로 설정하려고 시도

문제를 해결을 위해 코드를 아래와 같이 수정했다:
``` java
private static final LocalDate endOfLastMonth = today.withDayOfMonth(1).minusMonths(1).withDayOfMonth(today.minusMonths(1).lengthOfMonth());
```

수정된 코드의 진행 순서는 다음과 같다:
* today의 날짜를 해당 월의 첫째 날로 설정
* 그 월을 하나 줄임
* 이전 달의 마지막 날짜로 endOfLastMonth를 초기화

이렇게 하면 더 이상 에러가 발생하지 않는다.