Usage
=====

.. _installation:

Installation
------------

To use Django Access Point, first install it using pip:

.. code-block:: console

   (.venv) $ pip install django-access-point

Steps to Integrate Django Access Point
--------------------------------------

1. Add Package Name to Project Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   * Open your Django project's ``settings.py`` file.
   * Add the package name ``django_access_point`` to your project's ``INSTALLED_APPS``:

   .. code-block:: python

      INSTALLED_APPS = [
          ...
          'rest_framework',
          'django_filters',
          'django_access_point'
      ]

2. Create Django App Using Django Access Point Package
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   Django Access Point provides a command that automatically generates a Django app complete with
   user management, role management, and authentication-related files. This allows you to focus on writing code
   for your business logic rather than spending time on creating APIs for authentication, user, and role management.

   * To create your app, make sure you’re in the same directory as ``manage.py`` and run the following command:

   .. code-block:: console

      (.venv) $ python manage.py createapp userApp

   This command will create a Django app named ``userApp`` with APIs for authentication, user, and role management.

3. Add Django App Name to Project Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   * Open your Django project's ``settings.py`` file.
   * Add the newly created app name ``userApp`` to your project's ``INSTALLED_APPS``:

   .. code-block:: python

      INSTALLED_APPS = [
          ...
          'rest_framework',
          'django_filters',
          'django_access_point',
          'userApp'
      ]

4. Add AUTH_USER_MODEL to Project Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   Ensure that you define or update the ``AUTH_USER_MODEL`` to point to the model inheriting from ``UserBase``. You can
   access the model in the path ``userApp/models.py``.

   .. code-block:: python

      AUTH_USER_MODEL = 'userApp.User'

   The ``TenantUser`` model, provided by extending the Django Access Point package, offers basic fields that manage
   user management and authentication. You can add your own fields on top of the ``TenantUser`` model and extend
   its functionality.

5. Add TENANT_MODEL to Project Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   Ensure that you define ``TENANT_MODEL`` to point to the model inheriting from ``TenantBase``. You can access the
   model in the path ``userApp/models.py``.

   .. code-block:: python

      TENANT_MODEL = 'userApp.Tenant'

   The ``Tenant`` model, provided by extending the Django Access Point package, offers basic fields that manage tenant
   management. You can add your own fields on top of the ``Tenant`` model and extend its functionality.

6. Migrate Models to Database
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   To migrate the newly created app models to the database, use Django's default ``makemigrations`` and ``migrate``
   commands. Follow these steps:

   * Create Migrations:

   .. code-block:: console

      (.venv) $ python manage.py makemigrations userApp

   * Apply Migrations:

   .. code-block:: console

      (.venv) $ python manage.py migrate

7. Update the Project's urls.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   * Open your project’s ``urls.py`` (located in the main project folder, e.g., ``myproject/urls.py``).
   * Import ``include`` from ``django.urls``.
   * Add your app's URL configuration using ``include``:

   .. code-block:: python

      from django.urls import path, include

      urlpatterns = [
          path('api/', include('userApp.urls')),  # Include your app's URLs
      ]
