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