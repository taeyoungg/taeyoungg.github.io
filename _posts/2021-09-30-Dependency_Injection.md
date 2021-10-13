---
title:  "Dependency Injection"
excerpt: "의존성 주입"

categories:
  - Spring
tags:
  - Spring
  - 스프링
date: 2021-09-30
---
<br>

## Dependency Injection (의존성 주입) 이란?
객체 지향 프로그래밍에서 통용되는 개념으로, 하나의 객체가 다른 객체의 의존성을 제공하는 기술이다.<br>
객체를 필요로 하는 클래스 내에서 직접 객체를 생성하게 되면, 이후에 클래스 수정 없이 인스턴스의 생성을 변경하는 것이 불가능하기 때문에 결합도가 높아지며 유연하지 못하게 된다.<br>
그러나 의존성 주입은 외부에서 생성된 객체를 받기 때문에 결합도를 낮출 수 있고 런타임시에 의존 관계가 결정되기 때문에 유연한 구조를 갖게 된다. 따라서 코드의 재사용을 높이고 단위 테스트의 편의성을 높일 수 있다.

## Spring 에서의 DI 방법
### 수정자 주입

```java
@Service
public class StudentServiceImpl implements StudentService {

    private CourseService courseService;

    @Autowired
    public void setCourseService(CourseService courseService) {
        this.courseService = courseService;
    }

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }
}
```

- set 메소드를 통해 외부에서 CourseService 객체를 주입
- studentMethod() 메소드는 CourseService 객체에 의존
- StudentServiceImpl 객체를 생성하고 CourseService 객체를 주입하지 않고 studentMethod() 메소드를 호출하게 되면, *NullPointerException* 발생

### 필드 주입

```java
@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private CourseService courseService;

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }

}
```

- 스프링 컨테이너를 통해 CourseService 객체를 주입
- 수정자 주입과 유사한 방식으로 동작하기 때문에 수정자 주입의 단점을 그대로 가짐

### 생성자 주입

```java
@Service
public class StudentServiceImpl implements StudentService {

    private final CourseService courseService;

    @Autowired
    public StudentServiceImpl(CourseService courseService) {
        this.courseService = courseService;
    }

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }
}
```

- StudentServiceImpl 객체를 생성할 때 생성자를 통해 CourseService 객체를 할당
- final을 사용하여 무조건 선언과 동시에 초기화 되도록 할 수 있음
- 따라서 수정자 주입 방식에서의 *NullPointerException* 을 방지할 수 있음

## 순환 참조
```java
public interface CourseService {
    void courseMethod();
}

@Service
public class CourseServiceImpl implements CourseService {

    @Autowired
    private StudentService studentService;

    @Override
    public void courseMethod() {
        studentService.studentMethod();
    }
}
```

```java
public interface StudentService {
    void studentMethod();
}

@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private CourseService courseService;

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }
}
```

- 위와 같이 서비스들 간에 의존 관계가 생기는 경우, 서로 호출을 끊임없이 주고 받다가 *StackOverflowError* 가 발생하는 순환 참조 문제가 발생할 수 있음
- 필드 주입과 수정자 주입 방식에서는 코드가 호출 되기 전까지 순환 참조를 확인할 수 없음

```java
@Service
public class CourseServiceImpl implements CourseService {

    private final StudentService studentService;

    @Autowired
    public CourseServiceImpl(StudentService studentService) {
        this.studentService = studentService;
    }

    @Override
    public void courseMethod() {
        studentService.studentMethod();
    }
}

@Service
public class StudentServiceImpl implements StudentService {

    private final CourseService courseService;

    @Autowired
    public StudentServiceImpl(CourseService courseService) {
        this.courseService = courseService;
    }

    @Override
    public void studentMethod() {
        courseService.courseMethod();
    }
}
```

- 생성자 주입 방식은 컨테이너가 빈을 생성하는 시점에서 순환 참조를 캐치할 수 있음
- 따라서 코드가 실행 되기 전 구동 단계에서 미리 에러를 방지 할 수 있음

## [Lombok] @RequiredArgsConstructor
- Lombok의 @RequiredArgsConstructor 어노테이션을 이용하면 생성자 주입을 간편하게 사용할 수 있음

```java
@Service
@RequiredArgsConstructor
public class RequiredArgsConstructorDIServiceExample {
  private final FirstRepository firstRepository;
  private final SecondRepository secondRepository;
  private final ThirdRepository thirdRepository;
  
  // ...
}
```

- 컴파일 결과

```java
@Service
public class RequiredArgsConstructorDIServiceExample {
  @ConstructorProperties({"firstRepository", "secondRepository", "thirdRepository"})
  public RequiredArgsConstructorDIServiceExample(FirstRepository firstRepository, SecondRepository secondRepository, ThirdRepository thirdRepository) {
    this.firstRepository = firstRepository;
    this.secondRepository = secondRepository;
    this.thirdRepository = thirdRepository;
  }
}
```