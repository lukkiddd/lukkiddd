---
layout: post
title:  "Single-purpose CSS"
date:   2016-08-19 16:34:00 +0700
categories: css
---

การเขียน css มีหลากหลายวิธี บ้างก็เขียนเป็น `component` บ้างก็เขียนเป็นส่วน ๆ แล้วแต่ความถนัดและความต้องการของงานนั้น ๆ

และวันนี้ผมได้ไปอ่าน `marvel styleguide` ซึ่งผมรู้สึกว่ามันมีประโยชน์มาก ๆ และคิดว่ามันน่าสนใจและนำมาใช้ในการพัฒนา `css` รูปแบบนึงครับ มันคือ `single-purpose css` หรือ css ที่ทำงานเพียงอย่างเดียวเท่านั้น

## ข้อดีของ Single-purpose css
ข้อดีของ Single purpose css คือ
- ใช้งานง่าย
- ขึ้นหน้าเว็บเร็ว
- มีความ consistency สูง

## ข้อเสียของ Single-purpose css
- ขนาดใหญ่
- ต้องคิด core style ให้ดี

## Naming

### Class naming
การตั้งชื่อ `class` จะตั้งชื่อโดยใช้ `camelCase` แปลงจาก `css attributes` มาเป็นชื่อ `class` เลยครับ หรืออาจจะมีการกำหนดค่าลงไปเพื่อบอกความหมายของ `class` นั้นๆ
ยกตัวอย่างเช่น
- `font-size` เป็น `fontSize`
- `background-color` เป็น `backgroundColor` หรือ `bg-ชื่อสี`
- `color` เป็น `c-ชื่อสี`
- `border-width` เป็น `borderWidth-ค่า` เช่น `borderWidth-1` หมายถึงความหนาขอบ `1px`

### Class with state
หาก `class` นั้นมี `state` ที่แตกต่างกันจะใช้ `--stateName` ยกตัวอย่างเช่น
- `bg-black--hover` - หมายถึง ให้ `bg` เป็นสีดำเมื่อเกิด `hover state` เป็นต้น

## Scale
แน่นอนว่าการเขียน single-purpose css เหมาะสำหรับเว็บไซต์ที่ต้องการ styleguide ที่นิ่งระดับนึงแล้ว เนื่องจากเราจะทำการกำหนด ขนาด, ระดับ ของ `css` ไว้เป็นพื้นฐานสำหรับทุก ๆ ส่วน ที่จะนำไปใช้ เช่น ผมกำหนดขนาดไว้ 7 ระดับ
- `-xxs` - Extra extra small (โคตร โคตร เล็ก)
- `-xs` - Extra small (โคตรเล็ก)
- `-s` - Small (เล็ก)
- `-m` - Medium (ปานกลาง)
- `-l` - Large (ใหญ่)
- `-xl` - Extra large (โคตรใหญ่)
- `xxl` - Extra extra large (โคตร โคตร ใหญ่)

ดังนั้นในทุก `component` ที่มีขนาด ผมจะทำการกำหนดขนาดตาม `scale` ที่ผมได้ตั้งไว้ข้างต้น ยกตัวอย่างเช่น `fontSize` ก็จะกลายเป็น
{% highlight css %}
fontSize-xxs: 8 px;
fontSize-xs : 13px;
fontSize-s  : 16px;
fontSize-m  : 22px;
fontSize-l  : 30px;
fontSize-xl : 40px;
fontSize-xxl: 48px;
{% endhighlight %}

## เริ่มสร้าง Class
หลังจากนั้นเราก็ทำการสร้างไฟล์ของ `class` แต่ละ `class` ขึ้นมาเพื่อใช้งานครับ
- `_typography.scss` - `class` จำพวก `fontSize`, `fontFamily`, `textTransform` และ อื่น ๆ
- `_color.scss` - `class` จำพวก `c-black`, `c-success`, `c-danger`
- `_input.scss` - `class` จำพวก `input`, `input-s`

## Components
เมื่อได้ `class` พื้นฐานครบแล้ว ต่อมาคือการสร้าง `component`
วิธีการสร้าง component ภายในจะใช้ `@extend` ซึ่งเป็นหนึ่งในความสามารถของ `scss` ในการสร้าง `class` ดังนี้
{% highlight css %}
.button {
  @extend .c-white;
  @extend .bg-black;
  @extend .padding-m;
  @extend .borderWidth-1;
  @extend .borderStyle-solid;
  @extend .borderColor-black;
  @extend .fontSize-m;
}
{% endhighlight %}

และนี่ก็เป็นตัวอย่างในการเขียน css แบบ single-purpose ที่ผมกำลังทดลองใช้อยู่ ถ้าหากเขียนเสร็จแล้วจะนำมาปล่อย public ให้ลองนำไปใช้กันนะครับ หรือ ถ้าหากใครอยากจะลองศึกษาอ่านเพิ่มเติม ลองดูจาก Marvel Styleguide หรือ .... ได้เลยครับ

ใครสนใจหรือชอบ อยากจะแบ่งปันเพิ่มเติม เข้ามาคุยกันได้ที่ [Lukkiddd](http://www.facebook.com/lukkiddd) ขอบคุณครับ

## References
- []()
