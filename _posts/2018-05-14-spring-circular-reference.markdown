---
layout: post
title:  "java - spring circular reference 순환참조"
date:   2018-05-14 22:40:00 +0900
categories: java
---

# spring circular reference 순환참조

---------  

spring 에서 

A Service-> B Service -> A Service

서비스 객체에서 위와 같이 참조 하고 A 서비스에서 async 메소드를 추가 시 컴파일 시점에 아래와 같이 순환 참조 오류가 발생했다.

# 에러 로그

`Error creating bean with name 'a': Bean with name 'a' has been injected into other beans [b] in its raw version as part of a circular reference, but has eventually been wrapped. This means that said other beans do not use the final version of the bean. This is often the result of over-eager type matching - consider using 'getBeanNamesOfType' with the 'allowEagerInit' flag turned off, for example.`


# A Service
{% highlight java %}
@Service
public class A {

	@Autowired
	private B b;
	
	
    
	@Async("threadPoolTaskExecutor")
	public Future<String> jobA() throws InterruptedException {
		System.out.println("job A");
		job();
		return new AsyncResult<String>("Success");
	}
	
	public void job() throws InterruptedException{
	      Thread.sleep(3000);
	      System.out.println("job async");
	}
}

{% endhighlight %}

# B Service
{% highlight java %}

@Service
public class B {
	@Autowired
	private A a;
    
	public String jobB() {
		return "job B";
	}
}

{% endhighlight %}

# Application.java
{% highlight java %}

@EnableAsync
@SpringBootApplication
public class CircularApplication {

	public static void main(String[] args) {
		SpringApplication.run(CircularApplication.class, args);
	}
	
	@Bean(name = "threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(3);
        taskExecutor.setMaxPoolSize(30);
        taskExecutor.setQueueCapacity(10);
        taskExecutor.setThreadNamePrefix("Executor-");
        taskExecutor.initialize();
        return taskExecutor;
    }
}

{% endhighlight %}

# Test Code
{% highlight java %}
package com.dependency.circular;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import com.dependency.circular.service.A;

@RunWith(SpringRunner.class)
@SpringBootTest
public class CircularApplicationTests {
		
    @Autowired
    A a;
    
    @Test
    public void givenCircularDependency_whenSetterInjection_thenItWorks() throws InterruptedException, ExecutionException {
    	Future<String> future = a.jobA();
    	while(!future.isDone()) {
    	    System.out.println("wait...");
    	    Thread.sleep(500);
    	}
    	 
    	String result = future.get();
    	Assert.assertEquals("Success", result);
    }

}

{% endhighlight %}

spring io 에서 데모를 만든 후 테스트 코드를 실행 하면 BeanCurrentlyInCreationException 발생한다.

## 에러 로그
org.springframework.beans.factory.**BeanCurrentlyInCreationException**: Error creating bean with name 'a': Bean with name 'a' has been injected into other beans [b] in its raw version as part of a circular reference, but has eventually been wrapped. This means that said other beans do not use the final version of the bean. This is often the result of over-eager type matching - consider using 'getBeanNamesOfType' with the 'allowEagerInit' flag turned off, for example.


## 해결방법
	1. 종속적인 class를 lazy 로딩
	2. setter method autowired 사용
	3. @PostConstruct 이용해서 종속성 관계에 있는 클래스에 객체 주입
	4. 스프링 context 이용하여 수동 injection
	5. 디자인을 수정 한다. injectble interface를 통해서 injection

## 1번 해결 방법 (@Lazy anotation 추가)
{% highlight java %}
@Service
public class A {
	@Lazy 
	@Autowired
	private B b;
	
	
    
	@Async("threadPoolTaskExecutor")
	public Future<String> jobA() throws InterruptedException {
		System.out.println("job A");
		job();
		return new AsyncResult<String>("Success");
	}
	
	public void job() throws InterruptedException{
	      Thread.sleep(3000);
	      System.out.println("job async");
	}
}
{% endhighlight %}

## 참조 링크
[참조 1 ~ 4](http://www.baeldung.com/circular-dependencies-in-spring)

[참조 5](http://gal-levinsky.blogspot.kr/2012/04/judgement-day-weapon-for-circular.html)

[같은현상](https://stackoverflow.com/questions/36426712/why-async-annotation-causes-circular-reference-issue)