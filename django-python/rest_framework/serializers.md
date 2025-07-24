# Serializer Docs
```yaml
https://www.django-rest-framework.org/api-guide/serializers/
https://www.django-rest-framework.org/api-guide/serializers/fields
https://www.django-rest-framework.org/api-guide/serializers/relations
```
## Nested Objects
When dealing with nested Objects, ensure you declare them as one of the fields from when it is being called else it will throw an error

## Read Only fields
you can specify read-only fields with 

```ini
read_only_fields=()
```

A tupple contain all read-only field
