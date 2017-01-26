
.. _customising-models-label:

#####################
客製化：模型
#####################

photologue模型可被擴展以更加滿足您的項目。
此頁上記載的技術不是特定於Photologue - 它可以被應用到任何第三方庫。

Photologue內的模型不能直接修改（不像，例如，Django自己的用戶模型）。
這樣的決定背後有許多的原因，其中包括：

- 如果一個項目中的代碼直接修改Photologue模型的領域，它留下曖昧狀態的Photologue架構遷移。
- 同樣，模型方法不再被信任其行為如預期（因為它們所依賴的字段可能會被覆蓋）。

但是，創建新模型的一對一的關係是容易的，對Photologue自己的``Gallery``和``Photo`模。

在這個頁面，我們將告訴你如何添加標籤到``Gallery``模型。為此，我們將使用流行的第三方應用程序`Django的taggit <https://github.com/alex/django-taggit>`。

.. note::

該``Gallery``和``Photo``模型目前有標籤領域，但這些都是基於放棄的`django-tagging<https://github.com/brosner/django-tagging>`應用。相反，標籤被完全來自Photologue刪除，因為它是一個畫廊的應用程序的非核心功能，並很容易地添加回來 - 如這頁顯示！

創建一個定制的應用程序
----------------------------------
為了清楚起見，最好把我們的自定義代碼在放一個新的應用;讓我們把它叫做``photologue_custom``;創建應用程序並將其添加到您的``INSTALLED_APPS``設置。

擴展
---------

``photologue_custom``應用程序內，我們將編輯2個文件：

Models.py
~~~~~~~~~

.. code-block:: python

    from django.db import models

    from taggit.managers import TaggableManager

    from photologue.models import Gallery


    class GalleryExtended(models.Model):

        # Link back to Photologue's Gallery model.
        gallery = models.OneToOneField(Gallery, related_name='extended')

        # This is the important bit - where we add in the tags.
        tags = TaggableManager(blank=True)

        # Boilerplate code to make a prettier display in the admin interface.
        class Meta:
            verbose_name = u'Extra fields'
            verbose_name_plural = u'Extra fields'

        def __str__(self):
            return self.gallery.title


Admin.py
~~~~~~~~

.. code-block:: python

    from django.contrib import admin

    from photologue.admin import GalleryAdmin as GalleryAdminDefault
    from photologue.models import Gallery
    from .models import GalleryExtended


    class GalleryExtendedInline(admin.StackedInline):
        model = GalleryExtended
        can_delete = False


    class GalleryAdmin(GalleryAdminDefault):

        """Define our new one-to-one model as an inline of Photologue's Gallery model."""

        inlines = [GalleryExtendedInline, ]

    admin.site.unregister(Gallery)
    admin.site.register(Gallery, GalleryAdmin)

上面的代碼就足以開始在管理界面中輸入標籤。要使用/在前端顯示出來，您還需要重寫Photologue自己的模板 - 由於模板很可能是高度定制為您的特定項目，不包括在這裡的例子。