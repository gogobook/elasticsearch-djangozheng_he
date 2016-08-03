#model mommy 的使用中文教學

## 安裝

`pip install model_mommy`

## 基礎用法
*這裡的說明來自model mommy的說明文件

假設你有一個叫family 的app，其內有個長得像這樣的model

```python

class Kid(models.Model):
    """
    Model class Kid of family app
    """
    happy = models.BooleanField()
    name = models.CharField(max_length=30)
    age = models.IntegerField()
    bio = models.TextField()
    wanted_games_qtd = models.BigIntegerField()
    birthday = models.DateField()
    appointment = models.DateTimeField()
```
要建一個持久的實例只要呼叫Mommy

```python
# -*- coding:utf-8 -*-

#Core Django imports
from django.test import TestCase

#Third-party app imports
from model_mommy import mommy
from model_mommy.recipe import Recipe, foreign_key

# Relative imports of the 'app-name' package
from .models import Kid

class KidTestModel(TestCase):
    """
    Class to test the model
    Kid
    """

    def setUp(self):
        """
        Set up all the tests
        """
        self.kid = mommy.make(Kid)
```
不需每次傳遞屬性
每一次都import model也是很無聊，所以讓mommy來幫你做

```python
from model_mommy import mommy

# 1st form: app_label.model_name
kid = mommy.make('family.Kid')

# 2nd form: model_name
dog = mommy.make('Dog')
```

## Model Relationships
mommy也幫忙處理關係，例如"小孩有一隻狗"。

```python
class Kid(models.Model):
    """
    Model class Kid of family app
    """
    happy = models.BooleanField()
    name = models.CharField(max_length=30)
    age = models.IntegerField()
    bio = models.TextField()
    wanted_games_qtd = models.BigIntegerField()
    birthday = models.DateField()
    appointment = models.DateTimeField()

    class Meta:
        verbose_name = _(u'Kid')
        verbose_name_plural = _(u'Kids')

    def __unicode__(self):
        """
        Return the name of kid
        """
        return u'%s' % (self.name)

class Dog(models.Model):
    """
    Model class Dog of family app
    """
    owner = models.ForeignKey('Kid')
```
當你跟Mommy要求:

```python
# -*- coding:utf-8 -*-

#Core Django imports
from django.test import TestCase

#Third-party app imports
from model_mommy import mommy
from model_mommy.recipe import Recipe, foreign_key

# Relative imports of the 'app-name' package

class DogTestModel(TestCase):
    """
    Class to test the model
    Dog
    """

    def setUp(self):
        """
        Set up all the tests
        """
        self.rex = mommy.make('family.Dog')
```
Mommy不只會把狗做出來，也會把kid做出來。

## M2M Relationships
如果是多對多關係

```python
# -*- coding:utf-8 -*-

#Core Django imports
from django.test import TestCase

#Third-party app imports
from model_mommy import mommy
from model_mommy.recipe import Recipe, foreign_key

# Relative imports of the 'app-name' package

class DogTestModel(TestCase):
    """
    Class to test the model
    Dog
    """

    def setUp(self):
        """
        Set up all the tests
        """
        self.rex = mommy.make('family.Dog', make_m2m=True)
```
## Defining some attributes

當然如果你要為某些屬性設定明確的值也是可以的。

```python
# -*- coding:utf-8 -*-

#Core Django imports
from django.test import TestCase

#Third-party app imports
from model_mommy import mommy
from model_mommy.recipe import Recipe, foreign_key

# Relative imports of the 'app-name' package
from .models import Kid

class KidTestModel(TestCase):
    """
    Class to test the model
    Kid
    """

    def setUp(self):
        """
        Set up all the tests
        """
        self.kid = mommy.make(
            Kid,
            age=3
        )

        self.another_kid = mommy.make(
            'family.Kid',
            age=6
        )
```
關連物件屬性也可以達成

```python
# -*- coding:utf-8 -*-

#Core Django imports
from django.test import TestCase

#Third-party app imports
from model_mommy import mommy
from model_mommy.recipe import Recipe, foreign_key

# Relative imports of the 'app-name' package
from .models import Dog

class DogTestModel(TestCase):
    """
    Class to test the model
    Dog
    """

    def setUp(self):
        """
        Set up all the tests
        """

        self.bobs_dog = mommy.make(
            'family.Dog',
            owner__name='Bob'
        )
```
## Non persistent objects
如果你不要保留物件，Mommy也可以辦到。

```python
from model_mommy import mommy 
kid = mommy.prepare('family.Kid')
```
## More than one instance
假如你需要超過一個以上的物件實例，你可以使用 _quantity 參數。

```python
from model_mommy import mommy

kids = mommy.make('family.Kid', _quantity=3)
assert len(kids) == 3
```
prepare，也可以用。

```python
from model_mommy import mommy

kids = mommy.prepare('family.Kid', _quantity=3)
assert len(kids) == 3
```

