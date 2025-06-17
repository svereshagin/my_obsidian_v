
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
 ---
							<h2> Fastapi DI </h2>
 ___
 
``` python
from typing import Annotated

from fastapi import Depends, FastAPI

app = FastAPI()


async def common_parameters(q: str | None = None, skip: int = 0, limit: int = 100):
    return {"q": q, "skip": skip, "limit": limit}


@app.get("/items/")
async def read_items(commons: Annotated[dict, Depends(common_parameters)]):
    return commons
```