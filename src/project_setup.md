# Project Setup

當你開始一個新的 Django 專案時，有一些基本的步驟和命令需要遵循


1. 安裝 Django

   首先，確保你已經安裝了 Python 和 pip。然後安裝 Django：

   ```bash
   pip install django
   ```
2. 創建 Django 專案

   ```bash
   django-admin startproject myproject
   ```

   這將創建一個名為 myproject 的目錄，其中包含專案的基本結構
3. 創建 Django 應用

   ```bash
   python manage.py startapp myapp
   ```

   創建一個名為 myapp 的目錄，其中包含應用的基本結構
4. 配置 `settings.py`

   編輯 `myproject/settings.py` 文件，將新應用添加到 `INSTALLED_APPS` 列表中：

   ```python
   INSTALLED_APPS = [
       ...
       'myapp',
   ]
   ```
5. 創建和應用資料庫遷移

   創建初始資料庫遷移文件：

   ```bash
   python manage.py makemigrations
   ```

   執行遷移:

   ```bash
   python manage.py migrate
   ```
6. 創建管理員用戶 (可選)

   創建一個管理員用戶以訪問 Django 管理界面：

   ```bash
   python manage.py createsuperuser
   ```
7. 啟動開發伺服器

   啟動開發伺服器以查看你的專案：

   ```bash
   python manage.py runserver
   ```

   默認情況下，伺服器將運行在 `http://127.0.0.1:8000/`


