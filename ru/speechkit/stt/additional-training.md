# Расширение модели распознавания речи

{{ speechkit-name }} предоставляет два способа повышения качества распознавания речи. 

## Автотюнинг {#autotuning} 

По умолчанию {{ speechkit-name }} не сохраняет переданные пользователем данные. Однако самый эффективный способ улучшать модель распознавания речи — это обучать ее на реальных пользовательских данных. 

Чтобы повысить качество распознавания, вы можете использовать _автотюнинг_ модели. Автотюнинг позволит нам сохранять пользовательские данные и использовать их для дальнейшего обучения. Для этого в заголовках API-запросов необходимо установить флаг `x-data-logging-enabled: true`. Пример запроса с установленным флагом логирования см. в разделе [{#T}](../concepts/support-headers.md).

Автотюнинг позволяет повышать качество распознавания в процессе работы модели. 

## Дообучение модели {#advanced-training}

Основная модель распознавания речи предназначена для работы с общей лексикой, однако ее может быть недостаточно для распознавания специфичной лексики. С помощью дообучения модель можно научить распознавать доменно-специфичные термины из разных областей:

* медицина — диагнозы, биологические термины, названия лекарств;
* бизнес — названия компаний;
* торговля — номенклатура товаров (ювелирные изделия, электротехника и пр.);
* финансы — банковские термины и названия банковских продуктов.

### Данные для дообучения {#data}

Для дообучения необходимы следующие данные:

* _Глоссарий_ — полный список терминов. В глоссарии могут содержаться как слова, присутствующие на аудиозаписях для тестирования, так и другая лексика. Глоссарий должен быть предоставлен в отдельном файле, каждый термин размещается в файле на отдельной строке.
* _Текстовые шаблоны_ — однородные фразы, на основе которых модель будет синтезировать высказывания. Длина шаблона вместе с переменными не должна превышать 200 символов.

Глоссарий и текстовые шаблоны должны быть представлены в формате [TSV](https://ru.wikipedia.org/wiki/TSV) в нормализованном виде:
* Числительные — расшифрованы прописью.
* Латинские слова и символы — заменены на транскрипцию.
* Сокращения — полностью прописаны.

> ![No](../../_assets/common/no.svg) — Безвозмездно, т.е. даром, отдадим 2 кг картошки и журналы Cloud of Science за 2020 г.
> ![Yes](../../_assets/common/yes.svg) — Безвозмездно, то есть даром, отдадим два килограмма картошки и журналы Клауд оф сайенс за две тысячи двадцатый год.

Из полученных файлов будут подготовлены текстовые данные. В переменную часть шаблонов подставляются термины из глоссариев. Чтобы дообучение было эффективным, необходимо достаточное количество данных:

* Не менее 1 тысячи высказываний.
* Не менее 3-5 фраз, желательно пропорционально частоте использования термина в реальных задачах.

Например, файлы-глоссарии `first-name.tsv`, `middle-name.tsv` и `last-name.tsv` для дообучения модели колл-центра могут содержать имена, отчества и фамилии клиентов. 

| first-name.tsv | middle-name.tsv | last-name.tsv |
|---|---|---|
|  Никита<br>Кирилл<br>Павел<br>... <br> |  Александрович<br>Петрович<br>Казимирович<br>... <br> | Романов<br>Алексеев<br>Кукушкин<br>... <br> | 
  
Если фразы-шаблоны предполагают, что термины из глоссария могут склоняться, для каждой формы нужно создать отдельный файл-глоссарий. Например, файлы с именами в творительном падеже будут содержать записи:

| first-name-ablative.tsv | middle-name-ablative.tsv | last-name-ablative.tsv |
|---|---|---|
|  Никитой<br>Кириллом<br>Павлом<br>... <br> |  Александровичем<br>Петровичем<br>Казимировичем<br>... <br> | Романовым<br>Алексеевым<br>Кукушкиным<br>... <br> |

Тогда файл с шаблонами `templates.tsv` будет состоять из записей вида

```
Добрый день, вы {first-name=first-names.tsv}{middle-name=middle-names.tsv}{last-name=last-names.tsv}?
Здравствуйте, я могу поговорить с {first-name=first-names-ablative.tsv}{middle-name=middle-names-ablative.tsv}?
```

### Тестирование качества дообучения {#testing}

Для тестирования обученной модели используются следующие наборы данных: 
1. Корзина для оценки конкретной задачи, сформированная на основе полученных аудиозаписей.
1. Корзина для оценки общей лексики.
1. (Опционально) Аудиозаписи длительностью не менее 1 часа для оценки качества дообучения модели. Структура записанных высказываний должна повторять предоставленные шаблоны.

Оценка качества распознавания речи выполняется на основе метрики [WER](https://en.wikipedia.org/wiki/Word_error_rate) (Word Error Rate). Чем меньше полученная метрика, тем точнее распознан фрагмент речи. Дообучение считается успешным, если качество распознавания специфичной лексики значительно улучшилось, и при этом качество распознавания общей лексики также улучшилось или не изменилось. Самостоятельно оценить качество распознавания речи можно в [{{ ml-platform-full-name }}](../../datasphere/tutorials/speech-recognition.md).

Если новая версия модели после дообучения удовлетворяет требованиям метрик оценки качества, она будет подготовлена к релизу в статусе `general:rc`. 

### Сроки готовности модели

Изменения поступают в модель `general:rc` в течение 4 недель по стандартному циклу подготовки релиза.