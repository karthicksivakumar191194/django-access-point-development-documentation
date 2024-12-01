CRUD List Search & Filter
==========================

In Django Access Point, the search and filter functionality is designed to allow users to search and filter both CRUD (Create, Read, Update, Delete) fields and Custom Fields.
Here's a detailed breakdown of how these features work and how they can be used in the CRUD List API:

Search
------
How Search Works on CRUD:
^^^^^^^^^^^^^^^^^^^^^^^^^^
    * The search functionality for CRUD fields is driven by the ``list_fields`` attribute that is defined in the view.
    * When a user includes a **search=keyword** parameter in the query string of the CRUD List API, the system searches for the keyword in the fields specified in list_fields.

**Example:** If ``list_fields = ['name', 'description']`` in the view, and the user adds **?search=apple** to the URL, the system will search for records where *name or description* contains the word "apple".

    **/api/crud-list/?search=apple**

How Search Works on Custom Fields:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    * Custom fields are the extra fields that are added to the CRUD for additional functionality, the search feature for custom fields is only available for active custom fields that are of certain types: ``Text Box``, ``Website URL``, ``TextArea``, ``Number``, ``Email``, ``Phone Number``, ``Hidden``.
    * If a user includes the **?search=keyword** parameter in the query, the system searches within the custom field values that match the search term in any of these types.

**Example:** If there is an active custom field of type "Text Box", and the user includes **?search=apple**, it will search within the custom field values where the text matches "apple".

    **/api/crud-list/?search=apple**

How to Use Search on CRUD & Custom Fields:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    * To search for specific keywords in both CRUD & custom fields, simply add the **?search=keyword** parameter to the query string of the CRUD List API.

**Example URL:** /api/crud-list/?search=apple

This URL will search for the term "apple" across both CRUD fields (e.g., name, description) and any relevant active custom fields (e.g., text box fields).


Filter
------
How Filter Works on CRUD:
^^^^^^^^^^^^^^^^^^^^^^^^^
    * Filtering on CRUD fields is done by using the **__icontains** lookup, which performs a case-insensitive search. You can filter any model field that supports the __icontains lookup.

**Example:** To filter records where the **name** field contains the word "apple", the URL would look like:

    **/api/crud-list/?filter_name=apple**

This would filter the list of CRUD records to only include those where the **name** field contains the keyword "apple".

How Filter Works on Custom Fields:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    * Filtering on custom fields is supported for active custom fields with specific types: ``Dropdown``, ``Radio``, ``MultiSelect Checkbox``.
    * You can filter these custom fields by their values, just as you filter CRUD fields using the **__icontains** lookup.

**Example:** If there is an active **custom field(customer field id = 1)** of type "Dropdown" and the user wants to filter the records where the custom field contains the option "Option 1"(option label = Option 1, option value = option1), they can use the following filter parameter:

    **/api/crud-list/?filter_custom_field_1=option1**

This would filter the list of records where the value of the custom field (custom_field_1) contains "option1".

How to Use Filter on CRUD & Custom Fields:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To filter the list API by both CRUD and custom fields, use the ``filter`` prefix for CRUD fields and ``filter_custom_field`` for custom fields.

**Example URL:**
    /api/crud-list/?filter_name=apple&filter_description=fruit&filter_custom_field_1=option1

This URL would filter the records where:
    * name contains "apple"
    * description contains "fruit"
    * The custom field with ID 1 (e.g., a dropdown) contains the value "option1".


Examples
--------
1. AND Condition (Default Behavior)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
By default, most filter parameters combine conditions with an **AND** logic. This means that if you provide multiple filters for the same field, only records that match **all** conditions will be returned.

**Example:**
    GET /api/data?filter_name=John&filter_age=30

    * **Filter 1:**
        - **filter_name=John** (Find records where the name field is "John")
    * **Filter 2:**
        - **filter_age=30** (Find records where the age field is 30)

**Logic:** This query will return records where both **name** is "John" **and** **age** is 30.

2. OR Condition (Using or_filter_*)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To explicitly create **OR** conditions between different filter fields, you can use ``or_filter_*`` for each field. This allows the system to search for records that match **any** of the conditions in the OR group.

**Example:**
    GET /api/data?or_filter_name=John&or_filter_name=Jane

    * **OR Filter 1:**
        - **or_filter_name=John** (Find records where the name is "John")
    * **OR Filter 2:**
        - **or_filter_name=Jane** (Find records where the name is "Jane")

**Logic:** This query will return records where **name** is either "John" **or** "Jane".

3. Combination of AND and OR Conditions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You can combine **AND** and **OR** filters within a single request to create complex queries. To achieve this, use the default filter parameters ``filter_*`` for the **AND** logic and ``or_filter_*`` for the **OR** logic.

**Example:**
    GET /api/data?filter_age=30&filter_city=New York&or_filter_name=John&or_filter_name=Jane

    * **AND Filters:**
        - **filter_age=30:** Only include records where **age** is 30.
        - **filter_city=New York:** Only include records where **city** is "New York".
    * **OR Filters:**
        - **or_filter_name=John:** Include records where **name** is either "John".
        - **or_filter_name=Jane:** Include records where **name** is either "Jane".

**Logic:** This query will return records where:
    * **age** is 30 **and** **city** is "New York",
    * **or** the **name** is either "John" **or** "Jane".