# Java Version 주요 특징

### ****J2SE 5(2004년 9월)****

- **Generics**
   - Collection에 프로그래머가 원하는 객체 타입을 명시
- **Annotation**
- **Concurrency API**
- **Enumeration**
   - 열거형(Enum) 기능
- **Auto Boxing/Unboxing**
   - 개발자가 기본형 데이터를 래퍼 클래스로 직접 변환하지 않아도 자동 변환

### ****Java SE 6(2006년 12월)****

- **표기가 J2SE에서 Java SE로 변경**
- **가비지 컬렉터 G1(Garbage First) GC을 오직 테스트용으로 추가**

### ****Java SE 7(2011년 7월)****

- **Diamond Operator <>**
   - General Class 초기화 시 Type Interface를 지원
- **File NIO 라이브러리 추가**
- **switch 문에서 String 사용**
- **try-with-resources 추가**
- **멀티 catch 추가**
- **ForkJoinPool 클래스 제공**
   - 분할-정복 방식

### ****Java SE 8(2014년 3월)****

- **Lambda Expression**
- **Interface의 Default Methods**
   - default 메서드를 통한 완전 구현체 메서드 가능
- **Method Reference**
   - `(Soccer s) -> s.getGoal()`  ⇒ `Soccer::getGoal`
- **Stream API**
- **새롭게 추가된 날짜와 시간 API**
- **PermGen Area 제거**
   - Class의 Meta 정보, Method의 Meta 정보, Static Object, Class와 관련된 배열 객체 Meta 정보, JVM 내부적인 객체들과 JTI의 최적화 정보
   - Metaspace 로 이전되었으며 이는 OS가 관리, JVM이 관리하지 않기 때문에 `java.lang.OutOfMemoryError: PermGen space` 의 빈번한 발생을 방지

### ****Java SE 9(2017년 9월)****

### ****Java SE 10(2018년 3월)****

### ****Java SE 11(2018년 9월)****

### ****Java SE 12(2019년 3월)****

### ****Java SE 13(2019년 9월)****

### ****Java SE 14(2020년 3월)****

### ****Java SE 15(2020년 9월)****

### ****Java SE 16(2021년 3월)****

### ****Java SE 17(2021년 9월)****