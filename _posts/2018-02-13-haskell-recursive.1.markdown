---
layout: post
title:  "haskell recursive function"
date:   2018-02-13 23:00:00 +0900
categories: haskell
---

## recursive function

`product` 함수를 사용해서 `factorial` 함수를 정의
{% highlight haskell %}
factorial :: Int -> Int
factorial n = product [1..n] 
{% endhighlight %}


`factorial` 함수를 recursive로 구현
{% highlight haskell %}
factorial' :: Int -> Int
factorial' 0 = 1
factorial' (n+1) = (n+1) * factorial' n
{% endhighlight %}

실제 함수 적용
```
factorial 3

= {factorial 적용}  
3 * factorial 2  
= {factorial 적용}  
3 * (2 * factorial 1)  
= {factorial 적용}  
3 * (2 * (1 * factorial 0))  
= {factorial 적용}  
3 * (2 * (1 * 1))  
= {* 적용}  
result 6
```
recursive 에는 무한 호출에 빠지 않도록 하는 베이스 케이스가 있는데
`factorial` 함수에서는 
`factorial' 0 = 1` 이 구문 베이스 케이스가 된다.

> haskell in programing