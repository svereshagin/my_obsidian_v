### 🧱 Основы ООП в Python: 4 столпа, SOLID, DRY, KISS, YAGNI, DI

---
#### 🔧 **SOLID принципы** (Python примеры)

1. **S: Single Responsibility (Единственная ответственность)**  
   Класс = одна причина для изменений
```python
# Плохо: Класс отвечает за логику и сохранение
class UserManager:
    def create_user(self, name):
        # ... логика создания
        self.save_to_db(name)
    
    def save_to_db(self, user):
        print(f"Saving {user} to database")

# Хорошо: Разделение ответственности
class UserCreator:
    def create_user(self, name):
        return User(name)

class UserSaver:
    def save_to_db(self, user):
        print(f"Saving {user} to database")
```

2. **O: Open/Closed (Открытость/закрытость)**  
   Классы открыты для расширения, закрыты для модификации
```python
class Discount:
    def __init__(self, customer_type):
        self.customer_type = customer_type
    
    def get_discount(self):
        if self.customer_type == "regular":
            return 0.1
        # Добавление нового типа требует изменения метода -> нарушение OCP

# Реализация через абстракцию
class DiscountStrategy(ABC):
    @abstractmethod
    def get_discount(self):
        pass

class RegularDiscount(DiscountStrategy):
    def get_discount(self):
        return 0.1

class PremiumDiscount(DiscountStrategy):
    def get_discount(self):
        return 0.2

# Новые типы добавляются без изменения существующего кода
```

3. **L: Liskov Substitution (Подстановки Лисков)**  
   Подклассы должны заменять базовые классы
```python
class Bird:
    def fly(self):
        print("Flying")

class Duck(Bird):
    pass

class Ostrich(Bird):  # Нарушение LSP!
    def fly(self):
        raise Exception("Can't fly!")

def make_bird_fly(bird: Bird):
    bird.fly()

make_bird_fly(Duck())  # OK
make_bird_fly(Ostrich())  # Ошибка! Нарушение контракта
```

4. **I: Interface Segregation (Разделение интерфейсов)**  
   Много специализированных интерфейсов > один универсальный
```python
# Плохо: Один "толстый" интерфейс
class Worker(ABC):
    @abstractmethod
    def work(self): pass
    @abstractmethod
    def eat(self): pass

# Хорошо: Разделение
class Workable(ABC):
    @abstractmethod
    def work(self): pass

class Eatable(ABC):
    @abstractmethod
    def eat(self): pass

class Robot(Workable):  # Роботу не нужно есть!
    def work(self):
        print("Working 24/7")
```

5. **D: Dependency Inversion (Инверсия зависимостей)**  
   Зависимость от абстракций, а не от реализаций
```python
class LightBulb:
    def turn_on(self):
        print("Light on")

class Switch:
    def __init__(self, bulb: LightBulb):  # Зависимость от конкретного класса
        self.bulb = bulb
    
    def operate(self):
        self.bulb.turn_on()

# По принципу DIP:
class SwitchableDevice(ABC):
    @abstractmethod
    def turn_on(self): pass

class Switch:
    def __init__(self, device: SwitchableDevice):  # Зависимость от абстракции
        self.device = device
    
    def operate(self):
        self.device.turn_on()
```

---

#### 🧩 **Другие ключевые принципы**

1. **DRY: Don't Repeat Yourself**  
   Избегайте дублирования кода
```python
# Плохо: Дублирование
def calc_circle_area(r):
    return 3.14 * r * r

def calc_sphere_volume(r):
    return (4/3) * 3.14 * r * r * r  # Повтор формулы площади

# Хорошо: Устранение дублирования
def circle_area(r):
    return 3.14 * r ** 2

def sphere_volume(r):
    return (4/3) * circle_area(r) * r
```

2. **KISS: Keep It Simple, Stupid**  
   Упрощайте решения
```python
# Сложное решение
def factorial(n):
    return 1 if n == 0 else n * factorial(n-1)

# Простое решение (KISS)
def factorial(n):
    result = 1
    for i in range(1, n+1):
        result *= i
    return result
```

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

4. **DI: Dependency Injection**  
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

---

### 🚀 Итоговые рекомендации
1. **Для ООП проектов:**
   - Используйте абстрактные классы (`ABC`) для определения интерфейсов
   - Применяйте композицию вместо наследования где возможно
   - Инкапсулируйте изменяемое состояние

2. **При работе с SOLID:**
   - Сначала сосредоточьтесь на SRP и DI - дают максимальный эффект
   - Используйте `mypy` для контроля типов при работе с абстракциями

3. **Для повседневного кодирования:**
   ```python
   # DRY + KISS + YAGNI в действии
   def process_data(data):
       """Простая обработка данных без 'магических' оптимизаций"""
       if not data:  # YAGNI: Нет обработки edge-кейсов, которые не встречаются
           return None
       
       result = []
       for item in data:
           processed = item * 2  # KISS: Прямолинейная логика
           result.append(processed)
       
       return result  # Нет дублирования кода (DRY)
   ```

Все принципы взаимосвязаны: соблюдение SOLID автоматически ведет к выполнению DRY и KISS, а DI помогает реализовать DIP из SOLID. Начните с малого - применяйте по одному принципу за раз!