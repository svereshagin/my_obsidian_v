
**DI: Dependency Injection**  
   Внедрение зависимостей вместо их создания внутри класса
```python
# Без DI
class UserService:
    def __init__(self):
        self.db = MySQLDatabase()  # Жесткая зависимость

# С DI
class UserService:
    def __init__(self, database):  # Зависимость внедряется извне
        self.db = database

# Использование:
db = PostgreSQLDatabase()
service = UserService(db)  # Легко менять реализации
```
