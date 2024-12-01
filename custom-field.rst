Creating Custom Field
======================
The ``CustomFieldViewSet`` class is a base view for handling ``Custom Field CRUD`` (Create, Read, Update, Delete) operations
in a Django Rest Framework (DRF) API for a module. It inherits from **GenericViewSet** and provides implementations for the common
operations needed to manage **Custom Fields for a module** in a typical RESTful API.

**CustomFieldViewSet provides following APIs:**
    * **Create**: Allows creating new Custom Fields.
    * **Read**: Retrieves Custom Fields.
    * **Update**: Modifies existing Custom Fields.
    * **Delete**: Removes Custom Fields.
    * **List**: List all Custom Fields.

By inheriting from ``CustomFieldViewSet``, you can quickly implement these APIs without needing to write custom
code for each one.

**models.py**

.. code-block:: python

    from django_access_point.models.custom_field import CustomFieldBase, CustomFieldOptionsBase, CustomFieldValueBase
    from .models import Tenant, Post

    class PostCustomField(CustomFieldBase):
        tenant = models.ForeignKey(
            Tenant, on_delete=models.CASCADE, null=True, default=None
        )

*Note: These are the required fields in the Custom Field Model. Make sure this field name is not changed.*

.. code-block:: python

    class PostCustomFieldOptions(CustomFieldOptionsBase):
        custom_field = models.ForeignKey(PostCustomField, on_delete=models.CASCADE)

*Note: These are the required fields in the Custom Field Options Model. Make sure this field name is not changed.*

* ``custom_field``: This should be a **ForeignKey** to the model that defines the custom field. For example, if you are mapping the Custom Field Value to ``PostCustomField``, specify ``PostCustomField`` as the foreign key.

.. code-block:: python

    class PostCustomFieldValue(CustomFieldValueBase):
        submission = models.ForeignKey(Post, related_name="custom_field_values", on_delete=models.CASCADE)
        custom_field = models.ForeignKey(PostCustomField, on_delete=models.CASCADE)

*Note: These are the required fields in the Custom Field Value Model. Make sure these 2 field names are not changed. You can also add your own custom fields as per your requirements.*

* ``submission``: This should be a **ForeignKey** to the model you want to map the **Custom Field Value** to. For instance, if you want to map this Custom Field Value to the ``Post`` model, use ``Post`` as the foreign key. Make sure to specify the ``related_name`` as ``custom_field_values``.
* ``custom_field``: This should be a **ForeignKey** to the model that defines the custom field. For example, if you are mapping the Custom Field Value to ``PostCustomField``, specify ``PostCustomField`` as the foreign key.

**serializers.py**

.. code-block:: python

    from django_access_point.serializers.custom_field import CustomFieldSerializer
    from .models import PostCustomField

    class PostCustomFieldSerializer(CustomFieldSerializer):
        class Meta:
            model = PostCustomField
            fields = CustomFieldSerializer.Meta.fields

**views.py**

.. code-block:: python

    from django_access_point.views.custom_field import CustomFieldViewSet
    from .serializers import PostCustomFieldSerializer
    from .models import PostCustomField, PostCustomFieldOptions

    class PostCustomFieldViewSet(CustomFieldViewSet):
        queryset = PostCustomField.objects.all()
        serializer_class = PostCustomFieldSerializer
        custom_field_options_model = PostCustomFieldOptions

**urls.py**

.. code-block:: python

    from rest_framework.routers import DefaultRouter
    from .views import PostCustomFieldViewSet

    router = DefaultRouter()
    router.register(r"custom-fields/posts", PostCustomFieldViewSet, basename="post.custom_fields")

    urlpatterns = []

    urlpatterns += router.urls

