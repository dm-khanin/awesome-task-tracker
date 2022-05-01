#### Синхронные коммуникации в системе:
- процесс авторизации (Login to task tracker, accounting dashboard or analytics dashboard)
- бизнес-команды и бизнес-запросы Task сервиса (Get any account tasks, Get account assigned tasks, Add task, Change task complete status)
- бизнес-запросы Balance сервиса (Get any account balance with audit log, Get account balance with audit log, Get account earned money for today, Get earned money amount for any date, Get earned money for today)
- бизнес-запросы сервиса аналитики (Get accounts with negative balance, Get earned money for today, Get the most expensive closed task for period)

#### Асинхронные коммуникации в системе:
- CUD-события для задач: Task.Created (task id, task description, assigned account), Task.Updated (task id, status, assigned on)
- CUD-события для балансов аккаунтов: AuditRecord.Created (record id, cost/reward amount, account id), Balance.Updated (balance id, diff amount)
- BE Assign All Tasks
- BE Update task cost
- BE Update task reward
- BE Charge task cost from account’s balance
- BE Accrue task reward to account’s balance
- BE Calculate earned money for today by account
- BE Send email with earned money
- BE Pay earned money

#### Продьюсеры BE:
- Task Service
- Balance Service
- Cron Job

#### Консьюмеры BE:
- Balance Service
- Notifier

#### Продьюсеры CUD:
- Task Service
- Balance Service

#### Консьюмеры CUD:
- Balance Service
- Analytics
