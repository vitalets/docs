# Внутренний сетевой балансировщик нагрузки

Функция находится на [стадии Preview](../../overview/concepts/launch-stages.md).

Внутренний сетевой балансировщик нагрузки используется для балансировки трафика между ресурсами, подключенными:

* к подсетям VPC;
* через VPN;
* через услугу [Cloud Interconnect](../../vpc/interconnect/index.md).

Механизм балансировки трафика и принцип использования у внутреннего сетевого балансировщика такие же, как и у внешнего: трафик распределяется по ресурсам из [целевых групп](target-resources.md), подключенных к балансировщику. 

[Обработчик](listener.md) внутреннего балансировщика использует [внутренний IP-адрес](../../vpc/concepts/address.md) из подсети, в которой он находится. Балансировщик можно подключать к любой подсети из любой [зоны доступности](../../overview/concepts/geo-scope.md) — фактически он будет присутствовать во всех подсетях, где есть подключенные к балансировщику целевые ресурсы. 

## Особенности использования {#notes}

При использовании внутреннего сетевого балансировщика учитывайте следующие особенности:

1. Внутренний IP-адрес назначается балансировщику случайно из диапазона адресов подсети.
1. Порты ВМ для приема трафика от балансировщика и проверок состояния становятся недоступными при подключении ВМ к внутреннему балансировщику. На ВМ будет поступать только трафик от балансировщика.
