# 클래스와 멤버의 접근 권한을 최소화하라

### 정보 은닉(캡슐화)의 중요성

- 잘 설계된 컴포넌트는 내부 구현을 완벽히 숨겨 외부에서 접근할 수 없게 하고, API를 통해서만 소통하게 한다.
- 장점:
    - 개발 속도 향상: 병렬로 개발이 가능
    - 유지보수 비용 절감: 컴포넌트를 빠르게 이해하고 교체 부담이 적음
    - 성능 최적화 용이: 특정 컴포넌트만 독립적으로 최적화 가능
    - 재사용성 향상: 독립적으로 동작하는 컴포넌트는 다양한 환경에서도 재사용 가능

2. 접근 제한자 및 접근성 원칙
- 접근 제한자는 클래스의 내부 정보 노출을 줄이는 데 필수적
    - `private`: 해당 클래스 내부에서만 접근 가능
    - `package-private` (기본 접근 수준): 같은 패키지 내에서 접근 가능
    - `protected`: 패키지 내와 하위 클래스에서 접근 가능
    - `public`: 모든 클래스에서 접근 가능
- 상속 시 접근 제한 규칙:
    - 하위 클래스는 상위 클래스에서 정의한 메서드의 접근 수준을 좁힐 수 없음. 이는 리스코프 치환 원칙을 따르기 위함이다.

3. 테스트 시 접근 범위

- 테스트 목적으로 접근 범위를 넓히는 것은 가급적 필요한 최소한의 수준으로 한다.

4. Public 필드 사용에 대한 주의사항

- public 인스턴스 필드는 피하는 것이 좋음
    - 이유: 불변성을 해치고, 필드 변경 시 동기화 등의 추가 작업이 불가능하여 스레드 안전을 확보하기 어려움
- 예외: public static final 상수 필드는 가능하나, 참조하는 객체가 불변인지 확인이 필요