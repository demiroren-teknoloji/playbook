# Python

- Indentation (girintileme) için SOFT-TABS (`\t` karakteri yerine `boşluk`
  SPACE) kullanılmalı
- Indentation (girintileme) miktarı `4 BOŞLUK KARAKTERİ` (4 SPACES)
  kullanılmalı
- Geliştirme yapılan IDE/Editör’e [EditorConfig][editor-config] eklentisi
  yapılabilir
- Otomatik kod düzenü/şekli için (format) [Black][black] kullanılmalı
- Kod kontrolü için [flake8][flake8] kullanılmalı

## Python Kodu Yazarken Dikkat Edilecek Durumlar

Kod satırı için önerilen karakter sayısı önceleri `72` karakterdi. Daha sonra
bu [Black][black] ile beraber `88`’e yükseldi. Bir satırımız `88` karakterden
oluşabilir.

Yorum (*comment*) satırı ve `docstring` için [PEP 8][pep8] `72` karaktere
izin verir.

```python
# ----------------------------------------------------------------------
# this is a comment
# ----------------------------------------------------------------------

class Foo:
    """

    ------------------------------------------------------------------------
    123456789x123456789x123456789x123456789x123456789x123456789x123456789x12
    ------------------------------------------------------------------------

    """
```

Konu ile ilgili detaylar: https://www.python.org/dev/peps/pep-0257

---

Değişken, sabit ve benzeri tüm tanımlamalarda `snake_case` kullanılmalı. Sınıf
isimleri tanımlarken `InitialCaps` kullanılmalı. Değişken isimlerinin anlaşılır
olması, kısaltma gibi yazılmaması açık ve anlaşılır olması gerekmektedir.

Keza tüm tanımlamalar İngilizce olmalıdır.

```python
# İstenilen
customer_purchased_products_list = []
file_basename = os.path.basename('/foo/bar/baz.jpg')
username = 'vigo'

class BaseModelWithSoftDelete(models.Model):
    pass

# İstenilmeyen
cpList = []
fBaseName = os.path.basename('/foo/bar/baz.jpg')
usrnm = "vigo"

kullaniciAdi = 'vigo'
toplamUrun_sayisi = 5
```

---

String için mutlaka tek tırnak kullanılmalı. Çift tırnak sadece `docstring`
yazarken kullanılmalı:

```python
# İstenilen
a = 'Hello'
foo = 'bar'

# İstenilmeyen
a = "Hello"
```

Bu kuralı bozan tek durum eğer metnin içinde tek tırnak varsa durumudur:

```python
message = "I'm ready!"
message = 'I\'m ready!' # bu okunaklılık ve kullanım açısından zorlayabilir, hata yapma olasılığı var.
```


---

Dosya isimleri de aynen `snake_case` şeklinde olmalı.

```bash
# İstenilen
create_new_application.py

├── custom_management_commands/
│   ├── __init__.py
│   └── import_xls_files.py
│

# İstenilmeyen
create-new-application.py # dash kullanılmamalı
```

---

Uzun string’leri **4 SPACE HANGING** yöntemiyle okunur hale getirebilirsiniz.
[Black][black] bu işi sizin adınıza otomatik yapar.

```python
# Doğru
message = (
    'Hello! this is a very long string. Now we are using 4 SPACE HANGING convention'
    'Yes, this string will be displayed as one long line...'
)

# Yanlış
raise AttributeError('Here is a multine error message '
                     'shortened for clarity.')
```


---

String format, interpolation için;

- Python 3’le gelen `f-strings`’i kullanmak
- Yeni `.format` metodunu kullanmak

tavsiye edilir.

```python
# f-strings

a = 'Hello'
b = 'World'

c = f'{a}{b}'

# .format

'{0} {1}'.format(a, b)
'{a} {b}'.format(a=a, b=b)

vars = {'a': 'Hello', 'b': 'World'}

'{a} {b}'.format(**vars) 
```

---

`dict` construct ederken;

```python
a = {}
a['user_id'] = 1
```

yerine;

```python
a = dict(user_id=1)
```

şeklinde kullanmak iyidir. Hatta duruma göre `a = {}` yerine `a = dict()`
kullanmak daha güvenli olur. Keza aynı durum `a = []` yerine `a = list()`
de olabilir. Bazı durumlarda `[]` yerine `list()` ya da `{}` yerine `dict()`
kullanmak hız/performans düşüklüğü yapabiliyormuş. `[]` deklarasyon olarak
yorumlanırken `list()` function-call olarak Python tarafından yorumlanabiliyor:

```python
In [7]: time a = []                                                                                                                                                         
CPU times: user 3 µs, sys: 1 µs, total: 4 µs
Wall time: 7.15 µs

In [8]: time b = list()                                                                                                                                                     
CPU times: user 5 µs, sys: 1 µs, total: 6 µs
Wall time: 9.06 µs
```

Performans gereken yerde `[]` ya da `{}` kullanılmalı...

---

Her ne olursa olsun, kodun içinde `print()` kullanılmamalı. Konsola’a loglanmalı.
En çaresiz durumda `pprint` kullanılabilir ama `dev/prod` ortamına push
edilen kodda bulunmamalı

---

Revizyon kontrolü altındaki dosyalarda asla **comment out** edilmiş kod
**bırakılmamalı**.

---

## `import`’lar

Mutlaka `import` ifadeleri düzgün sırada yapılmalı. Bunun için `isort`
paketi kullanılmalı.

https://github.com/timothycrosley/isort#readme

---

## Faydalı Flake8 Paketleri

- flake8
- flake8-bandit
- flake8-blind-except
- flake8-bugbear
- flake8-builtins
- flake8-polyfill
- flake8-print
- flake8-quotes
- flake8-string-format

---

## Linkler

- https://github.com/PyCQA



[editor-config]: https://editorconfig.org/
[black]: https://github.com/ambv/black
[flake8]: https://gitlab.com/pycqa/flake8
[pep8]: https://www.python.org/dev/peps/pep-0008
