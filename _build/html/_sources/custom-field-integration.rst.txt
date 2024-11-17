Integrating Custom Field into CRUD API
======================================
To integrate a ``Custom Field`` into the ``CRUD API``, ensure that both the CRUD API and the Custom Field for the module have already been created.

**views.py**

.. code-block:: python

    from django_access_point.views.crud import CrudViewSet
    from .serializers import PostSerializer
    from .models import Post, PostCustomField, PostCustomFieldValue

    class PostViewSet(CrudViewSet):
        queryset = Post.objects.all()
        list_fields = {"id": "ID", "name": "Name", "description": "Description"}
        serializer_class = PostSerializer
        custom_field_model = PostCustomField
        custom_field_value_model = PostCustomFieldValue

Include ``custom_field_model`` & ``custom_field_value_model`` to the **CRUD** views.