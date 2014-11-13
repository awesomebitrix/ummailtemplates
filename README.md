Bitrix-модуль ummailtemplates
=============================

**ummailtemplates** - это модуль для расширения функционала почтовых шаблонов Битрикса.
Модуль позволяет модифицировать почтовые шаблоны путем добавления разметки сторонних шаблонизаторов.

В данный момент модуль поставляется с шаблонизатором Twig.

Установка
=========

1. Клонируйте репозиторий в свой проект или скачайте архив и распакуйте в проект. Обычно модули располагаются в папках `/bitrix/modules/` или `/local/modules/` (начиная с 14-й версии)
**Внимание**: каталог с модулем обязательно должен называться **um.mail_template**
2. Из административной панели Битрикса стандартными средствами установите модуль
3. Перейдите в настройки модуля: укажите название класса, осуществляющего обработку разметки стороннего шаблонизатора и путь к файлу, где определен данный класс.
4. В требуемом шаблоне разместите разметку стороннего шаблонизатора.
5. PROFIT

Отслеживание ошибок
===================

Возникающие в процессе исполнения ошибки модуль регистрирует в стандартном битриксовом хранилище для событий (`event_log`) с названием события **Ошибка шаблонизатора**

Правила разметки для Twig
=========================

Поставляющийся вместе с модулем шаблонизатор Twig будет использован при следующих условиях:

1. Параметр шаблона **FIELD1** или **FIELD2** должен иметь название **TWIG_USED** и значение преобразуемое в булевое true.
2. Блок для шаблонизации должен выглядеть следующим образом:

```
#FOR_LOOP_START_1##LOOP_SOURCE:ORDERS#
{% for idx, order in orders %}
<a href="http://#SITE_NAME#/{{ order.link }}">{{ order.name }} [{{ order.user }}]</a><br />
{% endfor %}
#FOR_LOOP_END_1#
```

Здесь:

1. `#FOR_LOOP_START_1#` и `#FOR_LOOP_END_1#` - открывающий и закрывающий элемент блока разметки.
В шаблоне может быть несколько блоков разметки, каждый из них будет открываться и закрываться, например, парами:
  * `#FOR_LOOP_START_1#` и `#FOR_LOOP_END_1#` - первый блок
  * `#FOR_LOOP_START_2#` и `#FOR_LOOP_END_2#` - второй блок
  * Использование нечисловых символов после `FOR_LOOP_START_` не работает, то есть пара:
`#FOR_LOOP_START_ZZZ#` и `#FOR_LOOP_END_ZZZ#` распознана не будет.

2. `#LOOP_SOURCE:ORDERS#` - название ключа в массиве полей письма (`$arFields`), значение которого
будет передано в шаблонизатор. Внимание, значение ключа должно быть предварительно сериализовано
В данном случае, в массиве `$arFields` должен быть ключ `ORDERS`, значение которого будет десериализовано и
передано в шаблонизатор в качестве параметра `orders`.
*Внимание*: название ключа в `#LOOP_SOURCE:ORDERS#` и итерируемый объект `{% for idx, order in orders %}` должны
различаться только регистром: `ORDERS` и `orders`.

3. Весь код между `#LOOP_SOURCE:ORDERS#` и `#FOR_LOOP_END_1#` - стандартная раметка Twig.

Внимание: мной проверялась только простейшая разметка Twig с циклом for. Все остальные варианты разметок **не проверялись**.
