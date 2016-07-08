---
layout: post
title:  "สร้าง Blog ง่าย ๆ ด้วย Jekyll+Heroku"
date:   2016-07-04 23:29:00 +0700
categories: dev
---
พอดีได้มีโอกาสทำ Blog ใหม่ เนื่องจากอันเก่ามันได้อัตรธานหายไปแล้ว ก็เลยลองค้น ๆ ดูวิธีการทำ blog แบบง่าย ๆ โดยที่ไม่ต้องใช้ WordPress ขอแบบง่ายโคตร ๆ เลยละกัน แล้วก็แว็บไปเจอเจ้าตัวนี้มานั่นก็คือ [Jekyll](https://jekyllrb.com/) ซึ่งมันง่ายมาก ๆ เลย เพียงแค่เราเขียนข้อความในรูปแบบของ Markdown([kramdown](http://kramdown.gettalong.org/)) แล้วก็สั่งรันให้มัน Generate ไฟล์ html มาให้ เท่านี้เว็บไซต์เราก็พร้อมใช้งานทันที เห้ยแม่เจ้า ต้องลองเล่นแล้ว ดังนั้นเรามาลองกันครับผม

# เตรียมพร้อม #
ก่อนอื่นเลยเราต้องเตรียมอุปกรณ์ให้พร้อมก่อนครับ เริ่มจาก ติดตั้งเจ้า jekyll ให้เรียบร้อย
{% highlight bash %}
$ gem install jekyll
$ jekyll new nice-project
#=> เริ่มกันได้เลยย
{% endhighlight %}

## เริ่มเขียนทันที ##
หลังจากที่ได้**โปรเจ็คเว็บไซต์**เรามาแล้วก็เข้าไปละเลงเขียนเนื้อหากันได้เลยที่ `./_posts` เรียบร้อยเป็นอันเสร็จสิ้น ส่วนรายละเอียดอื่น ๆ เพิ่มเติมคงต้องหาอ่านเพิ่มกันเอาที่ [Jekyll](https://jekyllrb.com/) หรือติดต่อสอบถามกันเข้ามาได้ครับ ถ้ารู้ก็จะช่วยตอบน้า

# สู่สายตาประชาชน #
หลังจากที่ได้เว็บไซต์แล้ว เขียน post ไปแล้ว ก็ถึงเวลาเอามันออกสู่สายตาประชาชนครับ เริ่มจากไปสมัครสมาชิกกับบริการ [heroku](https://www.heroku.com/) ก่อนเลย

## ตีพิมพ์ ##
แล้วก็ทำการดาวน์โหลด heroku toolbelt มาครับ สำหรับ Mac ก็ใช้ Homebrew โหลดเลยครับ
{% highlight bash %}
$ brew install heroku-toolbelt
{% endhighlight %}
แล้วก็ต้องลง bundler เพิ่มด้วย
{% highlight bash %}
$ gem install bundler
{% endhighlight %}

ต่อมาก็เข้าไปที่ โฟลเดอร์ Project เราครับแล้วสร้างไฟล์ชื่อ `Gemfile` ขึ้นมาโดยเขียนข้อความข้างในว่า
{% highlight bash %}
source 'https://rubygems.org'
ruby '2.1.0' # แล้วแต่ version ของคุณนะครับ
gem 'bundler'
gem 'jekyll'
gem 'rack-jekyll'
{% endhighlight %}
แล้วก็เจนตัว bundle ครับ
{% highlight bash %}
$ bundle
{% endhighlight %}

## เพิ่มเติม ##
ต่อมาให้เราปิด ส่วนที่มัน generate ออกมาบน local ให้ไม่ต้องอัพขึ้นไปบน server ครับ ด้วยการสร้างไฟล์ชื่อ `.gitignore` แล้วก็ใส่ข้อความลงไปว่า
{% highlight bash %}
`_site`
{% endhighlight %}
แล้วก็ให้เพิ่มข้อความต่อไปนี้ลงไปใน `_config.yml` ด้วย
{% highlight bash %}
exclude: ['config.ru', 'Gemfile', 'Gemfile.lock', 'vendor']
{% endhighlight %}

## จะปล่อยแล้ว ##
สุดท้ายแล้ว ให้เราสร้างไฟล์ชื่อ `config.ru` วางไว้ที่ root directory ของ blog เรานะครับ ใส่ข้อความว่า
{% highlight ruby %}
require 'rack/jekyll'
run Rack::Jekyll.new
{% endhighlight %}

## สร้าง App บน Heroku ##
{% highlight bash %}
$ heroku create blogName --buildpack https://github.com/andycroll/heroku-buildpack-jekyll.git
{% endhighlight %}

แล้วก็ add, commit, แล้ว push มันขึ้นไป
{% highlight bash %}
$ git add .
$ git commmit -m "publish my blog"
$ git push heroku master
$ heroku open
{% endhighlight %}

เป็นอันเสร็จสิ้น แล้วก็คุณจะมี blog เป็นที่เรียบร้อยแล้ว แบบ blog ของผมนี่ไง เย้~!

ใครสนใจหรือชอบ อยากจะแบ่งปันเพิ่มเติม เข้ามาคุยกันได้ที่ [Lukkiddd](http://www.facebook.com/lukkiddd) ขอบคุณครับ

อ้างอิง:

* [Andy Croll](http://andycroll.com/ruby/serving-a-jekyll-blog-using-heroku/)
+ [Jekyll](https://jekyllrb.com/)
