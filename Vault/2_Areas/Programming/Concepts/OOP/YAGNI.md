3. **YAGNI: You Ain't Gonna Need It**  
   Не добавляйте функциональность "на будущее"
```python
# Плохо: Ненужная абстракция "на вырост"
class User:
    def __init__(self, name):
        self.name = name
        self.orders = []  # Пока не используется
        self.payment_history = []  # Пока не используется

# Хорошо (YAGNI)
class User:
    def __init__(self, name):
        self.name = name
```
