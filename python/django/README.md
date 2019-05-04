# Django

- Indentation (girintileme) için SOFT-TABS (`\t` karakteri yerine `boşluk`
  SPACE) kullanılmalı
- HTML template kullanımında Indentation (girintileme) miktarı `2 BOŞLUK
  KARAKTERİ` (2 SPACES) ya da `4 BOŞLUK KARAKTERİ` (4 SPACES) kullanılabilir

---

## `import`’lar

Mutlaka `import` ifadeleri düzgün sırada yapılmalı. Bunun için `isort`
paketi kullanılmalı.

https://github.com/timothycrosley/isort#readme

`import` gruplama sırası:

1. Future import’ları
1. Standart kütüphaneden gelenler
1. Django bilenşenleri
1. 3.Parti kütüphaneler
1. Yerel Django bilenşenleri
1. `try/except` durumları

Örnek `.isort.cfg` dosyası:

```ini
[settings]
line_length = 60
multi_line_output = 3
use_parentheses = true
include_trailing_comma = true
quiet = true
force_grid_wrap = 0
known_django = django
sections = FUTURE,STDLIB,DJANGO,THIRDPARTY,FIRSTPARTY,LOCALFOLDER
```

Örnek:

```python
# Future
from __future__ import unicode_literals

# Standart kütüphaneden gelenler
import json
from itertools import chain

# Django bilenşenleri
from django.http import Http404
from django.http.response import (
    Http404,
    HttpResponse,
    HttpResponseNotAllowed,
    StreamingHttpResponse,
    cookie,
)

# 3.Parti kütüphaneler
import bcrypt

# Yerel Django bilenşenleri
from .models import LogEntry

# try/except
try:
    import yaml
except ImportError:
    yaml = None

CONSTANT = 'foo'
```

Yerel, bağıntılı tarzda modül import etmeye gayret edin. Aynı `app` içindeki
`models.py` dosyasından ya da aynı paket içinden giderken;

```python
from .models import Foo

# ya da yerine göre
from ..models import Foo

# hatta
from ...models import Foo
```

---

## Django Template Language (DTL) İsimlendirme Kuralları

- `{{ variable }}` ya da `{{ VARIABLE }}` - Template değişkeni
- `{% tag "argument" %}` - Template tag’i
- `{% my_tag %}...{% end_my_tag %}` - Blok template tag’i
- `{{ variable|filter }}` - değişkeni filtreye göndermek

---

## Model Yazma Kuralı

Model adı;

1. Mutlaka tekil olur
1. `InitialCaps` şeklindedir

Field adlı;

1. `Many2Many` ve bezeri durumlar dışında tekil olur
1. Küçük harflerle yazılır
1. `snake_case` şeklindedir

```python
# Doğru

class Post(models.Model):
    title = models.CharField(max_length=255, verbose_name=_('title'))

class Person(models.Model):
    name = models.CharField(max_length=255, verbose_name=_('name'))


# Yanlış
class Posts(models.Model):
    Title = models.CharField(max_length=255, verbose_name=_('title'))
```

Model tanımı içinde sıralama aşağıdaki gibi olmalıdır:

1. Eğer `choises` varsa class attribute olarak yazılmalı
1. Tüm alan tanımlamaları
1. Custom Manager tanımlamaları
1. `class Meta`
1. `def __str__()`
1. `def save()`
1. `def get_absolute_url()`
1. Kendi ürettiğiniz Custom method’lar...

Metod’lardan ve ek sınıflarda önce bir satır boşluk bırakılmalı, `ugettext_lazy`
kullanımında hep küçük harf kullanılmalıdır.

```python
from django.urls import reverse
from django.utils.translation import ugettext_lazy as _

class Post(models.Model):
    """
    Information about Post model...
    """

    STATUS_OFFLINE = 0
    STATUS_ONLINE = 1
    STATUS_DELETED = 2
    STATUS_DRAFT = 3
    STATUS_CHOICES = (
        (STATUS_OFFLINE, _('offline')),
        (STATUS_ONLINE, _('online')),
        (STATUS_DELETED, _('deleted')),
        (STATUS_DRAFT, _('draft')),
    )

    created_at = models.DateTimeField(auto_now_add=True, verbose_name=_('created at'))
    updated_at = models.DateTimeField(auto_now=True, verbose_name=_('updated at'))
    status = models.IntegerField(
        choices=STATUS_CHOICES, default=STATUS_ONLINE, verbose_name=_('status')
    )
    title = models.CharField(max_length=255, verbose_name=_('title'))

    objects = PostQuerySet.as_manager()

    class Meta:
        app_label = 'blog'
        verbose_name = _('post')
        verbose_name_plural = _('posts')

    def __str__(self):
        return self.title

    def get_absolute_url(self):
        return reverse('post_detail', args=[self.pk])

    def get_update_url(self):
        return reverse('post_update', args=[self.pk])

```

Tüm alan (*field*) tanımlamalarında mutlaka **keyword argument** prensibi
kullanılmalı, açık açık yazılmalıdır. Örneğin `User` modeline bir `FK`
yapılacaksa mutlaka `to='MODEL'` şeklinde (`dict` kullanımı) olmalıdır.
`related_name` modelin çoğulu olmalıdır:

```python
from django.db import models
from django.utils.translation import ugettext_lazy as _


class Product(models.Model):
    buyer = models.ForeignKey(
        to='User',
        related_name='products',
        on_delete=models.CASCADE,
        verbose_name=_('buyer'),
        help_text=_('who bought this item?'),
    )

u = User.objects.first()
u.products.all()
```

İlişki alan adını seçerken belli bir mantık kuralını takip etmek gerekir. Örneğin
`Article` diye bir model var, her `Article`’ın bir `User`’a `ForeignKey`
ilişkisi var. Bu durumda `Article` modelinde `user = models.ForeignKey(to='User')`
yerine `author = models.ForeignKey(to='User')` olmalı, yazının yazarı :)

Kullanmı tercihi olarak;


`unique=True` ile `ForeignKey` kullanmak yerine `OneToOneField` kullanın

`BooleanField` için `null=True` ya da `blank=True` kullanmayın
`models.BooleanField(null=True)` yerine `models.NullBooleanField()` kullanın

Eğer `User` diye bir model varsa ve `user_status` diye bir alan (*field*)
varsa bu çok gereksiz. Alan adının `status` olması yeterli. Alan adı içinde
model adını tekrar etmeyin!

Birden fazla `flag` tutmak yerine `choices` kullanın:

```python
# bunun yerine
class Article(models.Model):
    is_published = models.BooleanField(default=False)
    is_verified = models.BooleanField(default=False)
    :
    :

# bunun gibi bir kullanım tercih edilmeli
class Article(models.Model):
    STATUSES = Choices('new', 'verified', 'published')

    status = models.IntegerField(choices=STATUSES, default=STATUSES.draft)
```

Mümkünse `IntegerField` yerine `PositiveIntegerField` kullanın. `unique`,
`unique_together` durumlarında hayatınızı kolaylaştırır.

Fiyat gibi bir bilgi tutmak için `FloatField` yerine `DecimalField` kullanın.

`@property` ya da `@cached_property` dekoratörü kullandığımız method’lar asla
veritabanına istek **yapmamalı**, yani;

```python
# Yanlış örnek
class Author(models.Model):
    :
    :
    @property
    def number_of_books(self):
        return self.books.count()  # Book modeline bağlı ve veritabanı sorgusu yapıyor
```

---

## Model Query

Bir kaydın varlığını kontrol ederken `django.core.exceptions`’dan
`ObjectDoesNotExist` yerine, `ModelName.DoesNotExist` exception’ını yakalayın:

```python
logger = logging.getLogger('app')

try:
    p = Page.objects.get(url__startswith='/foo/')
    p.save()
except Page.DoesNotExist:
    logger.warning('page does not exists...')

```

---

`filter()` ya da `count()` öncesinde `all()` kullanmayın:

```python
User.objects.filter(...)
```

---

`latest` ve `earliest` kullanın!

```python
User.objects.all().order_by('-last_login')[0] # yerine
User.objects.latest('last_login')             # latest

User.objects.all().order_by('last_login')[0]  # yerine
User.objects.earliest('last_login')           # earliest
```

---

`len(QuerySet)` kullanmayın!

Her zaman; `User.object.all().count()` şeklinde kullanın!

---

Her zaman; `QuerySet` boş mu döndü? kontrolü için asla `if queryset: pass`
kullanmayın. Mutlaka `.exists()` kullanın:

```python
if Post.object.filter(status=Post.STATUS_ONLINE).exists():
    # burada işini yap
    pass
```

---

## `ModelForm` İpuçları

Mutlaka `Meta.fields` ile formda olması gereken alanları açıkca belirtin.
Mümkün mertebe `Meta.exclude` ya da `Meta.fields = '__all__'` şeklinde
kullanımdan kaçının.

---

## Diğer

`urls.py` içinde, `urlpatterns`’de isim verirken (*name*) asla **dash**
karakteri kullanmayın, underscore `_` kullanın:

```python
app_name = 'core'
urlpatterns = [
    path('', PostList.as_view(), name='post_list'),
    path('posts/<int:pk>', PostDetail.as_view(), name='post_detail'),
]
```

Template içinde kontrol yaparken, şunun yerine:

```django
{% if obj.customer_type == 'Member' %}
...
{% endif %}
```

Model içinde bir method’a bağlayıp:

```django
{% if obj.is_customer_type_member %}
...
{% endif %}
```

gibi işlem yapılmalı.

---

## Django Uygulaması (app) Dizin yapısı

    app/
    ├── admin
    │   ├── forms
    │   │   └── __init__.py
    │   ├── widgets
    │   │   └── __init__.py
    │   └── __init__.py
    ├── forms
    │   └── __init__.py
    ├── management
    │   ├── commands
    │   │   └── __init__.py
    │   ├── __init__.py
    │   └── base.py
    ├── middlewares
    │   └── __init__.py
    ├── migrations
    │   └── __init__.py
    ├── mixins
    │   └── __init__.py
    ├── models
    │   └── __init__.py
    ├── templatetags
    │   └── __init__.py
    ├── tests
    │   ├── test_models/
    │   │   └── __init__.py
    │   ├── test_templates/
    │   │   └── __init__.py
    │   ├── test_views/
    │   │   └── __init__.py
    │   └── __init__.py
    ├── utils
    │   └── __init__.py
    ├── views
    │   └── __init__.py
    ├── __init__.py
    ├── apps.py
    └── urls.py

---

## Linkler

- https://docs.djangoproject.com/en/2.2/internals/contributing/writing-code/coding-style/
- https://steelkiwi.com/blog/best-practices-working-django-models-python/
- https://github.com/octoenergy/conventions
