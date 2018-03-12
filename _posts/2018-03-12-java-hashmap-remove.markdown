---
layout: post
title:  "HASHMAP REMOVE 주의사항1"
date:   2018-03-12 11:55:00 +0900
categories: java
---

# JAVA HASH MAP REMOVE

{% highlight java linenos %}
public class KeyTest {
    public static void removeKey(Map<String, String> map) {
		List<String> keyList = new ArrayList<String>();
		for (String key : map.keySet()) {
		   if (key.contains("1")) {
			   map.remove(key);
		   }
		}
	}

    public static void main(String[] args) {
		Map<String, String> temp = new HashMap<>();
		
		temp.put("1", "1");
		temp.put("2", "2");
		temp.put("3", "3");
		temp.put("4", "4");
		
		removeKey(temp);
		
		for (String key : temp.keySet()) {
            System.out.println( String.format("키 : %s, 값 : %s", key, temp.get(key)) );
        }
		
		
	}
}
{% endhighlight %}


- 실행결과  
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.HashMap$HashIterator.nextEntry(HashMap.java:922)
	at java.util.HashMap$KeyIterator.next(HashMap.java:956)
	at com.gigaeyes.vms.vod.KeyTest.removeKey(KeyTest.java:33)
	at com.gigaeyes.vms.vod.KeyTest.main(KeyTest.java:63)

- 발생 원인  
    Java Collection classes for 문을 돌때 내부에서 Itr클래스에 hasNext메소드를 호출 하면서 loop를 순회 할때 컬렉션의 사이즈가 수정 되면 
    ConcurrentModificationException을 발생시키게 된다.



