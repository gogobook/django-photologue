
##################################
客製化: extending templates
##################################

Photologue附帶了一組基本模板，讓您快速上手 - 你
當然也可以用自己的替換它們。這就是說，是可以將基本模板擴充
到自己的項目，並覆蓋不同的blocks，例如加上CSS類。
通常，這也就足夠了。

擴充模板的訣竅並不限於Photologue，這適用
其他項目中，如`Oscar<http://django-oscar.readthedocs.io/en/latest/howto/how_to_customise_templates.html>`_。

首先，設置你的模板配置為這樣：

.. code-block:: python

    # for Django versions < 1.8 
    TEMPLATE_LOADERS = (
        'django.template.loaders.filesystem.Loader',
        'django.template.loaders.app_directories.Loader',
    )

    from photologue import PHOTOLOGUE_APP_DIR
    TEMPLATE_DIRS = (
        ...other template folders...,
        PHOTOLOGUE_APP_DIR
    )
    
    # for Django versions >= 1.8
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [os.path.join(BASE_DIR, 'templates')],
            # note: if you set APP_DIRS to True, you won't need to add 'loaders' under OPTIONS
            # proceeding as if APP_DIRS is False
            'APP_DIRS': False,
            'OPTIONS': {
                'context_processors': [
                    ... context processors ...,
                ],
                # start - please add only if APP_DIRS is False
                'loaders': [
                    'django.template.loaders.filesystem.Loader',
                    'django.template.loaders.app_directories.Loader',
                ],   
                # end - please add only if APP_DIRS is False
            },
        },
    ]

``PHOTOLOGUE_APP_DIR``指向的資料夾如上面Photologue的正常
模板目錄。這意味著``路徑/要/ photologue / template.html``也可以
be reached via ``templates/path/to/photologue/template.html``.

例如，可以自定義``photologue / gallery_list.html``，你可以有這樣一個實現：

.. code-block:: html+django

    # Create your own photologue/gallery_list.html
    {% extends "templates/photologue/gallery_list.html" %}

    ... we are now extending the built-in gallery_list.html and we can override
    the content blocks that we want to customise ...