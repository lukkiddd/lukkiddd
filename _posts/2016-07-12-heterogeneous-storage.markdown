---
layout: post
title:  "Heterogeneous Storage in HDFS"
date:   2016-07-12 14:58:00 +0700
categories: data
---
## Heterogeneous Storage

Heterogeneous Storage คือความสามารถในการแยกประเภทหรือความแตกต่างของ Storage ในระบบได้ โดยแต่ก่อน Hadoop HDFS ไม่สามารถแยกประเภทของ Storage ได้แต่จะมองรวมกันเป็นก้อนเดียว แต่ด้วยความต้องการความเร็วทำให้คนเริ่มหันมาใช้ SSD เพื่อเก็บข้อมูลมากขึ้น รวมถึงราคาของ SSD ค่อย ๆ ถูกลงเรื่อย ๆ
เมื่อมีการนำ SSD มาใช้ทำ Storage แต่ยังมีการใช้ DISK(HDD) อยู่ด้วย แล้ว Hadoop ก็มองมันเป็น Storage ก้อนเดียว จึงทำให้มันดูไม่ค่อยเวิร์คเท่าไหร่ จึงมี Feature ใหม่ (Heterogeneous storage) เกิดขึ้น

### Heterogeneous Storage in HDFS
เป็น Feature ที่ทำให้ผู้ใช้สามารถกำหนด Directory ได้ว่าส่วนไหนจะทำงานบน DISK, SSD, ARCHIVE ซึ่งแต่ละแบบก็จะทำงานแตกต่างกัน รวมถึงมี Concept เรื่อง StoragePolicies เพื่อเข้ามาจัดการกับข้อมูลให้มีประสิทธิภาพมากขึ้น

# Configuration

ตั้งค่าที่ `hdfs-site.xml`
* `dfs.storage.policy.enabled` ตั้งค่าเป็น true (default - true)
* `dfs.datanode.data.dir` ให้ติด Tag ไว้ด้านหน้าว่าเป็นประเภทอะไร
  + `[ARCHIVE]/datanode/data1,[SSD]/datanode/data2`
  + มี Tag ดังนี้
    + ARCHIVE
    + SSD
    + DISK (default)
    + RAM_DISK
หมายเหตุ: ถ้า Tag ที่ตั้งไว้ มี quota ไม่พอ หรือไม่สามารถใช้งานได้ จะถูกตั้งเป็น [DISK] อัตโนมัติ

# Using

## StoragePreferences

ตัว Storage Preference อ้างอิงจาก เอกสาร doc ปี 2014   
1. ตั้งค่าตอนสร้างไฟล์ด้วย `FileSystem#create`
2. ตั้งค่าไฟล์ที่มีอยู่แล้วด้วย `DistributedFileSystem#setStoragePreference`
3. คิวรี่ Storage Preference ด้วย `DistributedFileSystem#getStoragePreference`
4. ดู BlockLocation ดูว่า Block นี้อยู่ tag ไหน ด้วย `FileSystem#getFileBlockLocations`
5. CLI for Storage types
  1. `hdfs dfs -put -storagePref [storagePreferenceString] <localsrc> … <dst>`
  2. `hdfs dfs -cp -storagePref [storagePreferenceString] <localsrc> … <dst>`
  3. `hdfs dos setStoragePreference [storagePreferenceString] <file1> … <fileN>`

หมายเหตุ: คำสั่งนี้ ใน Apache Hadoop Doc ปัจจุบัน + CDH5.7.0 ไม่สามาถใช้งานได้ (Unknown command) จาก Apache Hadoop Doc ถูกเปลี่ยนเป็น `storagepolicy`

## Policies

Hot - ใช้สำหรับ storage และ compute เก็บ replica ทั้งหมดใน DISK
Cold - ใช้สำหรับ storage เก็บ replica ทั้งหมดใน ARCHIVE
Warm - กึ่ง Hot กึ่ง Cold. บางอันบน DISK บางอันบน ARCHIVE
All_SSD - ทั้งหมดบน SSD
One_SSD - อันนึงบน SSD, ที่เหลือบน DISK
Lazy_Persist - เขียนลง RAM_DISK แล้วค่อยๆถูกย้ายไป DISK

### Command

1. `hdfs storagepolicies -listPolicies` - เอาไว้ดูรายละเอียด Policies ทั้งหมด
2. `hdfs storagepolicies -setStoragePolicy -path <path> -policy <policy>` - เอาไว้เซ็ท Policy ให้กับ path ที่ต้องการ
3. `hdfs storagepolicies -getStoragePolicy -path <path>` - เอาไว้ดูค่า Policy ที่เคยเซ็ทไว้ของ path ที่ต้องการ

## Maintaining

ใช้เครื่องมือตัวใหม่ชื่อว่า Mover

Mover จะทำการ scan ไฟล์บน HDFS แล้วดูว่ามันถูกต้องตาม storage policies หรือไม่ ถ้าไม่ถูกมันจะย้ายข้อมูลไปให้ถูกที่ จะทำงานแบบ periodically หรือใช้  
{% highlight shell %}
hdfs mover [-p <files/dirs> | -f <local file name>]
{% endhighlight %}
## Quota Management APIs

มันสามารถ Set quotas ของ แต่ละ storageType ได้ด้วยครับ ว่า storageType นี้ใช้ resource เท่าไหร่ ด้วย   
1. `DistributedFileSystems APIs`
  1. `DistributedFileSystem#setStorageTypeSpaceQuotas`
  2. `DistributedFileSystem#setQuota`
2. `HdfsAdmin APIs`
  1. `HdfsAdmin#setStorageTypeSpaceQuota`
  2. `HdfsAdmin#clearStorageTypeSpaceQuotas`
3. `CLI`

### Set

{% highlight shell %}
hdfs dfsadmin -setStorageTypeSpaceQuota -­quotas storageType1=quotaInBytes1,storageType2=quotaInBytes2 <dirname1> ... <dirnameN>
{% endhighlight %}

### Clear

{% highlight shell %}
hdfs dfsadmin ­-clrStorageTypeSpaceQuota ­types storageType1,storageType2,... <dirname1> ... <dirnameN>
{% endhighlight %}

### Query กับดู Usage
{% highlight shell %}
hdfs dos -count -q <path>
{% endhighlight %}

ใครสนใจหรือชอบ อยากจะแบ่งปันเพิ่มเติม เข้ามาคุยกันได้ที่ [Lukkiddd](http://www.facebook.com/lukkiddd) ขอบคุณครับ

## References
* Arpit Agarwal 2014, http://hortonworks.com/blog/heterogeneous-storages-hdfs
* Agarwal 2014, https://issues.apache.org/jira/browse/HDFS-5682
* Arpit Agarwal 2015, http://hortonworks.com/blog/heterogeneous-storage-policies-hdp-2-2
* Apache Software Foundation 2016, https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/ArchivalStorage.html#Get_Storage_Policy
* Apache Software Foundation 2016, https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/ArchivalStorage.html
