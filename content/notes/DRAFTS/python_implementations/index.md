---
title: "Реализации Python"
date: 2024-07-12 00:00:01
type: posts
tags:
  - python
categories:
  - python
draft: true
---

Основные реализации интерпретаторов Python, дополнительные инструменты.
<!--more-->
## <center>Python</center>
**Python** — язык программирования, т.е. набор синтаксических правил + транслятор. Реализации Python представляют из себя определённый интерпретатор, который нужен для тех или иных целей, например, для связи с другими ЯП или для ускорения выполнения/компиляции кода. В этом конспекте я хотел бы разобрать основные интерпретаторы и языки программирования, относящиеся к Python.

## <center>CPython</center>
Если вы впервые слышите о разных интерпретаторах Python, то, вероятнее всего, работаете именно на **CPython**. Это стандартная реализация (которую зачастую называют просто "Python"), написанная на языке *ANSI C*. Является более полной и надёжной, чем её альтернативы, более того обновления на неё выходят в первую очередь.

## <center>Jython</center>
Jython является одной из самых популярных альтернативных реализаций Python, направленная на интеграцию с ЯП Java. Она генерирует байт-код Java для запуска на JVM (java virtual machine) и включает поддержку интеграции, которая разрешает коду Python использовать классы Java, будто они написаны на Python, а коду Java — выполнять код Python в качестве кода на встроенном языке. Таким образом, с помощью Jython мы сможем перенести сценарии Python в программы, написанные на Java.  

Тем не менее, система Jython более медленная и менее надёжная, чем золотой стандарт CPython, поэтому она используется в основном некоторыми разработчиками на Java.

## <center>IronPython</center>
IronPython является очередной популярной реализацией Python, цель которой — интеграция с приложениями 