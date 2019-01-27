# Python 代码规范

觉得有用的给颗星补贴家用呀!

> 翻译自: [https://gist.github.com/sloria/7001839](https://gist.github.com/sloria/7001839) (有些翻译的不太好就只能对照一下英文原文来理解了 ʅ(‾◡◝)ʃ ）

## 通用的标准

### 价值观

- "给别人写的工具要达到自己也愿意用的标准。" - Kenneth Reitz
- "简单比功能更重要。" - Pieter Hintjens
- "适合90％的用例，忽略那些说话的人。" - Kenneth Reitz
- "优美的总比丑的好。" - [PEP 20][]
- 为开源贡献 (为不开源的项目也贡献) 。 

### 通用的开发指导

- "明确好于不明确。" - [PEP 20][]
- "可读性很重要。" - [PEP 20][]
- "代码写完以后任何人都能来解决任何问题。" - [Khan Academy Development Docs][]
- 发现 [broken window(破窗理论那个破窗)](http://www.artima.com/intv/fixit2.html) (坏的设计, 不对的决定, 或者不好的代码) *就马上解决*。
- "现在着手去做比不做要强。" - [PEP 20][]
- 用全力去测试，为新特性写文档。
- 测试驱动的开发--*人驱动的开发*，是很重要的。
- 这些指导方针会，并且很可能会，改变。

## 关于 Python 的标准

### 风格

聪明的孩子都遵循 [PEP 8][]。

#### 命名
（命名我觉得还是英文更清晰你说是吧）

- Variables（变量）, functions, methods, packages, modules
    - `lower_case_with_underscores`
- Classes and Exceptions
    - `CapWords`
- Protected methods and internal functions
    - `_single_leading_underscore(self, ...)`
- Private methods
    - `__double_leading_underscore(self, ...)`
- Constants（常量）
    - `ALL_CAPS_WITH_UNDERSCORES`

###### 通用命名指南

避免单个字母的变量 (尤其是， `l`, `O`, `I`)。

*除非*: 在非常短的代码块中，变量意义上下文直接可见

**下面的代码就是可以的**

```python
for e in elements:
    e.mutate()
```

避免变量名冗余。

**正确**

```python
import audio

core = audio.Core()
controller = audio.Controller()
```

**错误**

```python
import audio

core = audio.AudioCore()
controller = audio.AudioController()
```

最好要 "反向标记"。

**正确**

```python
elements = ...
elements_active = ...
elements_defunct = ...
```

**错误**

```python
elements = ...
active_elements = ...
defunct_elements ...
```

避免用 getter, setter 方法

**正确**

```python
person.age = 42
```

**错误**

```python
person.set_age(42)
```

#### 缩进排版

都说那么多次了，用 4 下空格键 -- 别用 tab。

#### Imports 方法

导入整个 module，别导入其中一个或几个 symbol。例如, 对于 module `canteen` 来说有文件 `canteen/sessions.py`,

**正确**

```python
import canteen
import canteen.sessions
from canteen import sessions
```

**错误**

```python
from canteen import get_user  # Symbol from canteen/__init__.py
from canteen.sessions import get_session  # Symbol from canteen/sessions.py
```

*例外*: 有的第三方代码文档中说明要直接导入单个 symbol的。

*原因*: 避免循环导入。 看 [这里](https://sites.google.com/a/khanacademy.org/forge/for-developers/styleguide/python#TOC-Imports)。

把所有的 imports 放在代码最上面，并且分成三类用空格隔开，顺序如下

1. System imports（系统自带的）
2. Third-party imports （第三方的）
3. Local source tree imports （本地的）

*原因*：开发者能更清晰地读出 models 的来源。

#### 注释

遵循 [PEP 257][] 的注释准则。 [reStructured Text](http://docutils.sourceforge.net/docs/user/rst/quickref.html) 和 [Sphinx](http://sphinx-doc.org/) 能帮助你更好地执行这些标准。

用一行来注释显而易见的方法

```python
"""Return the pathname of ``foo``."""
```

多行注释应该包括

- 概要
- 用例（如果可以的话）
- Args （参数）
- 返回的类型

```python
"""Train a model to classify Foos and Bars.

Usage::

    >>> import klassify
    >>> data = [("green", "foo"), ("orange", "bar")]
    >>> classifier = klassify.train(data)

:param train_data: A list of tuples of the form ``(color, label)``.
:rtype: A :class:`Classifier <Classifier>`
"""
```

Notes

- 用动词 ("Return") 而不是用名词 ("Returns").
- 注释 class 中的 `__init__` 方法。

```python
class Person(object):
    """A simple representation of a human being.

    :param name: A string, the person's name.
    :param age: An int, the person's age.
    """
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

##### 注解

减少使用注解，一般来说多用一些 small methods 比注解更有效。

*错误*

```python
# If the sign is a stop sign
if sign.color == 'red' and sign.sides == 8:
    stop()
```

*正确*

```python
def is_stop_sign(sign):
    return sign.color == 'red' and sign.sides == 8

if is_stop_sign(sign):
    stop()
```

当你写注解时, 想想: "Strunk and White apply."（我也不知道这是啥，摊手） - [PEP 8][]

#### 每一行的长度

不要让你的注释阅读困难。 80-100 个字符差不多。

可以把长的字串加上小括号。

```python
wiki = (
    "The Colt Python is a .357 Magnum caliber revolver formerly manufactured "
    "by Colt's Manufacturing Company of Hartford, Connecticut. It is sometimes "
    'referred to as a "Combat Magnum". It was first introduced in 1955, the '
    "same year as Smith & Wesson's M29 .44 Magnum."
)
```

### 测试

争取 100% 的覆盖率。不过也别太钻牛角尖。

#### 通用测试指南

- 可以用长的，描述性的命名方式。这样可以不需要对 test methods 注释。
- 测试是独立的。别用真实的网络或数据库，可以用分离的用模拟数据的测试数据库。
- Prefer [factories](https://github.com/rbarrois/factory_boy) to fixtures. （感觉是一个用于测试的 model）
- 别让没完成的测试通过，不然你很可能把它忘了。此外，你应该加上一个 placeholder: `assert False, "TODO: finish me"`。

#### 单元测试

- 专注于一小部分功能。
- 最好要快，不过即使慢也不能不测试。
- 对于单个 class 或 model 可以提供一个专门的测试用例。

```python
import unittest
import factories

class PersonTest(unittest.TestCase):
    def setUp(self):
        self.person = factories.PersonFactory()

    def test_has_age_in_dog_years(self):
        self.assertEqual(self.person.dog_years, self.person.age / 7)
```

#### 功能测试

功能测试是更高一个级别的测试，更贴近用户操作你的应用的场景。一般用于 web 和 GUI 的应用。

- 把测试写的场景化。测试用例和测试方法名称应该对一个场景的描述。
- 在代码前用注释写出这句代码的故事（功能）。

```python
import unittest

class TestAUser(unittest.TestCase):

    def test_can_write_a_blog_post(self):
        # Goes to the her dashboard
        ...
        # Clicks "New Post"
        ...
        # Fills out the post form
        ...
        # Clicks "Submit"
        ...
        # Can see the new post
        ...
```

注意测试用例以及方法名连起来读就是： "Test A User can write a blog post"，是不是很好用。


## Inspired by...

- [PEP 20 (The Zen of Python)][PEP 20]
- [PEP 8 (Style Guide for Python)][PEP 8]
- [The Hitchiker's Guide to Python][python-guide]
- [Khan Academy Development Docs][]
- [Python Best Practice Patterns][]
- [Pythonic Sensibilities][]
- [The Pragmatic Programmer][]
- and many other bits and bytes

[Pythonic Sensibilities]: http://www.nilunder.com/blog/2013/08/03/pythonic-sensibilities/
[Python Best Practice Patterns]: http://youtu.be/GZNUfkVIHAY
[python-guide]: http://docs.python-guide.org/en/latest/
[PEP 20]: http://www.python.org/dev/peps/pep-0020/
[PEP 257]: http://www.python.org/dev/peps/pep-0257/
[PEP 8]: http://www.python.org/dev/peps/pep-0008/
[Khan Academy Development Docs]: https://sites.google.com/a/khanacademy.org/forge/for-developers
[The Pragmatic Programmer]: http://www.amazon.com/The-Pragmatic-Programmer-Journeyman-Master/dp/020161622X/ref=sr_1_1?ie=UTF8&qid=1381886835&sr=8-1&keywords=pragmatic+programmer
