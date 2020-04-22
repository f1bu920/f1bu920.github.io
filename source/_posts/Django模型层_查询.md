---
title: My New Post
date: 2019-11-22 22:26:19
categories: Django
tags: 
  - Python 
  - Django model
---

## 执行查询

创建数据模型后，Django自动提供了一套API来实现数据库的增删改查，模型代码如下

~~~python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    n_comments = models.IntegerField()
    n_pingbacks = models.IntegerField()
    rating = models.IntegerField()

    def __str__(self):
        return self.headline
~~~

<!--more--> 

- 创建对象

  Django中一个模型就代表数据库中的一张表，模型类的实例代表数据库表中的一行记录。

  创建一个对象进行初始化后，可调用save()保存到数据库中 ，可重写save()方法实现自定义保存

  ~~~python
  >>> from blog.models import Blog
  >>> b = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')
  >>> b.save()
  ~~~

- 修改对象

  修改后调用save()方法保存

  ~~~python
  >>> b5.name = 'New name'
  >>> b5.save()
  ~~~

  - 保存ForeignKey和ManyToManyField字段

    更新ForeignKey字段：

    ~~~python
    >>> from blog.models import Blog, Entry
    >>> entry = Entry.objects.get(pk=1)
    >>> cheese_blog = Blog.objects.get(name="Cheddar Talk")
    >>> entry.blog = cheese_blog
    >>> entry.save()
    ~~~

    更新ManyToManyField字段要调用add()方法

    ~~~python
    >>> from blog.models import Author
    >>> joe = Author.objects.create(name="Joe")
    >>> entry.authors.add(joe)
    ~~~

    add()方法可一次传入多个参数

    ~~~python
    entry.authors.add(john, paul, george, ringo)
    ~~~

- 检索对象

  通过模型类的Manager来构建queryset，每个模型至少有一个Manager，默认名称是objects

  - 检索全部对象

    all()方法返回全部对象

    ~~~python
    all_entries = Entry.objects.all()
    ~~~

  - 通过过滤器检索指导对象

    `filter(**kwargs)`

    返回一个新的 [`QuerySet`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet)，包含的对象满足给定查询参数。

    `exclude(**kwargs)`

    返回一个新的 [`QuerySet`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet)，包含的对象不满足给定查询参数

    ~~~python
    Entry.objects.filter(pub_date__year=2006) # 筛选出满足条件的对象
    Entry.objects.exclude(pub_date__year=2006) # 筛选出不满足条件的对象
    ~~~

    - 链式过滤器

      queryset的结果还是返回一个新的queryset，所以可链式调用

      ~~~python
      >>> Entry.objects.filter(
      ...     headline__startswith='What'
      ... ).exclude(
      ...     pub_date__gte=datetime.date.today()
      ... ).filter(
      ...     pub_date__gte=datetime.date(2005, 1, 30)
      ... )
      ~~~

     - queryset都是惰性的

       创建 [`QuerySet`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet) 并不会引发任何数据库活动。你可以将一整天的过滤器都堆积在一起，Django 只会在 [`QuerySet`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet) 被 计算 时执行查询操作 。

  - 用get()获得单个对象

    不同于filter()总是返回一个queryset，get()只会返回单个对象，当返回多个对象时Django 会抛出 [`MultipleObjectsReturned`](https://docs.djangoproject.com/zh-hans/2.2/ref/exceptions/#django.core.exceptions.MultipleObjectsReturned)。

    ~~~python
    one_entry = Entry.objects.get(pk=1)
    ~~~

  - 限制queryset条目数

    利用python切片语法将queryset切成指定长度，等价于 SQL 的 `LIMIT` 和 `OFFSET` 子句 

    ~~~python
    # 返回前5个对象
    Entry.objects.all()[:5]
    # 返回第6到10个对象
    Entry.objects.all()[5:10]
    ~~~

  - 字段查询

    字段查询即你如何制定 SQL `WHERE` 子句。 它们以关键字参数的形式传递给 [`QuerySet`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet) 方法 [`filter()`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet.filter)， [`exclude()`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet.exclude) 和 [`get()`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/querysets/#django.db.models.query.QuerySet.get)。 

    **基本的查询关键字参数遵照 `field__lookuptype=value` **.

    ~~~python
    Entry.objects.filter(pub_date__lte='2018-09-20')
    ~~~

    > | 转化成SQL语句大致是：
    >
    >    SELECT * FROM blog_entry WHERE pub_date <= '2018-09-20';

  - 跨关系查询

    要跨越关系，只需跨模型使用关联字段名，字段名由双下划线分割，直到拿到想要的字段。 

    ~~~python
    Entry.objecst.filter(blog__name='flbu blog')
    ~~~

    反向操作也能行。要指向一个“反向的”关联关系，只需使用模型名的小写。 

    ~~~python
    Blog.objects.filter(entry__headline__contains='Lennon')
    ~~~

  - 过滤器为模型指定字段

    将模型字段与同一模型的另一字段作比较。

    - F表达式

      F()的实例充当查询字段的引用，这些引用可以实现在同一模型实例中比较不同的字段。

      ~~~python
       from django.db.models import F
       Entry.objects.filter(number_of_comments__gt=F('number_of_pingbacks'))
      ~~~

      Django支持对`F()`对象进行加减乘除求余和次方等数学运算，另一操作数既可以是常量，也可是另一F()对象。

      ~~~python
      Entry.objects.filter(number_of_comments__gt=F('number_of_pingbacks') * 2)
      Entry.objects.filter(rating__lt=F('number_of_comments') + F('number_of_pingbacks'))
      ~~~

      **也可以用双下划线在`F()`对象中进行跨关系查询**，带有双下划线的 `F()` 对象将引入访问关联对象所需的任何连接 。

      ~~~python
      Entry.objects.filter(authors__name=F('blog__name'))
      ~~~

  - 主键(pk)查询快捷方式

    `pk` 表示主键 "primary key" 。

    在示例Blog模型中，主键是id，所以以下查询等价

    ~~~python
     Blog.objects.get(id__exact=14) # Explicit form
     Blog.objects.get(id=14) # __exact is implied
     Blog.objects.get(pk=14) # pk implies id__exact
    ~~~

    `pk`的查询并不局限于`__exact`，任何的查询项都能接在`pk`的后面

    ~~~python
    # Get blogs entries with id 1, 4 and 7
     Blog.objects.filter(pk__in=[1,4,7])
    
    # Get all blog entries with id > 14
     Blog.objects.filter(pk__gt=14)
    ~~~

    `pk`也支持跨连接

    ~~~python
     Entry.objects.filter(blog__id__exact=3) # Explicit form
     Entry.objects.filter(blog__id=3)        # __exact is implied
     Entry.objects.filter(blog__pk=3)        # __pk implies __id__exact
    ~~~

  - 通过Q对象完成复杂查询

    在`filter()`中，查询使用的关键字参数使用AND连接，如果要执行更加复杂的查询，可以使用`Q`对象

    `Q` 对象能通过 `&` 和 `|` 操作符连接起来。当操作符被用于两个 `Q` 对象之间时会生成一个新的 `Q` 对象。 

    ~~~python
    from django.db.models import Q
    Q(question__startswith='Who') | Q(question__startswith='What')
    # 等价于SQL WHERE 语句：WHERE question LIKE 'Who%' OR question LIKE 'What%'
    ~~~

    `Q`对象可通过`~`反转，代表NOT

    ~~~python
    Q(question__startswith='Who') | ~Q(pub_date__year=2005)
    ~~~

    每个查询函数可接受一个或多个`Q`对象作为位置参数，若提供了多个`Q`对象参数，这些参数会通过 "AND" 连接 。

    ~~~python
    Poll.objects.get(
        Q(question__startswith='Who'),
        Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))
    )
    ~~~

    查询函数可混合使用 `Q` 对象和关键字参数 ，均通过 "AND" 连接 。但**`Q` 对象必须位于所有关键字参数之前**。 

  - 比较对象总会使用主键值

  - 删除对象

    通过调用`delete()`方法删除对象，也可以通过`QuerySet`批量删除对象

    ~~~python
    >>> e.delete()
    # 返回被删除的对象数量和一个包含了每个被删除对象类型的数量的字典
    (1, {'weblog.Entry': 1})
    # 删除QuerySet中的所有成员
    >>> Entry.objects.filter(pub_date__year=2005).delete()
    (5, {'webapp.Entry': 5})
    ~~~

  - 批量修改对象

    可通过`update()`修改`QuerySet`中所有对象的一个字段，仅能用此方法设置非关联字段和 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/fields/#django.db.models.ForeignKey) 字段 

    调用更新方法时也可以使用`F`表达式，但只能引用被更新模型的内部字段 。若在更新方法中使用 `F()` 对象的同时使用 join ，会抛出一个 `FieldError()` 

    ~~~py
    Entry.objects.all().update(number_of_pingbacks=F('number_of_pingbacks') + 1)
    
    # This will raise a FieldError
     Entry.objects.update(headline=F('blog__name'))
    ~~~

  - 关联对象

    一个 `Entry` 对象 `e` 通过 `blog` 属性可以获取其关联的 `Blog` 对象： `e.blog` ，一个 `Blog` 对象 `b` 能通过 `entry_set` 属性 `b.entry_set.all()` 访问包含所有关联 `Entry` 对象的列表 。

    - 一对多关联

      - 正向访问

        对外键的修改直到你调用 [`save()`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/instances/#django.db.models.Model.save) 后才会被存入数据库 

        ~~~py
         e = Entry.objects.get(id=2)
         e.blog # Returns the related Blog object
         
         e = Entry.objects.get(id=2)
         e.blog = some_blog
         e.save()
        ~~~

      - 反向关联

        ~~~python
        b = Blog,objects.get(pk=1)
        b.entry_set.all()
        ~~~

        可以在定义 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/fields/#django.db.models.ForeignKey) 时设置 [`related_name`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/fields/#django.db.models.ForeignKey.related_name) 参数重写这个 `FOO_set` 名。例如，若修改 `Entry` 模型为 `blog = ForeignKey(Blog, on_delete=models.CASCADE, related_name='entries')`，前文示例代码会看起来像这样 :

        ~~~python
        >>> b = Blog.objects.get(id=1)
        >>> b.entries.all() # Returns all Entry objects related to Blog.
        
        # b.entries is a Manager that returns QuerySets.
        >>> b.entries.filter(headline__contains='Lennon')
        >>> b.entries.count()
        ~~~

      - 管理关联对象的方法

        1. `add(obj1, obj2, ...)`

           将特定的模型对象加入关联对象集合。

        2. `create(**kwargs)`

           创建一个新对象，保存，并将其放入关联对象集合中。返回新创建的对象。

        3. `remove(obj1, obj2, ...)`

           从关联对象集合删除指定模型对象.

        4. `clear()`

           从关联对象集合删除所有对象。

        5. `set(objs)`

           替换关联对象集合

           ~~~python
           b = Blog.objects.get(id=1)
           b.entry_set.set([e1, e2])
           ~~~

    - 多对多关联

      ~~~python
      e = Entry.objects.get(id=3)
      e.authors.all() # Returns all Author objects for this Entry.
      e.authors.count()
      e.authors.filter(name__contains='John')
      
      a = Author.objects.get(id=5)
      a.entry_set.all() # Returns all Entry objects for this Author.
      ~~~

      和 [`ForeignKey`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/fields/#django.db.models.ForeignKey) 一样， [`ManyToManyField`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/fields/#django.db.models.ManyToManyField) 能指定 [`related_name`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/fields/#django.db.models.ManyToManyField.related_name)。在上面的例子中，若 `Entry` 中的 [`ManyToManyField`](https://docs.djangoproject.com/zh-hans/2.2/ref/models/fields/#django.db.models.ManyToManyField) 已指定了 `related_name='entries'`，随后每个 `Author` 实例会拥有一个 `entries` 属性，而不是 `entry_set`。 