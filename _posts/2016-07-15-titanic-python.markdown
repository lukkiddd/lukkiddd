---
layout: post
title:  "จะรอดไหมบนเรือไททานิค"
date:   2016-07-15 20:14:00 +0700
categories: data
image: "http://blog.socialcops.com/wp-content/uploads/2016/06/HLWczNjzm4rW9HNZdPHJ4yrH-1920x1080.jpeg"
---

เพื่อน ๆ หลายคนคงเคยได้ดูหนัง หรืออ่านหนังสือนิยายเชิงวิทยาศาสตร์แล้วเจอพวกหุ่นยนต์อัจฉริยะที่สามารถเรียนรู้และเข้าใจสิ่งต่าง ๆ ได้เหมือนมนุษย์ เช่นรู้ว่าอะไรคือรถ อะไรคือบ้าน อ่านหนังสือออก แต่งนิยายได้ ซึ่งเรื่องราวเหล่านี้มันกำลังเกิดขึ้นจริงและเป็นจริงในโลกปัจจุบันแล้วครับ ด้วยสิ่งที่เรียกว่า Machine Learning ซึ่งเราจะมาพูดถึงกันในบทความนี้ครับ

## Machine Learning
การให้คอมพิวเตอร์เรียนรู้บางสิ่งบางอย่าง เกิดขึ้นจากการนำข้อมูลให้คอมพิวเตอร์วิเคราะห์หาความหมาย ความสัมพันธ์ด้วยสมการทางคณิตศาสตร์ที่แตกต่างกัน และสิ่งที่เราจะนำมาพูดถึงในบทความนี้คือ ถ้าคุณอยู่บนเรือไททานิค คุณจะรอดไหม?

# เตรียมเครื่อง
อันดับแรกก่อนที่จะเริ่มกัน อยากให้เพื่อน ๆ ติดตั้ง เครื่องมือเหล่านี้ลงบนเครื่องให้เรียบร้อยครับ

* [Python](https://www.python.org/) (บทความนี้ใช้ version 3.4.2)
* [SciPy, NumPy, Pandas, IPython, matplotlib](https://www.scipy.org/)
* [SciKit-Learn](http://scikit-learn.org/stable/)
* [TensorFlow](https://www.tensorflow.org)

วิธิการลงก็มีหลายแบบครับ ผมแนะนำให้ใช้ `"pip"` Python package manager ซึ่งเป็นตัวจัดการแพคเกจใน `python` ครับ วิธีลงก็ง่ายแสนง่าย แค่ `pip install <package_name>` ก็สามารถติดตั้งได้แล้วครับ [ดาวน์โหลดที่นี่](https://pip.pypa.io/en/stable/)

ต่อมาเวลาเราเขียน `code` เราต้องใช้พื้นที่ที่สามารถรันตอบผลลัพธ์ออกมาให้เราดูง่าย ๆ สวย ๆ ครับ ซึ่งตัวที่เป็นที่นิยมอย่างมากก็คือ IPython ที่สามารถแสดงผลได้เลยเป็นกราฟ เป็นสี นอกจากนั้นยัง save เก็บไว้อ่าน ๆ คราวหลังได้อีกด้วย

และสิ่งที่ขาดไม่ได้เลยนั่นก็คือข้อมูลใน titanic ครับ ต้องใช้ข้อมูลมาให้คอมพิวเตอร์เรียนรู้ ดังนั้น [Download](http://biostat.mc.vanderbilt.edu/wiki/pub/Main/DataSets/titanic3.xls)

หลังจากที่ติดตั้งทั้งหมดเสร็จเรียบร้อยแล้วก็เปิด IPython notebook ขึ้นมาครับ อย่างถ้าใช้ใน linux ก็แค่พิมพ์ว่า `juputer notebook` บน command line แล้วก็ไปสร้าง notebook ไว้ที่ที่เดียวกับที่ข้อมูล `titanic3.xls` อยู่ครับ

# ใครรอดและทำไมถึงรอด
เริ่มแรก เริ่มจากการนำ lib ที่ต้องใช้ใส่เข้ามาก่อนครับ
{% highlight python %}
import matplotlib.pyplot as plt
%matplotlib inline
import random
import numpy as np
import pandas as pd
from sklearn import datasets, svm, cross_validation, tree, preprocessing, metrics
import sklearn.ensemble as ske
{% endhighlight %}

หลังจากนั้นก็อ่านข้อมูลจาก xls มาใส่ไว้ใน Pandas dataframe ครับ โดยเราสามารถแสดงข้อมูลบางแถวมาดูก่อนได้ด้วยคำสั่ง `head()`

{% highlight python %}
titanic_df = pd.read_excel('titanic3.xls', 'titanic3', index_col=None, na_values=['NA'])

titanic_df.head()
{% endhighlight %}

จากข้อมูลที่เห็น แต่ละคอลัมน์ก็จะมีความหมายดังนี้ครับ

* survival: Survival (0 = no; 1 = yes)
* class: Class ของ ผู้โดยสาร (1 = first, 2 = second, 3 = third)
* name: ชื่อ
* sex: เพศ
* age: อายุ
* sibsp: จำนวนพี่น้องหรือสามีภรรยา
* parch: จำนวนของพ่อแม่หรือลูกหลาน
* ticket: หมายเลขตั๋ว
* fare: ค่าโดยสาร
* cabin: กลุ่ม
* embarked: จุดลงเรือ (C = Cherbourg, Q = Queenstown, S = Southampton)

หลังจากที่เราได้ข้อมูลมาแล้ว เรามาเริ่มวิเคราะห์ข้อมูลกันซักเล็กน้อยก่อนดีกว่าครับ ด้วยประสิทธิภาพของ Pandas function ที่ให้คุณพิมพ์แค่ 1 บรรทัด!! งั้นเรามาดูกันดีกว่าครับว่า โอกาสรอดจากข้อมูลที่มีนี้มีเฉลี่ยแล้วอยู่ที่เท่าไหร่

{% highlight python %}
titanic_df['survived'].mean()
==> 0.3819709702062643
{% endhighlight %}

จากการคำนวนพบว่ามีแค่ 38% ของผู้โดยสารเท่านั้นที่รอด เรามาดูรายละเอียดลงไปอีกหน่อยดีกว่าครับว่าถ้าแยกตาม class ของผู้โดยสารแล้วจะเป็นยังไง

{% highlight python %}
titanic_df.groupby('pclass').mean()
==> pclass  survived    age         fare      ...
==> 1       0.619195    39.159918   87.508992 ...
==> 2       0.429603    29.506705   21.179196 ...
==> 3       0.255289    24.816367   13.302889 ...
{% endhighlight %}

ตอนนี้เราเริ่มเห็นความน่าสนใจของมันเพิ่มขึ้นมาละครับจะเห็นว่าคนที่อยู่ใน first class มีโอกาสรอดมากถึง 62% เทียบกับคนที่อยู่ใน 3rd class ที่มีแค่ 25.5% เท่านั้น นอกจากนี้ เรายังเห็นอีกว่า ตั๋วสำหรับ first class จะเป็นคนที่มีอายุเยอะมากกว่า 2nd, 3rd รวมถึง ราคาของตั๋วก็แพงกว่าอีกด้วย

จริง ๆ แล้วเราสามารถใช้คำสั่ง `groupby` เพื่อจัดกลุ่มของข้อมูล สำหรับดูข้อมูลลึกลงไปอีก อย่างเช่นอาจจะแบ่ง ตามเพศ ตามคำสั่งนี้ครับ
{% highlight python %}
class_sec_grouping = titanic_df.groupby(['pclass', 'sex']).mean()
{% endhighlight %}

ในขณะที่เรือจม เจ้าหน้าที่จำเป็นต้องจัดลำดับผู้คนที่สามารถลงเรือกู้ชีพ ได้โดยจะให้ผู้หญิงและเด็กลงก่อนอยู่แล้ว แล้วข้อมูลที่เรากำลังวิเคราะห์อยู่ก็ทำให้เห็นว่ามันเป็นแบบนั้นจริง ๆ เนื่องจากคนที่รอดมักจะเป็นคนที่มีเพศหญิง และ มีอายุน้อย นั่นเอง ถ้าไม่เชื่อ เรามาลองจัดกลุ่มข้อมูลของคนที่รอดแบ่งตามอายุ แล้ววาดกราฟดูครับ

{% highlight python %}
group_by_age = pd.cut(titanic_df["age"], np.arange(0, 90, 10))
age_grouping = titanic_df.groupby(group_by_age).mean()
age_grouping['survived'].plot.bar()
{% endhighlight %}

เห็นไหมครับว่า ส่วนใหญ่แล้วเป็นเด็กทั้งนั้นเลยที่รอด บร๊ะ!!

ตอนต่อไปเราจะนำข้อมูลที่ได้ มาทำ Machine Learning เพื่อจัดกลุ่มและทำนายข้อมูลกันครับ

ใครสนใจหรือชอบ อยากจะแบ่งปันเพิ่มเติม เข้ามาคุยกันได้ที่ [Lukkiddd](http://www.facebook.com/lukkiddd) ขอบคุณครับ

## References
- [Socialcops](http://blog.socialcops.com/engineering/machine-learning-python)
