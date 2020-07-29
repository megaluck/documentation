Projects
~~~~~~~~


.. tabs::

   .. tab:: Apples

      Apples are green, or sometimes red.

   .. tab:: Pears

      Pears are green.

   .. tab:: Oranges

      Oranges are orange.

Projects list
+++++++++++++

.. http:get:: /users/(int:user_id)/posts/(tag)

   The posts tagged with `tag` that the user (`user_id`) wrote.

   **Example request**:

   .. sourcecode:: http

      GET /users/123/posts/web HTTP/1.1
      Host: example.com
      Accept: application/json, text/javascript

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Vary: Accept
      Content-Type: text/javascript

      [
        {
          "post_id": 12345,
          "author_id": 123,
          "tags": ["server", "web"],
          "subject": "I tried Nginx"
        },
        {
          "post_id": 12346,
          "author_id": 123,
          "tags": ["html5", "standards", "web"],
          "subject": "We go to HTML 5"
        }
      ]

   :query sort: one of ``hit``, ``created-at``
   :query offset: offset number. default is 0
   :query limit: limit number. default is 30
   :reqheader Accept: the response content type depends on
                      :mailheader:`Accept` header
   :reqheader Authorization: optional OAuth token to authenticate
   :resheader Content-Type: this depends on :mailheader:`Accept`
                            header of request
   :statuscode 200: no error
   :statuscode 404: there's no user


https://gist.github.com/rxaviers/7360908



|:fire:|


|:arrows_counterclockwise:|

ddddddddddddddddd

ddddddddddddddddd

ddddddddddddddddd

ddddddddddddddddd


ddddddddddddddddd

.. http:get:: /api/v3/projects/

    Retrieve a list of all the projects for the current logged in user.

    **Example request**:

    .. tabs::

        .. code-tab:: bash

            $ curl -H "Authorization: Token <token>" https://readthedocs.org/api/v3/projects/

        .. code-tab:: python

            import requests
            URL = 'https://readthedocs.org/api/v3/projects/'
            TOKEN = '<token>'
            HEADERS = {'Authorization': f'token {TOKEN}'}
            response = requests.get(URL, headers=HEADERS)
            print(response.json())

    **Example response**:

    .. sourcecode:: json

        {
            "count": 25,
            "next": "/api/v3/projects/?limit=10&offset=10",
            "previous": null,
            "results": [{
                "id": 12345,
                "name": "Pip",
                "slug": "pip",
                "created": "2010-10-23T18:12:31+00:00",
                "modified": "2018-12-11T07:21:11+00:00",
                "language": {
                    "code": "en",
                    "name": "English"
                },
                "programming_language": {
                    "code": "py",
                    "name": "Python"
                },
                "repository": {
                    "url": "https://github.com/pypa/pip",
                    "type": "git"
                },
                "default_version": "stable",
                "default_branch": "master",
                "subproject_of": null,
                "translation_of": null,
                "urls": {
                    "documentation": "http://pip.pypa.io/en/stable/",
                    "home": "https://pip.pypa.io/"
                },
                "tags": [
                    "disutils",
                    "easy_install",
                    "egg",
                    "setuptools",
                    "virtualenv"
                ],
                "users": [
                    {
                        "username": "dstufft"
                    }
                ],
                "active_versions": {
                    "stable": "{VERSION}",
                    "latest": "{VERSION}",
                    "19.0.2": "{VERSION}"
                },
                "_links": {
                    "_self": "/api/v3/projects/pip/",
                    "versions": "/api/v3/projects/pip/versions/",
                    "builds": "/api/v3/projects/pip/builds/",
                    "subprojects": "/api/v3/projects/pip/subprojects/",
                    "superproject": "/api/v3/projects/pip/superproject/",
                    "redirects": "/api/v3/projects/pip/redirects/",
                    "translations": "/api/v3/projects/pip/translations/"
                }
            }]
        }

    :query string language: language code as ``en``, ``es``, ``ru``, etc.
    :query string programming_language: programming language code as ``py``, ``js``, etc.

    The ``results`` in response is an array of project data,
    which is same as :http:get:`/api/v3/projects/(string:project_slug)/`.
