Creating CRUD API
===================
The ``CrudViewSet`` class is a base view for handling ``CRUD`` (Create, Read, Update, Delete) operations in a
Django Rest Framework (DRF) API. It inherits from **GenericViewSet** and provides implementations for the common
operations needed to manage resources in a typical RESTful API.

This class simplifies the creation of views for common API operations, allowing developers to focus on their business
logic rather than CRUD functionality.

**CrudViewSet provides following APIs:**
    * **Create**: Allows creating new records.
    * **Read**: Retrieves records.
    * **Update**: Modifies existing records.
    * **Delete**: Soft delete records.
    * **List**: List all records with pagination.

By inheriting from ``CrudViewSet``, you can quickly implement these APIs without needing to write custom
code for each one.

Basic
-----
**models.py**

.. code-block:: python

    from django.db import models

    class Post(models.Model):
        title = models.CharField(max_length=255)
        description = models.TextField()
        created_at = models.DateTimeField(auto_now_add=True)
        updated_at = models.DateTimeField(auto_now=True)

**serializers.py**

.. code-block:: python

    from django_access_point.serializers.crud import CrudSerializer
    from .models import Post

    class PostSerializer(CrudSerializer):
        class Meta:
            model = Post
            fields = ["id", "name", "description"]

**views.py**

.. code-block:: python

    from django_access_point.views.crud import CrudViewSet
    from .serializers import PostSerializer
    from .models import Post

    class PostViewSet(CrudViewSet):
        queryset = Post.objects.all()
        list_fields = {"id": "ID", "name": "Name", "description": "Description"}
        serializer_class = PostSerializer

``list_fields``: Define the fields that need to be returned in the List API response. This should be in the format:
*{<model_field>: <label_to_display_on_list>}*

**urls.py**

.. code-block:: python

    from rest_framework.routers import DefaultRouter
    from .views import Post

    router = DefaultRouter()
    router.register(r"posts", PostViewSet, basename="post")

    urlpatterns = []

    urlpatterns += router.urls

Advanced
--------
Django Access Point provides flexibility to customize the behavior of the CRUD views by allowing you to override
the following methods:

    * ``get_list_fields()``: Dynamically modify the fields returned in the list view.
    * ``get_queryset()``: Customize the queryset for the list view or other actions.
    * ``get_serializer_class()``: Choose the serializer to use based on the HTTP request method or other conditions.

**views.py**

.. code-block:: python

    from django_access_point.views.crud import CrudViewSet
    from .serializers import PostSerializer, PostSaveSerializer, PostGetSerializer
    from .models import Post

    class PostViewSet(CrudViewSet):
        # Define the default queryset for the viewset
        queryset = Post.objects.all()
        serializer_class = PostSerializer

        def get_list_fields(self):
            """
            Override the Django Access Point 'list_fields' attribute to dynamically select which
            fields to include in the list API response, or return a predefined set of fields.

            You can implement custom logic here based on conditions or just return a static
            dictionary.
            """

            return {"id": "ID", "name": "Name", "description": "Description"}

        def get_queryset(self):
            """
            Override the DRF 'get_queryset' method to dynamically return a queryset based on
            the request or condition.

            You can implement logic to filter or modify the queryset based on the request
            method or parameters.
            """

            return self.queryset

        def get_serializer_class(self):
            """
            Override the DRF 'get_serializer_class' method to return different serializers based
            on the request method.

            This allows you to return a different serializer for 'GET', 'POST', or other methods.
            """
            if self.request.method == "POST":
                # Return a serializer specific for the 'POST' method (e.g., saving new post data)
                return PostSaveSerializer
            elif self.request.method == "GET":
                # Return a serializer specific for the 'GET' method (e.g., fetching post data)
                return PostGetSerializer
            else:
                # Default case, return the default serializer class
                return self.serializer_class
