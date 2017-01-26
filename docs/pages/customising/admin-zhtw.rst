
.. _customisation-admin-label:

####################
Customisation: Admin
####################

該Photologue管理員可以很容易地進行定製，以符合您項目的要求。技巧描述在本頁中。 
不是特定於Photologue - 它可以被應用到任何第三方庫。

創建一個定制的應用程序
----------------------------------
為了清楚起見，最好把我們的自定義代碼放在一個新的應用中;讓我們叫它
``photologue_custom``;創建應用程序並將其添加到您的``INSTALLED_APPS``setting。


Changing the admin
------------------
在新的``photologue_custom``應用程序，創建一個新的空``admin.py``文件。在這個文件中，我們
可取代Photologue提供的管理配置，具體到項目的配置。
例如：

.. code-block:: python

    from django import forms
    from django.contrib import admin

    from photologue.admin import GalleryAdmin as GalleryAdminDefault
    from photologue.models import Gallery


    class GalleryAdminForm(forms.ModelForm):
        """Users never need to enter a description on a gallery."""

        class Meta:
            model = Gallery
            exclude = ['description']


    class GalleryAdmin(GalleryAdminDefault):
        form = GalleryAdminForm

    admin.site.unregister(Gallery)
    admin.site.register(Gallery, GalleryAdmin)


這個片段將根據Photologue的定義以定義一個新的畫廊管理類。我們進行的唯一更改
是從變化形式排除``description``字段。

然後，我們註銷了畫廊模式默認的admin，並利用我們新類替換它。

可能的使用
-------------

上面概括的技術可以被用來製造到管理許多變化;這裡有幾個建議。

Custom rich text editors
~~~~~~~~~~~~~~~~~~~~~~~~
在畫廊模式（和照片模式標題字段）的描述字段是純文本字段。
通過上述方法，可以很容易地使用富文本編輯器來管理字段在admin中。舉例來說：
假如你已安裝 `django-ckeditor <https://github.com/shaunsephton/django-ckeditor>`_ installed:

.. code-block:: python

    from django import forms
    from django.contrib import admin

    from ckeditor.widgets import CKEditorWidget
    from photologue.admin import GalleryAdmin as GalleryAdminDefault
    from photologue.models import Gallery


    class GalleryAdminForm(forms.ModelForm):
        """Replace the default description field, with one that uses a custom widget."""

        description = forms.CharField(widget=CKEditorWidget())

        class Meta:
            model = Gallery
            exclude = ['']


    class GalleryAdmin(GalleryAdminDefault):
        form = GalleryAdminForm

    admin.site.unregister(Gallery)
    admin.site.register(Gallery, GalleryAdmin)