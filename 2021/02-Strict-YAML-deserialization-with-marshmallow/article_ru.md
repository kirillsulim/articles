# Строгая десериализация YAML в Python c библиотекой marshmallow

## Исходная задача

- Необходимо прочитать нетривиальный конфиг из .yaml файла. 
- Структура конфига описана с помощью дата-классов.
- Необходимо, чтобы при десериализации были выполнены проверки типов, и, если данные невалидны, было брошено исключение.

То есть, проще говоря, нужна функция вида:

```python
def strict_load_yaml(yaml: str, loaded_type: Type[Any]):
    """
    Here is some magic
    """
    pass
```

И эта функция будет использоваться следующим образом:

```python
@dataclass
class MyConfig:
    """
    Here is object tree
    """
    pass

try:
    config = strict_load_yamp(open("config.yaml", "w").read(), MyConfig)
except Exception:
    logging.exception("Config is invalid")
```

## Классы конфигурации

Файл `config.py` выглядит следующим образом:

```python
from dataclasses import dataclass
from enum import Enum
from typing import Optional


class Color(Enum):
    RED = "red"
    GREEN = "green"
    BLUE = "blue"


@dataclass
class BattleStationConfig:
    @dataclass
    class Processor:
        core_count: int
        manufacturer: str

    processor: Processor
    memory_gb: int
    led_color: Optional[Color] = None

```

## Вариант, который не работает

Исходная задача встречается часто, не так ли?
Значит решение должно быть тривиальным.
Просто импортируем стандартную yaml-библиотеку и задача решена?

Делаем импорт [PyYaml](https://github.com/yaml/pyyaml) и вызываем функцию `load`:

```python
from pprint import pprint

from yaml import load, SafeLoader


yaml = """
processor:
  core_count: 8
  manufacturer: Intel
memory_gb: 8
led_color: red
"""

loaded = load(yaml, Loader=SafeLoader)
pprint(loaded)

```

и в результате получим:

```
{'led_color': 'red',
 'memory_gb': 8,
 'processor': {'core_count': 8, 'manufacturer': 'Intel'}}
```

Yaml прекрасно загрузился, но в виде словаря.
Это не проблема, можно передать словарь как `**args` в конструктор и...

```python
BattleStationConfig(processor={'core_count': 8, 'manufacturer': 'Intel'}, memory_gb=8, led_color='red')
```

результатом будет:

```
BattleStationConfig(processor={'core_count': 8, 'manufacturer': 'Intel'}, memory_gb=8, led_color='red')
```

Вау! Легко! Но... Подождите-ка. Поле processor это словарь? Черт побери.

Python не выполняет проверку типов в конструкторе и не преобразует аргументы к классу `Processor`.
Значит настало время идти на stackowerflow.

## Решение, которое требует yaml-теги и почти работает

Я прочитал вопросы и ответы на stackowerflow и документацию к PyYaml и выяснил, что yaml-документ может быть помечен тегами для определения типов.
Классы в документе должны быть потомками`YAMLObject`, и файл `config_with_tag.py` будет выглядеть так:

```python
from dataclasses import dataclass
from enum import Enum
from typing import Optional

from yaml import YAMLObject, SafeLoader


class Color(Enum):
    RED = "red"
    GREEN = "green"
    BLUE = "blue"


@dataclass
class BattleStationConfig(YAMLObject):
    yaml_tag = "!BattleStationConfig"
    yaml_loader = SafeLoader

    @dataclass
    class Processor(YAMLObject):
        yaml_tag = "!Processor"
        yaml_loader = SafeLoader

        core_count: int
        manufacturer: str

    processor: Processor
    memory_gb: int
    led_color: Optional[Color] = None
```

а код для загрузки так:


```python
from pprint import pprint

from yaml import load, SafeLoader

from config_with_tag import BattleStationConfig


yaml = """
--- !BattleStationConfig
processor: !Processor
  core_count: 8
  manufacturer: Intel
memory_gb: 8
led_color: red
"""

a = BattleStationConfig

loaded = load(yaml, Loader=SafeLoader)
pprint(loaded)
```

И что получится в результате десериализации?

```
BattleStationConfig(processor=BattleStationConfig.Processor(core_count=8, manufacturer='Intel'), memory_gb=8, led_color='red')
```

Неплохо. Но теперь yaml-документ наполовину состоит из тегов и потерял читаемость. К тому же, `Color` по-прежнему читается как строка.
Может нужно просто добавить `YAMLObject` в список родительских классов? Так? Увы, нет.
Код

```python
class Color(Enum, YAMLObject):
    RED = "red"
    GREEN = "green"
    BLUE = "blue"
```

приведет к ошибке:

```
TypeError: metaclass conflict: the metaclass of a derived class must be a (non-strict) subclass of the metaclasses of all its bases
```

Я не нашел решения этой проблемы за разумное время.
К тому же я не хотел добавлять теги к yaml-документу, поэтому продолжил искать другие варианты решения исходной задачи.

## Решение с библиотекой marshmallow

На stackowerflow я нашел рекомендацию использовать библиотеку [marshmallow](https://github.com/marshmallow-code/marshmallow) для парсинга словаря, полученного при десериализации JSON-объекта.
Я решил, что это случай аналогичной исходной задаче, за исключением того, что в нашей задаче используется yaml вместо JSON.
Попробуем использовать генератор `class_schema`, чтобы получить схему дата-класса:

```python
from pprint import pprint

from yaml import load, SafeLoader
from marshmallow_dataclass import class_schema

from config import BattleStationConfig


yaml = """
processor:
  core_count: 8
  manufacturer: Intel
memory_gb: 8
led_color: red
"""

loaded = load(yaml, Loader=SafeLoader)
pprint(loaded)

BattleStationConfigSchema = class_schema(BattleStationConfig)

result = BattleStationConfigSchema().load(loaded)
pprint(result)

```

и, в результате, получим:

```
marshmallow.exceptions.ValidationError: {'led_color': ['Invalid enum member red']}
```

Значит, marshmallow хочет имя enum, а не его значение. Можно немного изменить исходный yaml-документ на:

```yaml
processor:
  core_count: 8
  manufacturer: Intel
memory_gb: 8
led_color: RED
```

И, в результате, мы получим идеально десериализованный объект:

```
BattleStationConfig(processor=BattleStationConfig.Processor(core_count=8, manufacturer='Intel'), memory_gb=8, led_color=<Color.RED: 'red'>)
```

Но у меня все еще остается чувство, что можно использовать оригинальный yaml-документ.
Я продолжил исследование документации marshmallow и нашел следующие строчки:

> Setting `by_value=True`. This will cause both dumping and loading to use the value of the enum.

Оказывается, можно передать следующую конфигурацию в словарь `metadata` генератора датакласса `field`:

```python
@dataclass
class BattleStationConfig:
    led_color: Optional[Color] = field(default=None, metadata={"by_value": True})
```

И таким образом, мы получим ту самую "магическую" функцию, которая сможет распарсить исходный yaml-документ.

## Магическая функция

Теперь мы знаем, как выглядит тело магической функции:

```python
def strict_load_yaml(yaml: str, loaded_type: Type[Any]):
    schema = class_schema(loaded_type)
    return schema().load(load(yaml, Loader=SafeLoader))
```

Эта функция может потребовать дополнительной настройки для дата-классов, но решает исходную задачу и не требует наличия тегов в yaml.

## Небольшая заметка о ForwardRef

Если определить дата-классы с ForwardRef (строка с именем класса) marshmallow будет озадачена и не сможет распарсить этот класс.

Например, такая конфигурация

```python
from dataclasses import dataclass, field
from enum import Enum
from typing import Optional, ForwardRef


@dataclass
class BattleStationConfig:
    processor: ForwardRef("Processor")
    memory_gb: int
    led_color: Optional["Color"] = field(default=None, metadata={"by_value": True})

    @dataclass
    class Processor:
        core_count: int
        manufacturer: str


class Color(Enum):
    RED = "red"
    GREEN = "green"
    BLUE = "blue"

```

приведет к ошибке

```
marshmallow.exceptions.RegistryError: Class with name 'Processor' was not found. You may need to import the class.
```

И если переместить класс `Processor` выше, marshmallow потеряет класс `Color` с аналогичной ошибкой. 
Так что, по возможности, не используйте ForwardRef для ваших классов, если хотите парсить их с помощью marshmallow.

## Код

Весь код доступен в [репозитории на GitHub](https://github.com/kirillsulim/python-strict-yaml-parsing).
