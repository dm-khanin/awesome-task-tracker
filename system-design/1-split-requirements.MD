### Разбор требований таск-трекера на составляющие

- Таск-трекер должен быть отдельным дашбордом и доступен всем сотрудникам компании UberPopug Inc.
```
Actor – Account
Query – Get tasks
```

- Авторизация в таск-трекере должна выполняться через общий сервис авторизации UberPopug Inc
```
Actor – Account
Command – Login to task tracker
Data – форма клюва
Event – Account.Logined
```

- В таск-трекере должны быть только задачи. Проектов, скоупов и спринтов нет, потому что они не умещаются в голове попуга.
```
Actor – Account
Query – Get task
Data – task id
```

- Новые таски может создавать кто угодно (администратор, начальник, разработчик, менеджер и любая другая роль). У задачи должны быть описание, статус (выполнена или нет) и попуг, на которого заассайнена задача.
```
Actor – Account with any role
Command – Add task
Data – Task + assigned account id
Event – Task.Created, Task.Assigned
```

- Менеджеры или администраторы должны иметь кнопку «заассайнить задачи», которая возьмёт все открытые задачи и рандомно заассайнит каждую на любого из сотрудников (кроме менеджера и администратора) . Не успел закрыть задачу до реассайна — сорян, делай следующую.
```
Actor – Account with “admin” role or Account with “manager” role
Command – Assign All Tasks
Data – Task.ids + Account.ids
Event – Tasks.Assigned (Tasks.Reassigned)
```

- Каждый сотрудник должен иметь возможность видеть в отдельном месте список заассайненных на него задач + отметить задачу выполненной.
```
Actor – Account
Query – Get assigned to account tasks
Data – account id
```

```
Actor – Account
Command – Change task status
Data – Task + account id
Event – Task.StatusChanged
```


### Разбор требований аккаунтинга

- Аккаунтинг должен быть в отдельном дашборде и доступным только для администраторов и бухгалтеров.
```
Actor – Account with “admin” or “accountant” role
Query – Get any account balance with audit log
Data – account id, balance id
```

```
Actor – Account with “employer” role
Query – Get account’s account balance with audit log
Data – account id, balance id
```

```
Actor – Account with “admin” or “accountant” role
Query – Get earned money amount for any date
Data – date
```

- Авторизация в дашборде аккаунтинга должна выполняться через общий сервис аутентификации UberPopug Inc.
```
Actor – Account
Command – Login to accounting dashboard
Data – форма клюва
Event – Account.Logined
```

- У каждого из сотрудников должен быть свой счёт, который показывает, сколько за сегодня он получил денег. У счёта должен быть аудитлог того, за что были списаны или начислены деньги, с подробным описанием каждой из задач.
```
Actor – Account with “employer” role
Query – Get account’s earned money for today
Data – account id, balance id, date
```

```
Actor – Event “Task.StatusChanged”
Command – Create audit record
Data – Task, account id, money cost / reward amount
Event – AuditRecord.Created, AccountBalance.Updated
```

- цены на задачу определяется единоразово, в момент появления в системе (можно с минимальной задержкой). цены рассчитываются без привязки к сотруднику
```
Actor – Event “Task.Created”
Command – Update task cost
Data – Task id, cost
Event – Task.Updated
```

```
Actor – Event “Task.Created”
Command – Update task reward
Data – Task id, reward
Event – Task.Updated
```

- деньги списываются сразу после ассайна на сотрудника, а начисляются после выполнения задачи.
```
Actor – Event “Task.Assigned” (Task.Reassigned)
Command – Charge task cost
Data – Balance id, task id, cost
Event – Balance.Updated
```

```
Actor – Event “Task.StatusChanged”
Command – Accrue task reward
Data – Balance.id, task id, reward
Event – Balance.Updated
```

- Дашборд должен выводить количество заработанных топ-менеджментом за сегодня денег.
```
Actor – Account with “admin” or “accountant” role
Query – Get earned money for today
Data – date
```

- В конце дня необходимо:
a) считать сколько денег сотрудник получил за рабочий день
b) отправлять на почту сумму выплаты.
```
Actor – Everyday cron job
Command – Calculate earned money for today
Data – Balance id, date
Event – Balance.Calculated
```

```
Actor – Event “Balance.Calculated”
Command – Send email
Data – Account email, earned money
Event – Account.Notified
```

- После выплаты баланса (в конце дня) он должен обнуляться, и в аудитлоге всех операций аккаунтинга должно быть отображено, что была выплачена сумма.
```
Actor – Event “Balance.Calculated”
Command – Pay earned money
Data – Balance id, money amount
Event – Salary.Payed
```

```
Actor – Event “Salary.Payed”
Command – Update balance
Data – Balance id, money amount
Event – Balance.Updated
```

```
Actor – Event “Salary.Payed”
Command – Create audit record
Data – Balance id, payed money amount
Event – AuditRecord.Created
```

### Разбор требований аналитики
- Аналитика — это отдельный дашборд, доступный только админам. Нужно указывать, сколько заработал топ-менеджмент за сегодня и сколько попугов ушло в минус.
```
Actor – Account with “admin” role
Query – Get earned money for today
Data – date
```

```
Actor – Account with “admin” role
Query – Get accounts with negative balance
Data – date
```

- Нужно показывать самую дорогую задачу за день, неделю или месяц. самой дорогой задачей является задача с наивысшей ценой из списка всех закрытых задач за определенный период времени
```
Actor – Account with “admin” role
Query – Get the most expensive closed task for period
Data – date
```
