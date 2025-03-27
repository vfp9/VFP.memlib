# VFP.memlib — memlib.net.dll
### Оглавление
[Назначение](#Назначение)  
[Регистрация COM-сервера в реестре Windows](#Регистрация-COM-сервера-в-реестре-Windows)  
[Создание объекта VFP.memlib](#Создание-объекта-VFPmemlib)  
[Объект Stream](#Объект-Stream)  
&emsp; [Write(str)](#Writestr)  
&emsp; [LenStream()](#LenStream)  
&emsp; [Asc()](#Asc)  
&emsp; [Read(count)](#Readcount)  
&emsp; [ReadLine()](#ReadLine)  
&emsp; [ReadToEnd()](#ReadToEnd)  
&emsp; [CloseStream()](#CloseStream)  
[Объект Array](#Объект-Array)  
&emsp; [NewArray(n)](#newArrayn)  
&emsp; [LenArray()](#LenArray)  
&emsp; [PutArray(i, val)](#PutArrayi-val)  
&emsp; [GetArray(i)](#GetArrayi)  
&emsp; [CloseArray()](#CloseArray)  
[Объект Dictionary](#Объект-Dictionary)  
&emsp; [LenDic()](#LenDic)  
&emsp; [PutDic(strKey, val)](#PutDicstrKey-val)  
&emsp; [GetDic(strKey)](#GetDicstrKey)  
&emsp; [DelDic(strKey)](#DelDicstrKey)  
&emsp; [СloseDic()](#СloseDic)  
[Объект Queue/FIFO](#Объект-QueueFIFO)  
&emsp; [LenFIFO()](#LenFIFO)  
&emsp; [PutFIFO(val)](#PutFIFOval)  
&emsp; [PeekFIFO()](#GetFIFO)  
&emsp; [GetFIFO()](#GetFIFO)  
&emsp; [СloseFIFO()](#СloseFIFO)  
[СloseAll()](#СloseAll)  
[Объект Task](#Объект-Task)  
&emsp; [DoAsync(com, method)](#DoAsynccom-method)  
&emsp; [DoAsync1(com, method, val1)](#DoAsync1com-method-val1)  
&emsp; [DoAsync2(com, method, val1, val2)](#DoAsync2com-method-val1-val2)  
&emsp; [DoAsync3(com, method, val1, val2, val3)](#DoAsync3com-method-val1-val2-val3)  
&emsp; [DoAsyncN(com, method, @vals)](#DoAsyncNcom-method-vals)  
&emsp; [WaitTask(task)](#WaitTasktask)  
&emsp; [CloseTask(task)](#CloseTasktask)  
[Пример использования асинхронной задачи на языке Visual FoxPro для распараллеливания расчета](#Пример-использования-асинхронной-задачи-на-языке-Visual-FoxPro-для-распараллеливания-расчета)  

[История версий](#История-версий)  
### Назначение
Библиотека memlib.net.dll реализует COM-сервер для VFP9 или VFPA, который в принципе может использоваться и в любых других языках, поддерживающих COM технологию обмена данными.  

Microsoft VFP имеет ряд ограничений, связанных с использованием ОП. Теоретически в VFP cуществуют способы использовать ОП до 2 Гб, но при этом возникают очень большие утечки памяти, приводящие к блокировке работы VFP.  

При создании COM-объекта VFP.memlib, выделяемая ОП не занимает место в области VFP. Эта память выделяется на объекте VFP.memlib.  

В memlib.net.dll реализованы:
- объект Stream, который по используемым методам напоминает работу с файлом, находящимся в ОП;
- объект Array, представляющий одномерный массив с числовым индексом;
- объект Dictionary, представляющий одномерный массив с текстовым индексом (словарь);
- объект Queue/FIFO, очередь, в которцю помещаются любые переменные и объекты;
- объект Task — асинхронная задача, создаваемыя из метода любого COM-объекта.  
### Регистрация COM-сервера в реестре Windows
Чтобы объект VFP.memlib был доступен в разрабатываемых программах, его нужно зарегистрировать в ОС с помощью утилиты regasm.exe. Например:
```
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\regasm.exe D:\VFP\VFPA\memlib.net.dll /codebase
```
Предварительно поместите файл memlib.net.dll в удобную для вас папку, например, в папку, где находятся другие библиотеки Microsoft VFP.  
Для удаления регистрации из реестра Windows используйте ключ /unregister. Например:
```
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\regasm.exe D:\VFP\VFPA\memlib.net.dll /unregister
```
Для выполнения вышеуказанных команд требуются права администратора.
## Создание объекта VFP.memlib
Текст кода на VFP:
```
oMem = CreateObject('VFP.memlib')
```
## Объект Stream
Объект Stream имеет ниже следующие методы.
### Write(str)
Метод записывает указанную параметром строку в объект Stream.
```
oMem.Write("Привет, Мир!"+chr(10))
```
Указатель записи всегда находится в конце записанного потока. При первом же запросе любой операции на чтение, запись в этот же поток становится невозможной. В этом случае следующая операция записи откроет новый поток.
### LenStream()
Метод возвращает 32-х разрядное положительное число, содержащее длину потока или ноль, если запись в объект еще не производилась.
```
len = oMem.LenStream()
```
### Asc()
Метод читает код первого за указателем чтения символа, но не перемещает указаль.
```
kod = oMem.Asc()
```
Если поток пустой или достигнут конец потока, то метод возвращает значение -1.
### Read(count)
Метод читает заданное параметром количество символов из Stream и возвращает прочитанную строку. Указатель чтения переносится за последний прочитанный символ.
```
str = oMem.Read(10)
```
Если начальная позиция указателя находилась в коце потока, то вместо строки возвращается значение NULL.
### ReadLine()
Метод читает из Stream строку до символа перевода строки. Символы CR и LF не включаются в возвращаемое методом значение. Указатель чтения переносится за последний прочитанный символ включая CR и LF.
```
str = oMem.ReadLine()
```
Если начальная позиция указателя находилась в коце потока, то вместо строки возвращается значение NULL.
### ReadToEnd()
Метод читает из Stream строку до конца потока. Указатель чтения переносится в конец.
```
str = oMem.ReadToEnd()
```
Если начальная позиция указателя находилась в коце потока, то вместо строки возвращается значение NULL.
### CloseStream()
Метод удаляет объект Stream из памяти. Память освобождается. Используйте этот метод, если вам больше не нужен объект Stream. Объект Stream создается при первом использовании метода Write(str).
```
oMem.CloseStream()
```
## Объект Array
Объект Array имеет ниже следующие методы.
### NewArray(n)
Метод создает массив размером, передаваемым числовым параметром. В случае успешного создания массива метод возвращает число созданных элементов массива n. В противном случае возвращается ноль.
### LenArray()
Метод возвращает длинну массива.
### PutArray(i, val)
Метод присваивает элементу массива под номером первого числового параметра значение произвольного типа, переданное вторым параметром.
### GetArray(i)
Метод возвращает значение элемента массива с номером переданным числовым параметром.
### CloseArray()
Метод уничтожает массив.
## Объект Dictionary
Объект Dictionary имеет ниже следующие методы.
### LenDic()
Метод возвращает число пар ключ-значение.
### PutDic(strKey, val)
Метод присваивает элементу словаря со строковым ключем, заданным первым параметром, значение произвольного типа, переданное вторым параметром.
### GetDic(strKey)
Метод возвращает значение элемента словаря со строковым ключем, заданным параметром.
### DelDic(strKey)
Метод удаляет элемент словаря ключ-значение, заданное строковым параметром, содержащим значение ключа.
### СloseDic()
Метод уничтожает словарь и освобождает память.
## Объект Queue/FIFO
Объект FIFO представляет собой очередь значений помещаемых и извлекаемых по принципу первый помещен, первый извлечен. Объект имеет ниже следующие методы.
### LenFIFO()
Метод возвращает число значений в очереди.
### PutFIFO(val)
Метод помещает значение произвольного типа в очередь.
### PeekFIFO()
Метод возвращает первое значение в очереди, но не извлекает его.
### GetFIFO()
Метод возвращает первое значение в очереди и извлекает его.
### СloseFIFO()
Метод уничтожает очередь и освобождает память.
### СloseAll()
Метод закрывает все объекты COM-сервера и максимально освобождает всю память кроме ресурсов, занятых асинхронными задачами. Завершайте запущенные асинхронные задачи соответствующими методами.
## Объект Task
Объект Task представляеет собой асинхронную задачу, ссылка на которую не хранится в объекте VFP.memlib. Поэтому в общем случае таких задач может быть не одна. Все ссылки на задачу/задачи передаются вызывающей программе. Задача создается на основе метода открытого COM-объекта, такого как Excel.application, com.sun.star.frame.Desktop, VisualFoxPro.Application, ADODB.Connection и т.д. Метод представлятся строкой. Верхний или нижний регистр символов строки с методом имеют значение. Необходимо указывать точное название метода. Объект Task имеет ниже следующие методы.
### DoAsync(com, method)
Метод запускает асинхронную задачу без параметров и возвращает объект типа Task. Первым параметром передается открытый COM-обект, вторым параметром — текстовое наименование метода.
### DoAsync1(com, method, val1)
Метод запускает асинхронную задачу с одним требуемым параметром val1 и возвращает объект типа Task. Первым параметром передается открытый COM-обект, вторым параметром — текстовое наименование метода, третьим — значение параметра, передаваемое указанному методу.
### DoAsync2(com, method, val1, val2)
Метод запускает асинхронную задачу с двумя требуемыми параметрами val1 и val2 и возвращает объект типа Task. Первым параметром передается открытый COM-обект, вторым параметром — текстовое наименование метода, третьим и четвертым — значения передаваемые указанному методу.
### DoAsync3(com, method, val1, val2, val3)
Метод запускает асинхронную задачу с тремя требуемыми параметрами val1, val2 и val3 и возвращает объект типа Task. Первым параметром передается открытый COM-обект, вторым параметром — текстовое наименование метода. Третьим, четвертым и пятым передаются значения  параметров для указанного метода.
### DoAsyncN(com, method, @vals)
Метод запускает асинхронную задачу со специально подготовленным массивом требуемых параметров и возвращает объект типа Task. Первым параметром передается открытый COM-обект, вторым параметром — текстовое наименование метода. Нумерация элементов массива с параметрами начинается от нуля. Массив передается по ссылке. Смотрите ниже приведенный пример на языке Visual FoxPro.
### WaitTask(task)
Метод ожидает, если требуется, завершения задачи, указанной параметром, содержащим объект типа Task и возвращает сформированный результат.
### CloseTask(task)
Метод освобождает сформированные в объекте VFP.memlib ресурсы задачи, указанной параметром, содержащим объект типа Task.
### Пример использования асинхронной задачи на языке Visual FoxPro для распараллеливания расчета
```
oMem=CreateO('VFP.memlib')
oVFP1=CreateO('VisualFoxPro.Application')
oVFP2=CreateO('VisualFoxPro.Application')

* Эмитируем работу, выполняемую в течении 55.2 секунд в паралельном процессе.
* Метод DoCmd имеет один параметр - выполняемую команду:
oT1=oMem.doAsync1(oVFP1,"DoCmd","wait wind '' time 55.2")

* Тем временем вычисляем и возвращаем сумму чисел в другом процессе.
* Метод Eval имеет один параметр - выражение:
oT2=oMem.doAsync1(oVFP2,"Eval","2+2*2")
? oMem.WaitTask(oT2)    &&   6

* Закрываем второй процесс VFP. Метод Quit не имеет параметров:
oMem.doAsync(oVFP2,"Quit")
? oMem.WaitTask(oT1)    && .NULL. -- задача не возвращает значение

* Используем вариант обращения с массивом параметров размером в 1 элемент.
* Указываем тип массива с нулевого элемента для COM-объекта oVFP1:
ComArray(oVFP1,10)
dime vals(1)
vals(1)="m.A+m.A*m.A"   && выражение

* Заносим значение в переменную m.A синхронно с помощью метода SetVar.
* Метод SetVar требует два параметра:
oVFP1.SetVar("A",2)

* Выполняем вычисление асинхронно с использованием массива параметров:
oT1=oMem.doAsyncN(oVFP1,"Eval", @vals)
? oMem.WaitTask(oT1)    &&   6

* Закрываем первый процесс VFP синхронно. Метод Quit не имеет параметров:
oVFP1.Quit()

oMem.CloseTask(oT1)
oMem.CloseTask(oT2)
rele oT1,oT2,oVFP1,oVFP2
```  

### История версий
0.0.0.0. 08.03.2025. Опубликована первая рабочая версия с объектами Stream, Array и Dictionary.  
0.0.1.0. 09.03.2025. Добавлен метод CloseAll().  
0.0.2.0. 09.03.2025. Добавлена автоматическая очистка потока записи после формирования потока чтения.  
0.0.2.1. 26.03.2025. Добавлен объект FIFO и объект асинхронной задачи.  
