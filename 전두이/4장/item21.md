# 인터페이스는 구현하는 쪽을 생각해 설계하라

## **디폴트 메서드**

- 디폴트 메서드는 인터페이스 내에서 구현 코드가 있는 메서드
- 과거에는 인터페이스에 메서드의 구현을 넣을 수 없었으나, Java 8 이후 디폴트 메서드를 사용하면 인터페이스에서 메서드를 구현할 수 있게 되었다.

### **장점과 단점**

- 장점: 기존 인터페이스에 새로운 메서드를 추가할 때, 기존 구현체에 영향을 주지 않으므로 **하위 호환성**을 유지할 수 있다.
- 단점: 디폴트 메서드가 너무 범용적이거나 복잡하게 작성되면, **불변식**을 깨뜨릴 수 있기 때문에 신중하게 사용해야 한다.
- **디폴트 메서드를 사용할 때 주의사항**
    - 디폴트 메서드를 추가하는 일이 필요한 경우가 아니라면, **기존 인터페이스를 수정하는 일**을 피하는 것이 좋다.
    - 또한, 디폴트 메서드는 기존 메서드의 시그니처를 변경하거나 메서드를 제거하는 용도로 사용해서는 안 된다.
    - 디폴트 메서드를 추가하기 전에 **기존 구현체들과 충돌이 없는지** 확인해야 하며, 새로운 인터페이스를 설계할 때는 **테스트**를 반드시 거쳐야 한다.


---

## **결론**

- 디폴트 메서드는 매우 유용하지만, **인터페이스를 설계할 때는 신중함**이 필요하고, 꼭 필요한 경우에만 사용해야 한다.