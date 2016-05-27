# 關於本文章

這是來自網路上的blog的文章的簡單筆記，主要說明elasticsearch與django的整合使用的一個例子，以便個人複習使用。

主要有四個部分
1. 建立django的骨架，特別是models.py內的內容。
2. 建指令，生成資料
3. 建指令，映射elasticsearch，將資料送入elasticsearch
4. 完善django功能，urls->views->template

## 建立django的骨架，並加上models.py
目前伺服器好像掛掉了。^^

## 建立指令，以生成資料

Plan

    1. Create a command.
    2. Populate Universities.
    3. Populate Students.
    4. Populate Courses.
    5. Make a command reusable.


First, let's create a command via `django-extensions`

```
python project/manage.py create_command core
```
這將會在core資料夾下生成一個management資料夾，內有一個sample.py作為例子，把它執行看看。
```sh
python project/manage.py sample
```

```sh
File "/home/lee/mysb/el_django/project/apps/core/management/commands/sample.py", line 8, in handle
    raise NotImplementedError()
NotImplementedError
```
什麼～噴錯，進到sample.py看一下，才發現噴錯正常的，在def handle下讓它印點東西，再執行一次看看。

```python
 def handle(self, *args, **options):
     print('這是sample.py')
        #raise NotImplementedError()
```
你應該可以看到一些你的東西被印出來。把sample.py改名為dummy-data.py, 我們要用它來產生一些資料。

安裝model_mommy `pip install model_mommy`，我們要使用這個套件來把資料加到資料庫中。
把university加到資料庫的程式碼如下：
```python
from model_mommy import mommy
from django.core.management.base import BaseCommand
from core.models import University, Course, Student

class Command(BaseCommand):
    help = "My shiny new management command."

    def handle(self, *args, **options):
        print('這是sample.py')
        
        self.make_universities()
        #raise NotImplementedError()
    
    # 產生大學的資料
    def make_universities(self):
        university_names = (
            'MIT','MGU','CalTech','KPI','DPI','PSTU'
        )
        self.universities=[]
        
        for name in university_names:
            uni = mommy.make(University, name=name) # 這會將大學的名字塞到university的資料庫內。
            #由於名字的屬性是唯一的，因此再執行就會報錯。 
            #print(uni)
            #University model只有一個name欄位(字數255, unique)
            self.universities.append(uni) #把uni塞到universities
```
執行`python project/manage.py dummy-data`
```sh
System check identified some issues:

WARNINGS:
core.Student.courses: (fields.W340) null has no effect on ManyToManyField.
這是sample.py
(0.001) BEGIN; args=None
(0.001) INSERT INTO "core_university" ("name") VALUES ('MIT'); args=['MIT']
(0.000) BEGIN; args=None
(0.000) INSERT INTO "core_university" ("name") VALUES ('MGU'); args=['MGU']
(0.000) BEGIN; args=None
(0.000) INSERT INTO "core_university" ("name") VALUES ('CalTech'); args=['CalTech']
(0.000) BEGIN; args=None
(0.000) INSERT INTO "core_university" ("name") VALUES ('KPI'); args=['KPI']
(0.000) BEGIN; args=None
(0.000) INSERT INTO "core_university" ("name") VALUES ('DPI'); args=['DPI']
(0.000) BEGIN; args=None
(0.000) INSERT INTO "core_university" ("name") VALUES ('PSTU'); args=['PSTU']
```
可以看 handle函式被執行。

然後把製作courses的程式碼加進來。(自行查看程式碼)
再來是製作students的程式碼，這比較麻煩，得先安裝names(pip install names),然後也得動用到random來分配學校，最後還得將學生與課程連結在一起。在這裡要把xrange 改為range，不然會報錯。學生是使用mommy.perpare來製作資料，最後才用Student.objects.bulk_create()來一次製作。我們還得加入一個command-line option以指定要製作幾個學生。(請看add_arguments方法。)

在最一步時，我們需連結學生與課程, ForeignKeys的一個值並無法被知道，且我們無法插入一個列到一個表中當


