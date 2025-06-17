
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
