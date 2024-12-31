# Authentication

## 基本身份驗證

基本身份驗證是最簡單的身份驗證方法之一，它要求用戶提供用戶名和密碼。以下是如何在 Django Ninja 中實現基本身份驗證的示例：

```python
from ninja.security import HttpBasicAuth

auth = HttpBasicAuth()

@api.get("/protected", auth=auth)
def protected_operation(request):
    return {"message": "This is a protected endpoint"}
```

## API 密鑰身份驗證
API 密鑰身份驗證要求用戶提供一個有效的 API 密鑰。以下是如何實現 API 密鑰身份驗證的示例：

```python
from ninja.security import APIKeyHeader

class APIKeyAuth(APIKeyHeader):
    param_name = "X-API-Key"

    def authenticate(self, request, key):
        if key == "mysecretkey":
            return key

auth = APIKeyAuth()

@api.get("/protected", auth=auth)
def protected_operation(request):
    return {"message": "This is a protected endpoint"}
```

## JWT 身份驗證

JWT（JSON Web Token）身份驗證是一種無狀態的身份驗證方法，適合用於分佈式系統。以下是如何實現 JWT 身份驗證的示例：

```python
from ninja.security import HttpBearer
import jwt

class JWTAuth(HttpBearer):
    def authenticate(self, request, token):
        try:
            payload = jwt.decode(token, "secret", algorithms=["HS256"])
            return payload
        except jwt.PyJWTError:
            return None

auth = JWTAuth()

@api.get("/protected", auth=auth)
def protected_operation(request):
    return {"message": "This is a protected endpoint"}
```

## 多重身份驗證
您可以組合多種身份驗證方法來保護您的 API 端點。以下是如何實現多重身份驗證的示例：
```python
from ninja.security import HttpBasicAuth, APIKeyHeader

class APIKeyAuth(APIKeyHeader):
    param_name = "X-API-Key"

    def authenticate(self, request, key):
        if key == "mysecretkey":
            return key

basic_auth = HttpBasicAuth()
api_key_auth = APIKeyAuth()

@api.get("/protected", auth=[basic_auth, api_key_auth])
def protected_operation(request):
    return {"message": "This is a protected endpoint"}
```

## Router 身份驗證

如果您想要在 API 端點中使用身份驗證，但不想在每個端點中重新定義身份驗證，可以使用 Router 身份驗證。以下是如何實現 Router 身份驗證的示例：

```python
from ninja import Router
from ninja.security import HttpBasicAuth

auth = HttpBasicAuth()
router = Router(auth=auth)

@router.get("/protected")
def protected_operation(request):
    return {"message": "This is a protected endpoint"}

api.add_router("/protected", router)
```

## 自定義身份驗證
如果內置的身份驗證方法不滿足您的需求，您可以自定義身份驗證類。以下是如何自定義身份驗證的示例：

```python
def ip_whitelist(request):
    if request.META["REMOTE_ADDR"] == "8.8.8.8":
        return "8.8.8.8"


@api.get("/ipwhitelist", auth=ip_whitelist)
def ipwhitelist(request):
    return f"Authenticated client, IP = {request.auth}"
```