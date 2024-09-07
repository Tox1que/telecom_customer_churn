# Выпускной проект.  

## Отток клиентов телеком-компании.  

### Описание проекта  
Оператор связи «ТелеДом» хочет бороться с оттоком клиентов. Для этого его сотрудники начнут предлагать промокоды и специальные условия всем, кто планирует отказаться от услуг связи. Чтобы заранее находить таких пользователей, «ТелеДому» нужна модель, которая будет предсказывать, разорвёт ли абонент договор. Команда оператора собрала персональные данные о некоторых клиентах, информацию об их тарифах и услугах. Наша задача — обучить на этих данных модель для прогноза оттока клиентов. Критерием качества модели выступает метрика `ROC-AUC`, значение которой должно быть не менее 0.85.  
Информация о договорах актуальна на 1 февраля 2020.  

### Описание данных  

**Таблица telecom.contract**  
• `customer_id` — ID абонента;  
• `begin_date` — дата начала действия договора;  
• `end_date` — дата окончания действия договора;  
• `type` — тип оплаты: раз в год-два или ежемесячно;  
• `paperless_billing` — электронный расчётный лист;  
• `payment_method` — тип платежа;  
• `monthly_charges` — расходы за месяц;  
• `total_charges` — общие расходы абонента.  

**Таблица telecom.personal**  
• `customer_id` — ID пользователя;  
• `gender` — пол;  
• `senior_citizen` — является ли абонент пенсионером;  
• `partner` — есть ли у абонента супруг или супруга;  
• `dependents` — есть ли у абонента дети.  

**Таблица telecom.internet**  
• `customer_id` — ID пользователя;  
• `internet_service` — тип подключения;  
• `online_security` — блокировка опасных сайтов;  
• `online_backup` — облачное хранилище файлов для резервного копирования данных;  
• `device_protection` — антивирус;  
• `tech_support` — выделенная линия технической поддержки;  
• `streaming_tv` — стриминговое телевидение;  
• `streaming_movies` — каталог фильмов.  

**Таблица telecom.phone**  
• `customer_id` — ID пользователя;  
• `multiple_lines` — подключение телефона к нескольким линиям одновременно.  


### Первичное исследование таблиц:  
- количество таблиц соответствует условию задачи;  
- все таблицы имеют набор данных;  
- в столбцах `end_date` и `total_charges` содержаться пропуски;  
- явные дубликаты отсутствуют.

### Исследовательский анализ и предобработка:  
- загруженные таблицы объединены в общий датасет;  
- отсутствующие значения в столбцах с информацией об интернет-услугах и телефонии заполнены
значением `Not Used` 

- пропуски в колонке `total_charges` удалены;  
-  столбец `gender` был удалён, т.к. нет оснований утверждать, что между классами признака существуют статистически значимые различия;

- признак `end_date` выделен в целевую переменную;
- переменные, содержащие даты, удалены;  

- выполнен анализ признаков с точки зрения целевой переменной;

- проведён корреляционный анализ: признаки, относящиеся к интернет-услугам, сильно взаимосвязаны;  
- в итоговую таблицу добавлены новые признаки:  
    - `duration_day` - длительность контракта абонента;  
    - `additional` – количество дополнительных интернет-опций;  
    - `charges_difference` – разница между общими расходами, делёнными на количество месяцев, и ежемесячными расходами;  
- созданы классы для генерации признаков путём кластеризации, группировки, а также функция, создающая новые переменные с помощью библиотеки `Featuretools`.

### Обучение и тестирование модели:  
- для устранения мультиколлинеарности принято решение использовать метод главных компонент;  
- выполнен подбор гиперпараметров для трёх моделей: нейронной сети, градиентного бустинга и логистической регрессии; 
- на кросс-валидации лучший результат продемонстрировала модель логистической регрессии;
- значение метрики `ROC-AUC` на тестовой выборке: 0.852.  

Признаки `internet_service` и `monthly_chrages` являются одними из ключевых для модели. Причём размер ежемесячных расходов сильно зависит от типа подключения. На этапе исследовательского анализа мы выяснили, что пользователи с оптоволоконным типом подключения имеют высокий риск оттока. Вероятно, компании стоит
проанализировать, насколько стоимость интернет-услуг, предоставляемых по оптоволоконной сети, соответствует их качеству.  