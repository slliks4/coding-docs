# Django REST Framework: Class-Based Views vs. Function-Based Views

In Django REST Framework (DRF), both Class-Based Views (CBVs) and Function-Based Views (FBVs) are valid choices. However, most professional-grade APIs lean toward CBVs, especially DRF generics, for scalability, reusability, and clarity.

---

## ✅ Why Use Class-Based Views (CBVs)

### 1. **Less Boilerplate via Generics & Mixins**

Instead of repeating logic for CRUD operations in every function, DRF offers pre-built generic CBVs:

```python
class UserRetrieveAPIView(generics.RetrieveAPIView):
    queryset = CustomUser.objects.all()
    serializer_class = UserReadSerializer
    permission_classes = [permissions.IsAuthenticated]
```

This handles:

* Looking up objects
* Serializing
* Returning HTTP 200 or 404

Compare with an FBV:

```python
@api_view(['GET'])
@permission_classes([IsAuthenticated])
def user_detail(request, id):
    user = get_object_or_404(CustomUser, id=id)
    serializer = UserReadSerializer(user)
    return Response(serializer.data)
```

### 2. **Hookable Extension Points**

CBVs allow easy customization through specific method overrides:

* `get_object()`
* `get_queryset()`
* `perform_create()`
* `create()` / `retrieve()`

With FBVs, any customization often means repeating a large chunk of code.

### 3. **Integration with DRF Tools**

CBVs work better with:

* `DefaultRouter` for URL routing
* Schema generation for OpenAPI / Swagger
* Browsable API features (pagination, filters)

### 4. **Consistency and Readability**

When every view follows a standard class pattern, it becomes easier for teams to read and maintain code. You can quickly identify:

* Supported HTTP methods
* Required serializers
* Applied permissions

### 5. **Composable Behavior via Mixins**

DRF allows combining behaviors:

```python
class CustomView(mixins.ListModelMixin, mixins.CreateModelMixin, generics.GenericAPIView):
    ...
```

This is clean and powerful for views that support multiple methods.

### 6. **When to Use FBVs**

* One-off endpoints with simple logic
* Very custom workflows that don't fit CRUD patterns
* Preference for linear readability

---

## 🧠 What Are DRF Generics?

`rest_framework.generics` provides reusable CBVs for:

* `CreateAPIView`
* `RetrieveAPIView`
* `ListAPIView`
* `UpdateAPIView`
* `DestroyAPIView`
* and combos like `ListCreateAPIView`

These handle most CRUD operations with minimal setup.

---

## 🎯 Example: `RetrieveAPIView`

```python
class MeAPIView(generics.RetrieveAPIView):
    serializer_class = UserReadSerializer
    permission_classes = [IsAuthenticated]

    def get_object(self):
        return self.request.user
```

This view returns the authenticated user, automatically handling 200/404, serialization, and permission checks.

---

## 📘 Summary

* Use `APIView` for custom logic
* Use `generics` for standard CRUD to stay DRY
* Favor CBVs when working in a team or building scalable APIs
* Use FBVs for truly simple or unconventional endpoints

> Tip: You can mix FBVs and CBVs in one project as needed. Use the right tool for the job.

---

## 🔗 Resources

* [DRF Generic Views Docs](https://www.django-rest-framework.org/api-guide/generic-views/)
* [DRF APIView Docs](https://www.django-rest-framework.org/api-guide/viewsets/)
* [DRF Mixins Docs](https://www.django-rest-framework.org/api-guide/viewsets/#mixins)
