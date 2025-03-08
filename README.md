# VFP.memlib — memlib.dll
### Назначение
Библиотека memlib.dll реализует COM-сервер для VFP9 или VFPA, ктоторый в принципе может использоваться и в любых других языках, поддерживающих COM технологию обмена данными.  

VFP имеет ряд ограничений, связанных с использованием ОП. Теоретически в VFP cуществуют способы использовать ОП до 2 Гб, но при этом возникают очень большие утечки памяти, приводящие к блокировке работы VFP.  

При создании COM-объекта VFP.memlib, выделяемая ОП не занимает место в области VFP. Эта память выделяется на объекте memlib.  

В первой версии memlib.dll реализован объект Stream, который по используемым методам напоминает работу с файлом, находящимся в ОП. На ближайшую перспективу на базе memlib.dll планируется добавить массивы с простыми индексами, массивы с именованными индексами, возможно будут добавлены и другие вспомогательные элементы, если в этом будет нуждаться сообщество программистов VFP.  

