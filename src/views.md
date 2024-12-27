# Views

## 什麼是 Django 視圖？

Django 視圖是一個簡單的 Python 函數，它接受一個網頁請求並返回一個網頁響應。視圖可以是基於函數的視圖（Function-Based Views, FBV）或基於類的視圖（Class-Based Views, CBV）。

## 基於函數的視圖 (FBV)

基於函數的視圖是最簡單的視圖形式。它們是接受一個 HttpRequest 對象並返回一個 HttpResponse 對象的 Python 函數。

### 定義基於函數的視圖

簡單的基於函數的視圖示例：

```python
from django.http import HttpResponse

def my_view(request):
    return HttpResponse("Hello, world!")
```

#### 使用模板

可以使用 Django 模板來生成 HTML 響應：

```python
from django.shortcuts import render

def my_view(request):
    context = {'message': 'Hello, world!'}
    return render(request, 'my_template.html', context)
```

#### 處理表單數據

在視圖中處理表單數據：

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import MyForm

def my_view(request):
    if request.method == 'POST':
        form = MyForm(request.POST)
        if form.is_valid():
            # 處理表單數據
            return HttpResponseRedirect('/success/')
    else:
        form = MyForm()

    return render(request, 'my_template.html', {'form': form})
```

### 基於類的視圖 (CBV)

基於類的視圖提供了一種更結構化的方式來組織視圖邏輯。它們是基於 Python 類的，並且可以繼承和重用。

#### 定義基於類的視圖

基於類的視圖示例：

```python
from django.http import HttpResponse
from django.views import View

class MyView(View):
    def get(self, request):
        return HttpResponse("Hello, world!")
```


#### 使用通用視圖

Django 提供了一組通用視圖，可以簡化常見的視圖邏輯。例如，`ListView` 和 `DetailView`：

```python
from django.views.generic import ListView, DetailView
from .models import MyModel

class MyListView(ListView):
    model = MyModel
    template_name = 'my_model_list.html'

class MyDetailView(DetailView):
    model = MyModel
    template_name = 'my_model_detail.html'
```

#### 處理表單數據

使用 FormView 來處理表單數據：

```python
from django.urls import reverse_lazy
from django.views.generic.edit import FormView
from .forms import MyForm

class MyFormView(FormView):
    template_name = 'my_template.html'
    form_class = MyForm
    success_url = reverse_lazy('success')

    def form_valid(self, form):
        # 處理表單數據
        return super().form_valid(form)
```


