# Утилита *tcptraceroute6*
## Общее описание
Утилита *tcptraceroute 6* отслеживает маршрут пакетов данных, взятых из IP-сети на пути к конечному хосту в сетях, где используется протокол TCP/IPv6.


## Назначение 
Утилита *tcptraceroute 6* предназначена для определения маршрутов следования данных в сетях TCP/IPv6, что позволяет администраторам оперативно решать проблемы с подключением к сети.

## Принцип работы
Утилита *tcptraceroute 6* отправлет пакеты данных (по умолчанию - TCP, а также может отправлять пакеты ICMPv6 Echo Request) с увеличением их прыжка пока не будет достигнут конечный хост, одновременно пытаясь вызвать ответ ICMP TIME_EXCEEDED от каждого шлюза по пути к хосту. 

В результате работы утилиты отображается список переходов по сетевому маршруту между локальной системой и конечным хостом. 

Для корректной работы утилиты необходимо указать имя или адрес хоста, к которому должен быть определен сетевой маршрут. Дополнительно можно указать длину пакетов (для пакетов UDP и ICMP), либо номер порта назначения/имя службы (для пакетов TCP).

```
< hostname/address > [ port ]
```

Обратите внимание, что порт назначения TCP 0 на самом деле является портом TCP с номером 0, который не может использоваться через стандартный программный интерфейс TCP/IP более высокого уровня.


## Краткий список используемых параметров
- `[ -AdEnrS ]` 
- `[ -f min_hop ] `
- `[ -g hop ] `
- `[ -i iface ] `
- `[ -l packet_size ]` 
- `[ -m max_hop ]` 
- `[ -p port ]` 
- `[ -q attempts ]` 
- `[ -s source ]` 
- `[ -t tclass ]` 
- `[ -w wait ]` 
- `[ -z delay_ms ]` 



## Параметры
| Параметр           | Значение           |
|:-------------------|:-------------------|
|`-A`|Отправка пакетов данных TCP/ACK. Данный параметр эффективен для работы со stateless брандмауэрами (например, официальные версии ядра Linux до 2.4.31 включительно и 2.6.14) и не работает c statefull брандмауэрами. Обратите внимание, что TCP/ACK зондирование не может определить, открыт ли TCP-порт назначения или нет
|`-d`|Чтобы использовать данный параметр необходимо включить опцию отладки сокетов (SO_DEBUG), в противном случае параметр может не сработать. Traceroute в Linux по умолчанию отправляет пробные пакеты в формате UDP, данный параметр может преобразовать их в пакеты формата ICMP
|`-E`|Отправка ECN-setup пакетов данных TCP/SYN (согласно RFC 3168) вместо тестовых пакетов TCP/SYN без настройки ECN. Параметр не работает без одновременного применения параметра "\-S"
|`-F`|Этот параметр игнорируется в целях обратной совместимости - пакеты IPv6 никогда не фрагментируются в пути
|`-f`|Параметр переопределяет исходное ограничение количества переходов пакетов IPv6 (по умолчанию: 1)
|`-g`|Параметр добавляет сегмент маршрута IPv6 в заголовок маршрутизации IPv6. Это обеспечивает свободную маршрутизацию от источника. В настоящее время поддерживается только заголовок маршрутизации «Тип 0»
|`-h`|Отобразить справку и выйти
|`-i`|Отправка пакета только через указанный интерфейс. Дополнительно см. раздел [Возникновение ошибок](#возникновение-ошибок)
|`-l`|Указать размер (в байтах) отправляемых пакетов
|`-m`|Переопределить максимальный предел прыжков (максимальное количество прыжков). По умолчанию установлено 30 прыжков, чего должно быть достаточно в сети IPv6 в течение некоторого времени
|`-N`|Преобразовать IPv6-адрес каждого перехода в имя хоста. Это значение по умолчанию. Эта опция предназначена для обратной совместимости с tcptraceroute(8)
|`-n`|Не преобразовывать IPv6-адрес каждого перехода в имя хоста. Это может значительно ускорить трассировку
|`-p`|Указать номер исходного порта (по умолчанию: auto). Обратите внимание, что нулевой номер порта источника на самом деле означает нулевой номер, а не номер порта, который будет назначен автоматически, как в случае с обычным программным обеспечением
|`-q`|Переопределить количество зондов, отправляемых на каждый переход (по умолчанию: 3)
|`-r`|Не маршрутизировать пакеты, т.е. не отправлять пакеты через шлюз, который будет указан в таблице маршрутизации. Дополнительно см. раздел [Возникновение ошибок](#возникновение-ошибок)
|`-S`|Используйте пробные пакеты TCP/SYN - значение по умолчанию для tcptraceroute6
|`-s`|Указать адрес источника, который будет использоваться для зондрующх пакетов
|`-t`|Указать класс трафика (DSCP) для зондрующих пакетов
|`-V`|Отобразить версию программы и лицензию и выйти
|`-w`|Переопределить задержку (в секундах) для ожидания ответа после отправки данного пробного пакета (по умолчанию: 5 секунд)
|`-x`|Этот параметр игнорируется при плавном переходе с трассировки IPv4. Заголовок IPv6 не имеет поля контрольной суммы
|`-z`|Указать задержку в миллисекундах между каждым зондированием с одинаковым пределом прыжков. Это может быть полезно для обхода ограничения скорости ICMPv6 на некоторых хостах

## Диагностика проблем с подключением к сети
Если ответ получен, в ответном отчете выводится время прохождения пакетов данных туда и обратно. Следующие символы обозначают определенные ошибки:
 
|Символ| Ошибка | Расшифровка|
|:-----|:-------|:-----------|
|`*`| Нет ответа | До истечения тайм-аута не получен действительный ответ (см. параметр -w).| 
|`!N`| Нет маршрута к месту назначения |В таблице маршрутизации нет записи для сети назначения |
|`!A`| Сообщение с пунктом назначения административно запрещено| Брандмауэр отклонил трафик
|`!S`| За пределами исходного адреса |Область адреса исходного адреса слишком мала для достижения адреса назначения. На момент написания документации такая ситуация могла произойти только при использовании локального исходного адреса для достижения пункта назначения глобальной области. (Примечание: некоторые реализации трассировки IPv4 используют "!S" для ошибки исходного маршрута, что является другой ошибкой)
|`!H`| Адрес недоступен | Адрес хоста недоступен по некоторым другим причинам, в частности, из-за сбоя канального уровня (например, сбой обнаружения соседнего адреса)
|`!P`| Обнаружен неопознанный тип следующего заголовка | Пункт назначения не реализует используемый протокол уровня 4. Следует повторить попытку с помощью эхо-запросов ICMPv6 (параметр командной строки -I). который должен поддерживаться любым узлом IPv6

## Возникновение ошибок
Параметры `-i` и `-r` игнорируются официальным ядром Linux на момент написания документации, следовательно, могут работать не так, как описано в документации. 

Поддержка параметра `-t` была прекращена во всех версиях до версии ядра Linux 2.6.18.

Использование заголовка маршрутизации с параметром `-g` вызывает ошибку OOPS в ядрах Linux ниже 2.6.17.12.

Получение пакетов TCP/SYN-ACK вообще не работает во FreeBSD - это серьезно ограничивает целесообразность использования утилиты *tcptraceroute6* во FreeBSD.

Длина пакета учитывает заголовок IPv6, а также заголовки расширений, если такие присутствуют. Linux iputils Traceroute6 не включает заголовок UDP. В целом семантика длины пакета несовместима с реализацией трассировки IPv6.
