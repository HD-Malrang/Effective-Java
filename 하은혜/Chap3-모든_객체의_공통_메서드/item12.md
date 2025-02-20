# ITEM 12. toString을 항상 재정의하라

> **모든 구체 클래스에서 Object의 toString을 재정의하자. 상위 클래스에서 이미 알맞게 재정의한 경우는 예외다. toString을 재정의한 클래스는 사용하기도 즐겁고 그 클래스를 사용한 시스템을 디버깅하기 쉽게 해준다. toString은 해당 객체에 관한 명확하고 유용한 정보를 읽기 좋은 형태로 반환해야 한다.**

- toString은 간결하면서 사람이 읽기 쉬운 형태의 유익한 정보를 반환해야 한다.
- Object의 toString은 클래스이름@16진수로 표시한 해시 코드
- 객체가 가진 모든 정보를 보여주는 것이 좋다.(강사님은 동의하지 않음)
- 값 클래스라면 포맷을 문서에 명시하는 것이 좋으며 해당 포맷으로 객체를 생성할 수 있는 정적 팩터리나 생성자를 제공하는 것이 좋다.
- toString이 반환한 값에 포함된 정보를 얻어올 수 있는 API를 제공하는 것이 좋다.
- 경우에 따라 AutoValue, 롬복 또는 IDE를 사용하지 않는게 적절할 수 있다.
    - 전화번호를 쪼개서 각 변수로 저장하는 클래스 같은 경우 한 줄, 혹은 포맷대로 나오는 것이 적절할 수 있다.
