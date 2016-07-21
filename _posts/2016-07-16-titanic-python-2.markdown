---
layout: post
title:  "จะรอดไหมบนเรือไททานิค Part2"
date:   2016-07-16 20:14:00 +0700
categories: data
image: "http://blog.socialcops.com/wp-content/uploads/2016/06/HLWczNjzm4rW9HNZdPHJ4yrH-1920x1080.jpeg"
---

มาต่อกันครับ บทความนี้เราจะทำการนำข้อมูลจาก Part 1 มาทำการสร้าง Model เพื่อทำนายข้อมูลกันครับ

# ทำไมต้อง Machine Learning
ปกติแล้วเราสามารถดูข้อมูลจากการวิเคราะห์แบบ Part 1 เพื่อดูว่าผู้ชายมีโอกาศรอดกี่เปอร์เซนต์ อายุ 13 ปี มีโอกาศรอดกี่เปอร์เซ็นต์ โดยการจัดกลุ่มข้อมูลแล้วค่อย ๆ เจาะลึกลงไปอีก แต่ว่าถ้าหากว่าเราต้องการเขียนโปรแกรมเพื่อทำนายว่า ผู้โดยสารคนนี้ที่มีลักษณะแบบนี้เค้าจะรอดไหมถ้าอยู่บนเรือไททานิค เราก็จะต้องเขียนโปรแกรมที่เป็นแบบ Rule-based (if-else) จำนวนมาก ๆ แล้วก็ตัวแปร weight เพื่อให้คะแนนความสำคัญของแต่ละค่าที่แตกต่างกัน เช่น เพศ ส่งผลมากกว่า อายุ เป็นต้น ถ้าหากว่าเราต้องเขียนข้อมูลแบบนี้ ยิ่งข้อมูลเยอะ ยิ่งอ้วก ตายแน่ ๆ อีกอย่างมันแก้ไขยากเพราะทุกอย่างมันจะซ้อนกันไปเรื่อย ๆ ต้องปรับนู่นแก้นี่มากมาย เมื่อต้องแก้ตัวแปรใดตัวแปรนึง

และนี่แหละครับคือเหตุผลที่เราต้องใช้ Machine Learning

## เตรียมข้อมูล
อันดำแรกเราต้องเตรียมข้อมูลก่อนครับ ดูว่ามีข้อมูลอะไร เท่าไหร่ ข้อมูลไหนเป็นประเภทอะไร และขาดข้อมูลไหนบ้าง

{% highlight python %}
titanic_df.count()
{% endhighlight %}

จะเห็นว่าข้อมูลหายไปเยอะมากในคอลัมน์ `boat, cabin, body` เราก็จะตัดมันทิ้งไปครับ

{% highlight python %}
titanic_df = titadic_df.drop(['boat', 'cabin', 'boat']), axis=1)
{% endhighlight %}

และในอีกคอลัมน์นึงชื่อ `home.dest` ที่ข้อมูลหายไปจำนวนนึง เราก็จะใส่ข้อมูลส่วนที่ไม่มีด้วยคำว่า `NA` ครับ

{% highlight python %}
titanic_df["home.dest"] = titanic_df["home.dest"].fillna("NA")
{% endhighlight %}

หลังจากนั้นเราก็ลบข้อมูลที่เป็นค่าว่าง หรือ ไม่มีข้อมูลออกไปแล้วลองนับใหม่อีกรอบครับ

{% highlight python %}
titanic_df = titanic_df.dropna()
titanic_df.count()
{% endhighlight %}

ลำดับต่อมาเราก็จะเอาข้อมูลมาแปลงให้อยู่ในรูปที่ Machine Learning จะสามารถนำไปเรียนรู้ได้ครับ

{% highlight python %}
# สร้างฟังค์ชัน
def preprocess_titanic_df(df):
    processed_df = df.copy()
    le = preprocessing.LabelEncoder()
    processed_df.sex = le.fit_transform(processed_df.sex)
    processed_df.embarked = le.fit_transform(processed_df.embarked)
    processed_df = processed_df.drop(['name','ticket','home.dest'],axis=1)
    return processed_df

# เรียกใช้ฟังค์ชัน
processed_df = preprocess_titanic_df(titanic_df)
{% endhighlight %}

ฟังค์ชันที่สร้างขึ้นนี้จะทำการแปลงข้อมูลที่เป็นตัวอักษรให้กลายเป็นตัวเลขแบบจัดกลุ่มครับ อย่างเช่น เพศ มีข้อมูลเป็น ชาย, หญิง เราก็จะแปลงมันเป็น คอลัมน์ ชาย แล้วก็บอกว่า เป็น ผู้ชายหรือไม่เป็นผู้ชาย, คอลัมน์ หญิง เพื่อบอกว่าเป็นผู้หญิงหรือไม่ โดยใช้เลข 1,0 นั่นเอง

ต่อมาเราก็แยกคอลัมน์เป้าหมาย หรือ ผลลัพธ์ ออกจากข้อมูล input ครับ

{% highlight python %}
X = processed_df.drop(['survived'], axis=1).values
y = processed_df['survived'].values
{% endhighlight %}

หลังจากนั้นเราจะทำการแบ่งข้อมูลออกเป็น 4 เซ็ท คือข้อมูลสำหรับการเทรนด์ X, y และสำหรับการทดสอบ X,y โดยตั้งขนาดของข้อมูลทดสอบไว้ที่ 20% ของข้อมูลทั้งหมด

{% highlight python %}
X_train, X_test, y_train, y_test = cross_validation.train_test_split(X,y,test_size=0.2)
{% endhighlight %}


# ถึงเวลาสนุกแล้วสิ

หลังจากที่ผ่านความเหน็ดเหนื่อยมามากมาย ถึงเวลาละเลงเล่นข้อมูลกันละครับ เราจะเริ่มจากการทำ Decision tree กันก่อนนะครับ ลองดูคำอธิบายเรื่อง Decision tree ได้ที่ [r2d3](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/)

และนี่คือ Decision tree หลังจากที่เราทำการเทรนด์ข้อมูลไปแล้ว โดยแสดงแค่ 3 ระดับครับ
![Deicision tree](http://blog.socialcops.com/wp-content/uploads/2016/06/image00.png)

จะเห็นว่ามันจะถูกแบ่งด้วย เพศ ก่อน แล้วค่อย คลาส แล้วก็แบ่งลงไปเรื่อย ๆ จนเห็นผลลัพธ์ โดยที่สีน้ำเงินคือประเภทของผู้โดยสารที่จะมีโอกาสรอดมากกว่า, สีส้มก็มีโอกาสรอดน้อยกว่าครับ ซึ่งน่าสนใจมากครับเพราะจะเห็นว่า ผู้หญิง มักจะมีตัวตัดสินว่าจะรอดหรือไม่รอดด้วยราคาตั๋ว (fare) ในขณะเดียวกัน ผู้ชายกับถูกตัดสินว่าจะรอดหรือไม่ด้วยอายุ (age)

## จะสร้าง tree นี้ได้อย่างไร
เริ่มจากทำการเทรนด์โมเดลกันก่อน

{% highlight python %}
clf_dt = tree.DecisionTreeClassifier(max_depth=10)

clf_dt.fit (X_train, y_train)
clf_dt.score (X_test, y_test)

>>> 0.78947368421052633
{% endhighlight %}

จะเห็นว่าผลลัพธ์ที่ได้คือ 0.7703 หรือประมาณ​ 77% ที่โมเดลนี้จะสามารถทำนายได้ถูกต้อง ไม่แย่เลยนะครับสำหรับโมเดลแรก

ถ้าหากว่าคุณสงสัย และไม่เชื่อว่า เห้ยจริงหรอ มันจะได้ตั้ง 77% เลยจริงๆดิ งั้นเรามาลองทำการทดสอบกันดูอีกครั้ง

{% highlight python %}
shuffle_validator = cross_validation.ShuffleSplit(len(X), n_iter=20, test_size=0.2, random_state=0)

def test_classifier(clf):
    scores = cross_validation.cross_val_score(clf, X, y, cv=shuffle_validator)
    print("Accuracy: %0.4f (+/- %0.2f)" % (scores.mean(), scores.std()))

test_classifier(clf_dt)
>>> Accuracy: 0.7742 (+/- 0.02)
{% endhighlight %}

จาก `code` เราทำการสลับสับเปลี่ยนข้อมูล 20 รอบ แล้วก็ทดสอบวัดคะแนนดูใหม่ ก็จะเห็นว่า ผลลัพธ์มันคือ 0.7742 หรือประมาณ 77% เช่นเดียวกัน

จากที่เห็นจะพบว่า เมื่อใช้ Deicision tree จะได้ความแม่นยำอยู่ที่ 77% แต่มันจะมากกว่านี้ได้อีก ถ้าหากเราใช้ Algorithm ตัวอื่นครับ

{% highlight python %}
clf_rf = ske.RandomForestClassifier(n_estimators=50)
test_classifier(clf_rf)
>>> Accuracy: 0.7837 (+/- 0.02)

clf_gb = ske.GradientBoostingClassifier(n_estimators=50)
test_classifier(clf_gb)
>>> Accuracy: 0.8201 (+/- 0.02)

eclf = ske.VotingClassifier([('dt', clf_dt), ('rf', clf_rf), ('gb', clf_gb)])
test_classifier(eclf)
>>> Accuracy: 0.8036 (+/- 0.02)
{% endhighlight %}

หลังจากที่เราได้โมเดลแล้ว เราสามารถที่จะนำโมเดลแต่ละตัวมาทำการทำนายได้ว่า ใครจะรอดหรือไหม ดังนี้ครับ

{% highlight python %}
passengers_set_1 = titanic_df[titanic_df.pclass == 1].iloc[:20,:].copy()
passengers_set_2 = titanic_df[titanic_df.pclass == 2].iloc[:20,:].copy()
passengers_set_3 = titanic_df[titanic_df.pclass == 3].iloc[:20,:].copy()
passenger_set = pd.concat([passengers_set_1,passengers_set_2,passengers_set_3])

prediction = eclf.predict(X_test)
passenger_set[passenger_set.survived != prediction]
{% endhighlight %}

จากตัวอย่าง เราจะใช้ VotingClassifier ในการทดสอบนะครับ จะเห็นได้ว่า ข้อมูลของคนบางคนทีี่ถูกทำนายออกมาว่ารอด อาจจะไม่ได้รอดก็ได้นะครับ เนื่องจากความแม่นยำของการทำนายยังมีความผิดพลาดอยู่นั่นเองครับ

## ต่อไป
ข้อมูลต่อออนไลน์ที่น่าสนใจที่จะนำมาเล่นคือนี่เลยครับ [Game Of Thrones](https://www.kaggle.com/mylesoneill/game-of-thrones) ใครเล่นแล้วได้อะไรมาแบ่งปันข้อมูลกันบ้างนะครับ :D

ใครสนใจหรือชอบ อยากจะแบ่งปันเพิ่มเติม เข้ามาคุยกันได้ที่ [Lukkiddd](http://www.facebook.com/lukkiddd) ขอบคุณครับ

## References
- [Socialcops](http://blog.socialcops.com/engineering/machine-learning-python)
- [r2d3](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/)
