
.. _customisation-views-label:

#############################
客製化: Views and Urls
#############################

photologue視圖和URL可以調整，以更適合您的項目。技巧描述在本頁中。 
不是特定於Photologue - 它可以被應用到任何第三方庫。

創建一個定制的應用程序
----------------------------------
為了清楚起見，最好把我們的自定義代碼放在一個新的應用中;讓我們叫它
``photologue_custom``;創建應用程序並將其添加到您的``INSTALLED_APPS``setting。

我們也將要自定義的網址：

1創建urls.py以包含我們的自定義的網址：


.. code-block:: python

    from django.conf.urls import *

    urlpatterns = [
        # Nothing to see here... for now.
    ]


2.這些自定義的URL將覆蓋原本的Photologue的URL，所以只要把它們放在Photologue之前
在項目的主urls.py文件：

.. code-block:: python

    ... other code
    (r'^photologue/', include('photologue_custom.urls')),
    url(r'^photologue/', include('photologue.urls', namespace='photologue')),

    ... other code

現在，我們準備做出一些改變。

從我們的新urls.py更改分頁
----------------------------------------

Photologue的列表頁面（包括畫廊和照片）每頁顯示20個物件。讓我們改變這個值。
編輯我們新的urls.py文件，並添加：


.. code-block:: python

    from django.conf.urls import *

    from photologue.views import GalleryListView
    
    urlpatterns = [
        url(r'^gallerylist/$',
            GalleryListView.as_view(paginate_by=5),
            name='photologue_custom-gallery-list'),
    ]


我們已經複製了URLPATTERN為
`從Photologue本身圖庫列表視圖<https://github.com/jdriscoll/django-photologue/blob/master/photologue/urls.py>`_，
略通過傳遞``paginate_by = 5``改變了它。

這就是它 - 現在當請求該頁面，我們定制的urls.py將首先被調用，以分頁
設置為5項。

可以從urls.py覆蓋值
------------------------------------------

GalleryListView
~~~~~~~~~~~~~~~

* paginate_by：每頁顯示的項目數。

PhotoListView
~~~~~~~~~~~~~

* paginate_by：每頁顯示的項目數。

更改views.py創建一個RESTful API
-----------------------------------------
較大幅度的定制可以通過編寫自定義視圖進行。舉例來說：
很容易改變一個Photologue視圖返回JSON對象，而不是HTML網頁。為了這
快速演示，我們將使用
`django-braces library <http://django-braces.readthedocs.org/en/latest/index.html>`_
重寫返回所有照片的列表視圖。

添加以下代碼在``photologue_custom``到views.py中：

.. code-block:: python

    from photologue.views import PhotoListView

    from braces.views import JSONResponseMixin


    class PhotoJSONListView(JSONResponseMixin, PhotoListView):

        def render_to_response(self, context, **response_kwargs):
            return self.render_json_object_response(context['object_list'],
                                                    **response_kwargs)

並呼籲從urls.py這個新的觀點;在這裡，我們正在取代由Photologue提供的標準照片列表頁面：

.. code-block:: python

    from .views import PhotoJSONListView

    urlpatterns = [
        # Other urls...
        url(r'^photolist/$',
            PhotoJSONListView.as_view(),
            name='photologue_custom-photo-json-list'),
        # Other urls as required...
    ]


謝謝收看當然，這是一個簡單的演示和一個真正的RESTful API會相當複雜。