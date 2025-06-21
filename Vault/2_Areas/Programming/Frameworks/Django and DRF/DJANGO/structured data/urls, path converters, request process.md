

# docs #docs_django
https://docs.djangoproject.com/en/5.2/topics/http/urls/

## Как Django обрабатывает запрос [¶](https://docs.djangoproject.com/en/5.2/topics/http/urls/#how-django-processes-a-request "Ссылка на этот заголовок")

Когда пользователь запрашивает страницу с вашего сайта на базе Django, система следует следующему алгоритму, чтобы определить, какой код Python следует выполнить:

1. определяет ROOT_URLCONF аттрибут, но если входящий HTTPREQUEST имеет urlconf атрибут (установленный промежуточным программным обеспечением0 его значением будет использовано вместо  ROOT-URLCONF настройки)
2. джанго загружает этот модуль и ищет переменную url_patterns, которая представляет собой последовательность экземпляров: django.urls.path()/ django.urls.re_path
- `path()` → для стандартных URL.
- `re_path()` → если нужны regex-фильтры.
1. Django проходит по каждому шаблону URL по порядку и останавливается на первом, который соответствует запрошенному URL, сопоставляя его с [`path_info`](https://docs.djangoproject.com/en/5.2/ref/request-response/#django.http.HttpRequest.path_info "django.http.HttpRequest.path_info").
2. Как только один из шаблонов URL совпадает, Django импортирует и вызывает указанное представление, которое является функцией Python (или [представлением на основе класса](https://docs.djangoproject.com/en/5.2/topics/class-based-views/) ). Представлению передаются следующие аргументы:
    
    - Экземпляр [`HttpRequest`](https://docs.djangoproject.com/en/5.2/ref/request-response/#django.http.HttpRequest "django.http.HttpRequest").
        
    - Если сопоставленный шаблон URL не содержит именованных групп, то соответствия из регулярного выражения предоставляются в качестве позиционных аргументов.
        
    - Ключевые аргументы состоят из любых именованных частей, соответствующих указанному выражению пути, переопределяемому любыми аргументами, указанными в необязательном `kwargs`аргументе [`django.urls.path()`](https://docs.djangoproject.com/en/5.2/ref/urls/#django.urls.path "django.urls.путь")или [`django.urls.re_path()`](https://docs.djangoproject.com/en/5.2/ref/urls/#django.urls.re_path "django.urls.re_path").
        
3. Если шаблон URL не совпадает или если в любой точке этого процесса возникает исключение, Django вызывает соответствующее представление обработки ошибок. См. [Обработка ошибок](https://docs.djangoproject.com/en/5.2/topics/http/urls/#error-handling) ниже.