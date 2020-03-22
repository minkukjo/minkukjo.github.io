---
layout: post
title: 2020 카카오공채 괄호 변환
subtitle: ""
categories: cs
tags: Algorithm
comments: false
---

## 문제

![스크린샷 2020-03-22 오후 11 04 16](https://user-images.githubusercontent.com/43809168/77251474-e10ced80-6c91-11ea-9212-b9eaeca772c8.png)

```java
import java.util.Stack;

class Solution {
    public boolean validate(String v){
        Stack<Character> stack = new Stack<>();
        for(int i=0; i<v.length(); i++){
            if(v.charAt(i) == ')' && stack.isEmpty()){
                return false;
            }
            else if(v.charAt(i) == ')'){
                stack.pop();
            }
            else{
                stack.push('(');
            }
        }

        return true;
    }

    public int division(String w){
        int open = 0,close = 0;
        for(int i=0; i<w.length(); i++){
            if(w.charAt(i) == '('){
                open++;
            }else{
                close++;
            }
            if(open == close){
                return i+1;
            }
        }
        return w.length()-1;
    }

    public String make(String p){
        if(p.isEmpty()){
            return "";
        }
        int division = division(p);
        String u = p.substring(0,division);
        String v = p.substring(division);

        if(validate(u)){
            return u + make(v);
        }else{
            String temp = '(' + make(v) + ')';
            u = u.substring(1,u.length()-1);
            u = reverse(u);
            return temp+u;
        }
    }

    public String reverse(String u) {
        StringBuilder temp = new StringBuilder();

        for(int i=0; i<u.length(); i++){
            if(u.charAt(i) == '('){
                temp.append(')');
            }else{
                temp.append('(');
            }
        }
        return temp.toString();
    }

    public String solution(String p) {
        String answer = "";
        if(p.isEmpty()){
            return  "";
        }
        if(validate(p)){
            return p;
        }

        answer = make(p);



        return answer;
    }
}
```

문제의 요구사항대로 **정확히** 구현하기만 하면 되는 문제.

재귀 함수를 사용하라는 부분이나, 문제의 요구사항들을 명확히 알려주고 있기 때문에 쉽다고 생각했으나

reverse를 StringBuilder로 사용하다가 안되서 살짝 당황한 문제. (문제를 정확히 읽자)

순서를 뒤집는게 아니라 괄호를 뒤집는거였는데 **뒤집는**다는 말의 의미를 잘못 이해해서 실수하고 말았다.

그 외에는 특별히 어려운 점은 없었다.
