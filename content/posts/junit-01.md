---
title: "JUnit4 assert method 예시"
date: 2019-06-17T14:23:07+08:00
---

# JUnit4 assert method 예시

assertXxx Method | 사용 목적
---|---
`assertArrayEquals("message", A, B)` | 배열 A와 B가 일치함을 확인한다.
`assertEquals("message", A, B)` | 객체 A와 B가 일치함을 확인한다. B를 파라미터로 A의 equals() 메서드를 호출한다(A.equals(B)).
`assertSame("message", A, B)` | 객체 A와 B가 같은 객체임을 확인한다. assertEquals 메서드는 두 객체의 값이 같은가를 검사하는데 반해(equals 메서드 사용), assertSame 메서드는 두객체가 동일한, 즉 하나의 객체인가를 검사한다(== 연산자 사용)
`assertTrue("message", A)` | 조건 A가 참(true)임을 확인한다.
`assertNotNull("message", A)` | 객체 A가 null임을 확인한다.

더 많은 assert method들 이 있다..

assertArrayNotEqauls, assertNotSame, assertNotTrue 등...

더 상세한 내용은 Assert API 문서 참고..

[Assert Document](https://junit.org/junit4/javadoc/4.12/org/junit/Assert.html)