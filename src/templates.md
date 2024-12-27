# Templates

## 什麼是 Django 模板？

Django 模板是一種用於生成 HTML 的文本文件。它們允許你將動態內容嵌入到靜態 HTML 中，從而使你能夠動態生成網頁內容。模板使用 Django 模板語言（DTL）來控制邏輯和顯示。

## 如何定義模板

模板是純文本文件，通常具有 .html 擴展名。它們可以包含靜態 HTML、DTL 標籤和過濾器。

簡單的模板示例，它顯示一個動態消息：

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Template</title>
</head>
<body>
    <h1>{{ message }}</h1>
</body>
</html>
```

## 模板語言

Django 模板語言（DTL）提供了一組標籤和過濾器來控制模板的行為。

### 變量

使用雙大括號 {{ }} 來插入變量：

```python
<p>Hello, {{ name }}!</p>
```

### 標籤

Django 模板語言提供了多種標籤來控制模板的行為。以下是一些常見的標籤：

* `{% if %}`: 條件語句

  ```html
  {% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}!</p>
  {% else %}
    <p>Please log in.</p>
  {% endif %}
  ```
* `{% for %}`: 循環語句

  ```html
  <ul>
    {% for item in items %}
      <li>{{ item }}</li>
    {% endfor %}
  </ul>
  ```
* `{% block %}`: 模板繼承中的塊

  ```html
  {% block content %}
    <p>Default content</p>
  {% endblock %}
  ```
* `{% include %}`: 包含其他模板

  ```html
  {% extends 'base.html' %}
  ```

### 過濾器

過濾器是用來修改變量的顯示方式。以下是一些常見的過濾器：

* `|lower`: 將字符串轉換為小寫

  ```python
  {{ name|lower }}
  ```
* `|upper`: 將字符串轉換為大寫

  ```python
  {{ name|upper }}
  ```
* `|title`: 將字符串轉換為首字母大寫

  ```python
  {{ name|title }}
  ```
* `|length`: 將字符串長度轉換為數字

  ```python
  {{ name|length }}
  ```
* `|date`: 將日期轉換為格式化的日期

  ```python
  {{ date|date:"Y-m-d" }}
  ```

## 模板繼承

模板繼承允許你創建可重用的模板塊，從而避免重複代碼。

### 基模板

基模板定義了可重用的塊：

```html
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
</head>
<body>
    <div id="content">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

### 子模板

子模板繼承基模板並填充塊：

```html
<!-- child.html -->
{% extends "base.html" %}

{% block title %}My Page{% endblock %}

{% block content %}
<p>This is the content of my page.</p>
{% endblock %}
```

## 模板上下文

模板上下文是一個字典，它包含傳遞給模板的變量。你可以在視圖中創建上下文並將其傳遞給模板。

### 在視圖中使用模板

```python
from django.shortcuts import render

def my_view(request):
    context = {
        'message': 'Hello, world!',
        'item_list': [{'name': 'Item 1'}, {'name': 'Item 2'}],
    }
    return render(request, 'my_template.html', context)
```

## 加載模板

Django 使用模板加載器來查找和加載模板文件。默認情況下，Django 會在每個應用的 `templates` 目錄中查找模板。

### 配置模板加載器

在設置文件中配置模板加載器：

```python
# settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

## 自定義模板、過濾器標籤

創建自定義模板標籤和過濾器來擴展 Django 模板語言。

### 自定義模板標籤

```python
# templatetags/my_tags.py
from django import template

register = template.Library()

@register.simple_tag
def my_tag(arg):
    return f"Argument: {arg}"
```


### 自定義模板過濾器

```python
# templatetags/my_filters.py
from django import template

register = template.Library()

@register.filter
def my_filter(value, arg):
    return f"Value: {value}, Argument: {arg}"
```

### 在模板中使用自定義標籤和過濾器

```python
{% load my_tags my_filters %}

<p>{% my_tag "Hello" %}</p>
<p>{{ "world"|my_filter:"Django" }}</p>
```


