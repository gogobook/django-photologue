
#######################
Customisation: Settings
#######################


Photologue有幾個設置以客製化行為。

PHOTOLOGUE_GALLERY_LATEST_LIMIT
-------------------------------

    Default: ``None``

Default limit for gallery.latest


PHOTOLOGUE_GALLERY_SAMPLE_SIZE
------------------------------

    Default: ``5``

Number of random images from the gallery to display.


PHOTOLOGUE_IMAGE_FIELD_MAX_LENGTH
---------------------------------

    Default: ``100``

max_length setting for the ImageModel ImageField


PHOTOLOGUE_SAMPLE_IMAGE_PATH
----------------------------

    Default: ``os.path.join(os.path.dirname(__file__), 'res', 'sample.jpg'))``

Path to sample image


PHOTOLOGUE_MAXBLOCK
-------------------

    Default: ``256 * 2 ** 10``

Modify image file buffer size.


PHOTOLOGUE_DIR
--------------

    Default: ``'photologue'``

The relative path from your ``MEDIA_ROOT`` setting where Photologue will save image files. If your ``MEDIA_ROOT`` is set to "/home/user/media", photologue will upload your images to "/home/user/media/photologue"


PHOTOLOGUE_PATH
---------------

    Default: ``None``

尋找用戶的功能來定義的文件路徑。指定需要一個模型實例和原始上傳文件名，並從你的``MEDIA_ROOT``返回一個相對路徑，該文件將被保存在“通知”。該功能可以直接設置。

例如，你可以使用下面的代碼到一個工具程序模組::

    # myapp/utils.py:

    import os

    def get_image_path(instance, filename):
        return os.path.join('path', 'to', 'my', 'files', filename)

Then set in settings::

    # settings.py:

    from utils import get_image_path

    PHOTOLOGUE_PATH = get_image_path

或相反，傳遞一個字符串路徑::

    # settings.py:

    PHOTOLOGUE_PATH = 'myapp.utils.get_image_path'

.. _settings-photologue-multisite-label:

PHOTOLOGUE_MULTISITE
--------------------

    Default: ``False``

Photologue can integrate galleries and photos with `Django's site framework`_.
Default is for this feature to be switched off, as only a minority of Django projects
will need it.

在這種情況下，新的畫廊和照片會自動鏈接到當前網站
（``SITE_ID = 1``）。The Sites many-to-many field is hidden is the admin, as there is no
need for a user to see it.

如果設置為``True``，那麼管理界面略有改變：

*本網站的許多一對多字段顯示在畫廊和照片模式。
*畫廊上傳允許你多了一個網站的上傳的照片（和畫廊）相關聯。

.. note:: Gallery Uploads (zip archives) are always associated with the current site. Pull requests to
   fix this would be welcome!

.._Django的站點框架：http://django.readthedocs.org/en/latest/ref/contrib/sites.html