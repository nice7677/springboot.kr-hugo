---
title: "Java serialVersionUID에 대하여"
date: 2019-12-10T15:09:19+09:00
---

**serialVersionUID**

- serialVersionUID에 란 무엇인가?
    - 객체를 ``직렬화`` 하여 ``역직렬화``를 할때 사용한다.
    - 직렬화 외에도 ``보안``등의 용도에 사용됬다.
    - 객체에 대한 고유번호(?)으로 생각하면 된다.
    - serialization가 사용되는 클래스의 경우에 명시적으로 선언해 주는것을 권유한다.


- 어떻게 사용하는가?
    - serialVersionUID를 사용하기 위해서는 static, final, long 타입이여야 한다.
    - ``private``을 추천함.
    - example)
        ```java
        private static final long serialVersionUID = 11L;
        ```    


- 선언을 하지 않을 경우에는?
    - 선언을 하지 않은 경우에는 실행하는 시점에서 serialization을 담당하는 모듈을 통해 자동생성되어 디폴트값이 만들어진다.
    - 그 알고리즘은 ``Java(TM) Object Serialization Specification``에 정의 된 것을 따른다.
    - 디폴트로 생성된 serialVersionUID는 매우 민감하게 반영 하기 떄문에 컴파일러에 따라 Exception이 발생 할 수 있다.
    - Exception 발생 방지를 위해서라도 명시를 해주는 것이 좋다.
    
**참고**

- [serialVersionUID 선언이유에 대한 포스팅](http://nom3203.egloos.com/2537294)
- [JAVA serialVersionUID 사용법](https://jinseongsoft.tistory.com/39)
- [serialVersionUID를 선언하는 이유](https://seongilman.tistory.com/53)

