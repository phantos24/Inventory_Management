# Authentication Setup for Inventory Management API

This document explains the authentication setup for the Inventory Management API and provides instructions for testing it. The API supports **Basic Authentication** using Django REST Framework (DRF).

---

## **Basic Authentication**

### **Overview**
Basic Authentication requires the client to send the username and password with every request in the `Authorization` header. This method is suitable for development or internal use.

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
