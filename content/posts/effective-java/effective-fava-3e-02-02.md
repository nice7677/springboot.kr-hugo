---
title: "생성자에 매개변수가 많다면 빌더를 고려하라"
date: 2019-06-20T14:23:57+08:00
---

# Effective Java 3/E 2장 생성자에 매개변수가 많다면 빌더를 고려하라

정적 팩터리와 생성자에는 똑같은 제약이 하나 있다.

선택적 매개변수가 많을때 대응이 어렵다.

점층적 생성자 패턴도 쓸 수는 있지만 매개변수가 많아지면 클라이언트 코드를 작성하거나 읽기 어려워 진다.

```java
public class Hello {

    private String str;
    private String str2;
    private String str3;

    public Hello(String str) {
        System.out.println("Hello str" + str);
    }
    
    public Hello(String str, String str2) {
            System.out.println("Hello str1, str2" + str + str2);
    }
    
    public Hello(String str, String str2) {
                System.out.println("Hello str, str3 " + str + str3);
    }
        
    public Hello(String str2, String str3) {
            System.out.println("Hello str2, str3" + str2 + str3);
    }    
    
}
```
다음으로 매개변수가 많을 때 활용할 수 있는 자바 빈즈 패턴을 보자.
```java
publci class Hello {

    private String str;
    private String str2;
    private String str3;
    
    public void setStr(String str){
        this.str = str;
    }
    
    public void setStr(String str2){
            this.str2 = str2;
    }
        
    public void setStr(String str3){
            this.str3 = str3;
    }    
    
}
```
자바 빈즈 패턴에서는 점층적 생성자 패턴의 단점들이 보이지 않는다.

인스턴스를 만들기 쉽고 그 결과 더 읽기 쉬워졌다.

하지만 심각한 단점이 있으니 그건 자바 빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓인다.

일관성이 무너지는 문제로 인해 자바빈즈 패턴은 클래스를 불변으로 만들 수 없다.

하지만 이러한 문제점들에 대한 대안으로 빌더 패턴이 존재한다.

빌더는 생성할 클래스 안에 정적 멤버 클래스를 만들어두는 게 보통이다.

```java
public class Hello {

    private String str;
    private String str2;
    private String str3;
    
    public Hello() {}
    
    public static class Builder {
    
        private String str;
        private String str2;
        private String str3;
    
        public Builder() {
    
        }
        
        public setStr(String str) {
            this.str = str;
        }
        
        public setStr2(String str2) {
            this.str2 = str2;
        }
         
        public setStr3(String str3) {
            this.str3 = str3;
        }
        
        public Hello build() {
            return new Hello(this); 
        }
            
    }
    
    private Hello(Builder builder) {
        this.str = builder.str;
        this.str2 = builder.str2;
        this.str3 = builder.str3;
    }
        
}
```

쓰기 쉽고 읽기 쉬운 빌더 패턴이 나온다.

빌더 패턴은 파이썬이나 스칼라에 있는 명명된 선택적 매개변수를 흉내 낸 것이다.

만들어진 빌더 패턴은 다음과 같이 사용할 수 있다.
```java
Hello hello = new Hello.Builder()
            .setStr("He")
            .setStr2("ll")
            .setStr3("o")
            .build();
```