# Authentication Setup for Inventory Management API

This document explains the authentication setup for the Inventory Management API and provides instructions for testing it. The API supports **Basic Authentication** and **Token-Based Authentication** using Django REST Framework (DRF).

---

## **Basic Authentication**

### **Overview**
Basic Authentication requires the client to send the username and password with every request in the `Authorization` header. This method is suitable for development or internal use but is less secure compared to token-based authentication.

### **Setup**

1. Update `settings.py` to include the required authentication classes:
   ```python
   REST_FRAMEWORK = {
       'DEFAULT_AUTHENTICATION_CLASSES': [
           'rest_framework.authentication.BasicAuthentication',
           'rest_framework.authentication.SessionAuthentication',
       ],
       'DEFAULT_PERMISSION_CLASSES': [
           'rest_framework.permissions.IsAuthenticated',
       ],
   }
   ```

2. Apply `IsAuthenticated` permission to views to require authentication:
   ```python
   from rest_framework.permissions import IsAuthenticated
   from rest_framework.views import APIView
   from rest_framework.response import Response

   class ProductListView(APIView):
       permission_classes = [IsAuthenticated]

       def get(self, request):
           return Response({"message": "List of products"})
   ```

### **Testing Basic Authentication**
1. **With cURL**:
   ```bash
   curl -u username:password http://127.0.0.1:8000/api/v1/products/
   ```

2. **With Postman**:
   - Select the **Authorization** tab.
   - Choose **Basic Auth**.
   - Enter the username and password.
   - Send a request to an endpoint (e.g., `http://127.0.0.1:8000/api/v1/products/`).

---

## **Token-Based Authentication**

### **Overview**
Token-Based Authentication uses a secure token for each request, avoiding the need to send credentials repeatedly. This implementation uses **JWT (JSON Web Tokens)** via the `djangorestframework-simplejwt` package.

### **Setup**

1. Install the required packages:
   ```bash
   pip install djangorestframework djangorestframework-simplejwt
   ```

2. Update `settings.py` to use JWT authentication:
   ```python
   REST_FRAMEWORK = {
       'DEFAULT_AUTHENTICATION_CLASSES': [
           'rest_framework_simplejwt.authentication.JWTAuthentication',
       ],
       'DEFAULT_PERMISSION_CLASSES': [
           'rest_framework.permissions.IsAuthenticated',
       ],
   }
   ```

3. Add token endpoints to `urls.py`:
   ```python
   from django.urls import path
   from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

   urlpatterns = [
       path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
       path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
   ]
   ```

4. Restrict views using the `IsAuthenticated` permission:
   ```python
   from rest_framework.permissions import IsAuthenticated
   from rest_framework.views import APIView
   from rest_framework.response import Response

   class ProductListView(APIView):
       permission_classes = [IsAuthenticated]

       def get(self, request):
           return Response({"message": "List of products"})
   ```

### **Testing Token-Based Authentication**

1. **Obtain a Token:**
   Send a POST request to `/api/token/` with the username and password:
   ```bash
   curl -X POST http://127.0.0.1:8000/api/token/ -d "username=<username>&password=<password>"
   ```
   Response example:
   ```json
   {
       "refresh": "<refresh_token>",
       "access": "<access_token>"
   }
   ```

2. **Use the Access Token:**
   Include the `Authorization` header in subsequent requests:
   ```bash
   curl -H "Authorization: Bearer <access_token>" http://127.0.0.1:8000/api/v1/products/
   ```

3. **Refresh the Token:**
   To refresh an expired access token, send a POST request to `/api/token/refresh/` with the `refresh` token:
   ```bash
   curl -X POST http://127.0.0.1:8000/api/token/refresh/ -d "refresh=<refresh_token>"
   ```

4. **With Postman:**
   - Obtain a token by sending a POST request to `/api/token/`.
   - Copy the `access` token from the response.
   - For subsequent requests, add a header:
     - Key: `Authorization`
     - Value: `Bearer <access_token>`

---

## **Authentication Summary**

| Authentication Type | Endpoint                       | Header Example                                        |
|---------------------|--------------------------------|------------------------------------------------------|
| **Basic Auth**      | `/api/v1/products/`           | `Authorization: Basic <base64(username:password)>`   |
| **Token Auth**      | `/api/v1/products/`           | `Authorization: Bearer <access_token>`              |
| **Token Obtain**    | `/api/token/`                 | -                                                    |
| **Token Refresh**   | `/api/token/refresh/`         | -                                                    |

---

For further assistance, feel free to contact the development team.

