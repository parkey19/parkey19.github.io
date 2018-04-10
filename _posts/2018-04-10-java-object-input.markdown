---
layout: post
title:  "java - compress, decompress serialized object"
date:   2018-04-10 18:35:00 +0900
categories: java
---

# java obejct compress, decompress serialized write file and read file method
---------  


{% highlight java %}
public static void deserialzeKmsCamEncryptMap(String filename) {

  try (FileInputStream fin = new FileInputStream(filename);
      GZIPInputStream gis = new GZIPInputStream(fin);
      ObjectInputStream ois = new ObjectInputStream(gis);) {

    VO vo = (VO) ois.readObject();

  } catch (Exception ex) {
    ex.printStackTrace();
  }

}  

	
public static void serializeKmsCamEncryptMap(VO vo, String fileName) {
		
  try (FileOutputStream fos = new FileOutputStream(fileName);
      GZIPOutputStream gz = new GZIPOutputStream(fos);
      ObjectOutputStream oos = new ObjectOutputStream(gz);) {
    oos.writeObject(vo);
    System.out.println("Done");

  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
{% endhighlight %}



# 참조 링크  
[1.How to compress serialized object into file](https://www.mkyong.com/java/how-to-compress-serialized-object-into-file/)

[2.How to decompress serialized object from a Gzip file](https://www.mkyong.com/java/how-to-decompress-serialized-object-from-a-gzip-file/)