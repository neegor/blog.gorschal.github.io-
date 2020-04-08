---
layout: post
title: Python на колесах
categories:
  - Develop
tags:
  - develop
  - python
  - wheels
---

***Wheels*** в питоне настолько старая фича что про нее все забыли примерно в 2005 году. Так что это такое и почему я про это решил напомнить вам.

Основной формат распространения приложений в питоне это исходные файлы. Тем не менее существуют и бинарные форматы. Например ```egg```. ***Python eggs*** это обычные zip-архивы, содержащие python-пакет и необходимые метаданные. Это не было идеальным решение и в один прекрасный момент на смену ему пришел ***Python wheels***. Который попытался решить основные проблемы присущие egg. Понятно что все решить не смог и кое что пришлось унаследовать от предшественника. К примеру бинарные 'колёса' нельзя загружать на ***PyPI*** как несовместимые с различными системами. Или проще говоря бинарные wheel’ы по сути можно использовать только в своих собственных однородных инфраструктурах. Ключевое слово однородных. А как мы знаем даже в рамках одной команды однородности мало.

Казалось бы все печально, но не так как могло показаться. Мы живем в эпоху докеров а докеры это как раз та самая однородная среда. А это значит что если мы используем docker’ы в своем девелопмент процессах то мы можем и  wheel’ы использовать в полном объеме. А это в первую очередь поможет нам ускорить сборку приложений и в некоторых случаях даже более чем в сотни раз.

## Создаем архив wheel пакетов

И так у нас есть типичный Python проект с файлом ```requirements.txt``` содержащим зависимости.

```bash
# Core
cassandra-driver==3.17.0
aiohttp==3.5.4

# Supplement
cchardet==2.1.4
aiodns==2.0.0
uvloop==0.12.2
```

Для начала нам необходимо установить пакеты. Действуем обычным способом:

```bash
$ pip install -r requirements.txt
```

Теперь нам необходимо сгенерировать архив ***wheel*** для всех наших пакетов и их зависимостей. И сложить их куда нибудь нам в проект.

```bash
$ mkdir wheels
$ pip wheel -w wheels/ -r requirements.txt 
```

Теперь когда в директории ```wheels``` есть все необходимые пакеты для установки всех зависимостей системы можем просто выполнить:

```bash
$ pip install --no-index -f wheels/ -r requirements.txt
```

В результате время установки пакетов из локального архива ускорилось на порядки.