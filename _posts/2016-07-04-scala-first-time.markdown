---
layout: post
title:  "Scala First Time"
date:   2016-07-04 09:35:00 +0700
categories: dev
---
ภาษา Scala ย่อมาจาก Scalable Language

นั่นแปลว่ามันถูกสร้างขึ้นมาให้รองรับการทำงานกับระบบขนาดใหญ่ มันทำให้รู้สึกเหมือนเป็น Script Language ในบางครั้งเวลาที่ต้องการ Feedback แบบเร็วๆ และก็ให้ความรู้สึกเหมือนเป็นภาษาที่จริงจังเมื่อเราต้องการสร้างระบบที่ทรงพลัง และวันนี้ ผมจำเป็นต้องเรียนรู้ Scala เพื่อทำ Project สำหรับตอนฝึกงาน ดังนั้น เรามาเรียนรู้ไปพร้อม ๆ กันดีกว่าครับ

# ก่อนจะเริ่ม #
Scala เป็น Object-Oriented และ Functional ในขณะเดียวกัน ทำให้คนที่มีพื้นฐาน Java ที่ไม่ใช่เรา สามารถเรียนรู้มันได้อย่างง่ายได้

## Classes ##
ในภาษา Scala เราจะนิยาม class ในรูปแบบนี้ครับ

{% highlight scala %}
class Point(xc: Int, yc: Int) {
  var x: Int = xc
  var y: Int = yc

  def move(dx: Int, dy: Int) {
    x = x + dx
    y = y + dy
  }
  override def toString(): String = "(" + x + ", " + y + ")";
}
{% endhighlight %}

Class ข้างต้นเป็นการสร้าง class ชื่อ Point และรับตัวแปรเข้าไป 2 ตัว คือ xc, yc ที่มีลักษระเป็น integer โดยมี attribute x, y มารับค่า รวมถึงมี `method move(dx, dy)` เพื่อใช้ภายใน object แล้วก็มีการเขียนทับ method เดิม โดยใช้ `override` เพื่อเปลี่ยนแปลงการทำงานของ class แม่

วิธีการนำไปใช้ เช่น
{% highlight scala %}
object Classes {
  def main(args: Array[String]) {
    val pt = new Point(1, 2)
    println(pt)
    pt.move(10, 10)
    println(pt)
  }
}
{% endhighlight %}

หมายเหตุ: ความต่างของ var กับ val คือ val มันจะอัพเดทไม่ได้ เปรียบเสมือนตัวแปร constant หรือ final นั่นเองครับ

เมื่อรัน เราจะได้ผลลัพธ์ดังนี้ครับ

{% highlight scala %}
(1, 2)
(11, 12)
{% endhighlight %}

ใครสนใจหรือชอบ อยากจะแบ่งปันเพิ่มเติม เข้ามาคุยกันได้ที่ [Lukkiddd](http://www.facebook.com/lukkiddd) ขอบคุณครับ
