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
[Объект Task](#Объект-Task)  
&emsp; [DoAsync(com, method)](#DoAsynccom-method)  
&emsp; [DoAsync1(com, method, val1)](#DoAsync1com-method-val1)  
&emsp; [DoAsync2(com, method, val1, val2)](#DoAsync2com-method-val1-val2)  
&emsp; [DoAsync3(com, method, val1, val2, val3)](#DoAsync3com-method-val1-val2-val3)  
&emsp; [DoAsyncN(com, method, @vals)](#DoAsyncNcom-method-vals)  
&emsp; [WaitTask()](#WaitTask)  
&emsp; [CloseTask()](#CloseTask)  
&emsp; [Пример использования асинхронной задачи на языке Visual FoxPro](#Пример-использования-асинхронной-задачи-на-языке-Visual-FoxPro)  
[СloseAll()](#СloseAll)  

[История версий](#История-версий)  
### Назначение
Библиотека memlib.net.dll реализует COM-сервер для VFP9 или VFPA, который в принципе может использоваться и в любых других языках, поддерживающих COM технологию обмена данными.  

Microsoft VFP имеет ряд ограничений, связанных с использованием ОП. Теоретически в VFP cуществуют способы использовать ОП до 2 Гб, но при этом возникают очень большие утечки памяти, приводящие к блокировке работы VFP.  

При создании COM-объекта VFP.memlib, выделяемая ОП не занимает место в области VFP. Эта память выделяется в объекте VFP.memlib.  

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
## Объект Task
Объект Task представляеет собой асинхронную задачу. Задача создается на основе метода открытого COM-объекта, такого как Excel.application, com.sun.star.frame.Desktop, VisualFoxPro.Application, ADODB.Connection и т.д. Метод представлятся строкой. Верхний или нижний регистр символов строки с методом имеют значение. Необходимо указывать точное название метода. Объект Task имеет ниже следующие методы.
### DoAsync(com, method)
Метод запускает асинхронную задачу без параметров, возвращает локальный id задачи или -1 в случае если задача не создана. Первым параметром передается открытый COM-объект, вторым параметром — текстовое наименование метода.
### DoAsync1(com, method, val1)
Метод запускает асинхронную задачу с одним требуемым параметром val1, возвращает локальный id задачи или -1 в случае если задача не создана. Первым параметром передается открытый COM-объект, вторым параметром — текстовое наименование метода, третьим — значение параметра, передаваемое указанному методу.
### DoAsync2(com, method, val1, val2)
Метод запускает асинхронную задачу с двумя требуемыми параметрами val1 и val2, возвращает локальный id задачи или -1 в случае если задача не создана. Первым параметром передается открытый COM-объект, вторым параметром — текстовое наименование метода, третьим и четвертым — значения передаваемые указанному методу.
### DoAsync3(com, method, val1, val2, val3)
Метод запускает асинхронную задачу с тремя требуемыми параметрами val1, val2 и val3, возвращает локальный id задачи или -1 в случае если задача не создана. Первым параметром передается открытый COM-объект, вторым параметром — текстовое наименование метода. Третьим, четвертым и пятым передаются значения  параметров для указанного метода.
### DoAsyncN(com, method, @vals)
Метод запускает асинхронную задачу со специально подготовленным массивом требуемых параметров, возвращает локальный id задачи или -1 в случае если задача не создана. Первым параметром передается открытый COM-объект, вторым параметром — текстовое наименование метода. Нумерация элементов массива с параметрами начинается от нуля. Массив передается по ссылке. Смотрите ниже приведенный пример на языке Visual FoxPro.
### WaitTask()
Если требуется, метод ожидает завершения задачи. Метод возвращает сформированный результат.
### CloseTask()
Метод освобождает сформированные в объекте VFP.memlib ресурсы задачи.
### Пример использования асинхронной задачи на языке Visual FoxPro
```
oMem=CreateO('VFP.memlib')
oVFP=CreateO('VisualFoxPro.Application')

* Эмитируем работу, выполняемую в течении 123.4 секунд в паралельном процессе.
* Метод DoCmd имеет один параметр - выполняемую команду:
oMem.DoAsync1(oVFP,"DoCmd","wait wind '' time 123.4")

* Тем временем вычисляем и возвращаем сумму чисел в текущем процессе.
? 2+2*2                 &&   6

* Формируем массив параметров размером в 1 элемент:
dime vals(1)
vals(1)="m.A+m.A*m.A"   && выражение

* Указываем тип массива с нулевого элемента для COM-объекта oMem:
ComArray(oMem,10)

* Завершаем запущенную асинхронно задачу:
oMem.WaitTask()

* Заносим значение в переменную m.A синхронно с помощью метода SetVar.
* Метод SetVar требует два параметра:
oVFP.SetVar("A",2)

* Выполняем вычисление асинхронно с использованием массива параметров:
oMem.DoAsyncN(oVFP,"Eval", @vals)

* Получаем результат вычисления асинхронной задачи:
? oMem.WaitTask()       &&   6

* Закрываем процесс VFP синхронно. Метод Quit не имеет параметров:
oVFP.Quit()

oMem.CloseTask()
rele oVFP
```  
### СloseAll()
Метод закрывает все объекты COM-сервера и максимально освобождает всю память.

### История версий
0.0.0.0. 08.03.2025. Опубликована первая рабочая версия с объектами Stream, Array и Dictionary.  
0.0.1.0. 09.03.2025. Добавлен метод CloseAll().  
0.0.2.0. 09.03.2025. Добавлена автоматическая очистка потока записи после формирования потока чтения.  
0.0.2.1. 26.03.2025. Добавлен объект FIFO и объект асинхронной задачи.  
0.0.2.2. 28.03.2025. Объект Task переведен из памяти вызывающей задачи в память VFP.memlib.  
