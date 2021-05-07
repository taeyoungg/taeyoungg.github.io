---
title:  "Java 인터페이스"
excerpt: "인터페이스의 개념과 특징"

categories:
  - Java
tags:
  - Java
  - interface
date: 2021-05-07
---
<br>

## 인터페이스란?
> 클래스들이 구현해야 하는 동작을 지정하기위한 추상 자료형

- '인터페이스'라는 명칭 그대로 내부가 없이 틀만 가지고 있는 형태

<br><br>

## 인터페이스의 특징
- 모든 매소드가 추상 메소드
  - 인터페이스의 모든 메소드는 자동으로 추상 메소드(abstract method)가 됨
  - 상속받는 하위 클래스에서 반드시 해당 메소드의 내용을 구현해야함
- 다중 상속이 가능
  - Java에서는 '다이아몬드 문제'와 같은 이유로 다중 상속을 금지하고 있음
  - 그러나 인터페이스는 메소드의 내부가 없고 상속받는 클래스에서 구현하기 때문에 다중 상속이 가능함
- 모든 멤버 변수는 public static final
- 모든 메소드는 public abstract

<br><br>

## 인터페이스를 사용하는 이유
> 상속 받는 객체들이 같은 동작을 할 수 있도록 보장하는 것이 목적
- 협업 시, 메소드를 정해진 형태로 구현할 수 있도록 함
- 코드간의 결합도를 낮출 수 있음
- 다중 상속이 가능함

<br><br>

## 인터페이스의 구현 예시

#### 인터페이스 선언
```
public interface Predator {
       boolean chasePrey(Prey p);
       void eatPrey(Prey p);
}
```
- public, abstract 와 같은 키워드를 생략할 수 있음

#### 인터페이스 상속(구현)
```
public class Lion implements Predator {

        @Override
        public boolean chasePrey(Prey p) {
               // programming to  chase prey p (specifically for a lion)
        }

        @Override
        public void eatPrey(Prey p) {
               // programming to eat prey p (specifically for a lion)
        }
}
```
- implements로 해당 인터페이스의 내용을 구현
- 자식 클래스는 반드시 인터페이스의 메소드를 재정의(오버라이딩)해야 함

#### 서브 인터페이스
```
public interface VenomousPredator extends Predator, Venomous {
        //interface body
}
```
- interface 들 간의 상속의 경우는 extends를 사용