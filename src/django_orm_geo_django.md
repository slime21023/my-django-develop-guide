# GeoDjango

GeoDjango 是 Django 的地理擴展，允許開發者在 Django 模型中使用地理數據。它提供了強大的地理數據處理功能，支持地理查詢、地理計算和地理可視化。GeoDjango 與多種地理資訊庫（如 PostGIS、SpatiaLite 和 MySQL）集成，使得地理數據管理更加高效和靈活。


## 安裝和配置
要使用 GeoDjango，需要安裝相應的地理數據庫和 Python 庫。以下是安裝和配置 GeoDjango 的步驟：
* 安裝
  ```python
  sudo apt-get install mysql-server mysql-client libmysqlclient-dev
  sudo apt-get install binutils libproj-dev gdal-bin
  pip install django mysqlclient
  ```

* 配置 Django 設置
  ```python
  # settings.py
  DATABASES = {
      'default': {
          'ENGINE': 'django.contrib.gis.db.backends.mysql',
          'NAME': 'geodjango',
          'USER': 'geo',
          'PASSWORD': 'password',
          'HOST': 'localhost',
          'PORT': '3306',
      }
  }

  INSTALLED_APPS = [
      ...
      'django.contrib.gis',
  ]
  ```

## 定義地理模型
GeoDjango 提供了多種地理字段，如 `PointField`、`LineStringField` 和 `PolygonField`，用於存儲地理數據。

* 基本地理模型：
  使用地理字段來定義地理模型。
  ```python
  from django.contrib.gis.db import models

  class Location(models.Model):
      name = models.CharField(max_length=100)
      point = models.PointField()

      def __str__(self):
          return self.name
  ```

* 地理管理器：
  使用地理管理器來進行地理查詢。
  ```python
  from django.contrib.gis.db import models

  class Location(models.Model):
      name = models.CharField(max_length=100)
      point = models.PointField()
      objects = models.GeoManager()

      def __str__(self):
          return self.name
  ```

## 地理查詢
GeoDjango 支持多種地理查詢，如距離查詢、包含查詢和相交查詢。
* 距離查詢:
  查詢距離特定點一定距離內的地理對象。
  ```python
  from django.contrib.gis.geos import Point
  from django.contrib.gis.measure import D
  from myapp.models import Location

  # 定義一個點
  pnt = Point(10, 10)

  # 查詢距離該點 5 公里內的所有 Location 對象
  nearby_locations = Location.objects.filter(point__distance_lte=(pnt, D(km=5)))
  ```

* 包含查詢：
  查詢包含在特定地理區域內的地理對象。
  ```python
  from django.contrib.gis.geos import Polygon
  from myapp.models import Location

  # 定義一個多邊形區域
  poly = Polygon(((0, 0), (0, 10), (10, 10), (10, 0), (0, 0)))

  # 查詢包含在該多邊形區域內的所有 Location 對象
  locations_within_area = Location.objects.filter(point__within=poly)
  ```

* 相交查詢:
  查詢與特定地理區域相交的地理對象。
  ```python
  from django.contrib.gis.geos import LineString
  from myapp.models import Location

  # 定義一條線
  line = LineString((0, 0), (5, 5))

  # 查詢與該線相交的所有 Location 對象
  intersecting_locations = Location.objects.filter(point__intersects=line)
  ```

## 地理計算
GeoDjango 提供了多種地理計算功能，如距離計算、面積計算和長度計算。

* 距離計算:
  計算兩個地理點之間的距離。
  ```python
  from django.contrib.gis.geos import Point
  from django.contrib.gis.measure import D

  # 定義兩個點
  pnt1 = Point(10, 10)
  pnt2 = Point(20, 20)

  # 計算兩個點之間的距離
  distance = pnt1.distance(pnt2)
  print(distance)  # 輸出: 14.142135623730951 (單位：度)
  ```

* 面積計算：
  計算地理多邊形的面積。
  ```python
  from django.contrib.gis.geos import Polygon
  from django.contrib.gis.measure import Area

  # 定義一個多邊形
  poly = Polygon(((0, 0), (0, 10), (10, 10), (10, 0), (0, 0)))

  # 計算多邊形的面積
  area = poly.area
  print(area)  # 輸出: 100.0 (單位：平方度)
  ```

* 長度計算：
  計算地理線的長度。
  ```python
  from django.contrib.gis.geos import LineString
  from django.contrib.gis.measure import D

  # 定義一條線
  line = LineString((0, 0), (5, 5))

  # 計算線的長度
  length = line.length
  print(length)  # 輸出: 7.0710678118654755 (單位：度)
  ```

## 地理可視化
GeoDjango 支持地理數據的可視化，可以將地理數據渲染到地圖上。

* 基本地理可視化：
  使用地理數據庫和地理 Python 庫來渲染地理數據。
  ```python
  from django.contrib.gis.geos import Point
  from django.contrib.gis.gdal import OGRGeometry
  from myapp.models import Location

  # 創建一個新的 Location 對象
  location = Location.objects.create(name="My Location", point=Point(10, 10))

  # 將地理數據渲染到地圖上
  geom = OGRGeometry(location.point.wkt)
  ```