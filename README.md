# VFP.memlib — memlib.dll
Библиотека memlib.dll реализует COM-сервер для VFP9 или VFPA, ктоторый в принципе может использоваться и в любых других языках, поддерживающих COM технологию обмена данными.  

Microsoft VFP имеет ряд ограничений, связанных с использованием ОП. Теоретически в VFP cуществуют способы использовать ОП до 2 Гб, но при этом возникают очень большие утечки памяти, приводящие к блокировке работы VFP.  

При создании COM-объекта VFP.memlib, выделяемая ОП не занимает место в области VFP. Эта память выделяется на объекте memlib.  

В первой версии memlib.dll реализован объект Stream, который по используемым методам напоминает работу с файлом, находящимся в ОП. На ближайшую перспективу на COM-сервер планируется добавить объекты массивов с простыми и с именованными индексами. Возможно будут добавлены и другие вспомогательные элементы, если в этом будет нуждаться сообщество программистов на VFP.  
## Создание объекта VFP.memlib
Текст кода на VFP:
```
oMem = CreateObject('VFP.memlib')
```
## Объект Stream
### Write(string)
Метод записывает указанную параметром строку в объект Stream.
```
oMem.write('Привет, Мир!'+chr(10))
```
### lenStream()
Метод подсчитывает количество записанных в объект Stream байт и возвращает 32-х разрядное положительное число или ноль, если запись в объект еще не производилась.
```
len = oMem.lenStream()
```
