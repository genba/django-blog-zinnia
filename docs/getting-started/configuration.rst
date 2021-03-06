======================
Advanced Configuration
======================

.. _zinnia-sitemaps:

Sitemaps
========

.. module:: zinnia.sitemaps

One of the cool features of Django is the sitemap application, so if you
want to fill your Web site's sitemap with the entries of your blog, follow
these steps.

* Register :mod:`django.contrib.sitemaps` in the :setting:`INSTALLED_APPS` section.
* Edit your project's URLs and add this code: ::

   from zinnia.sitemaps import TagSitemap
   from zinnia.sitemaps import EntrySitemap
   from zinnia.sitemaps import CategorySitemap
   from zinnia.sitemaps import AuthorSitemap

   sitemaps = {'tags': TagSitemap,
               'blog': EntrySitemap,
               'authors': AuthorSitemap,
               'categories': CategorySitemap,}

   urlpatterns += patterns(
       'django.contrib.sitemaps.views',
       url(r'^sitemap.xml$', 'index',
           {'sitemaps': sitemaps}),
       url(r'^sitemap-(?P<section>.+)\.xml$', 'sitemap',
           {'sitemaps': sitemaps}),)

.. _zinnia-templates:

Templates for entries
=====================

In your Weblog you will always publish entries, but sometimes you want to
have a different look and feel for special entries.

You may want to publish an entry with a short content like a quote, in
which case it would be better not to provide a *continue reading* link when
rendering this entry.

To solve this problem, Zinnia allows the user to select a template to
render the entry's content and the entry's detail page.

In order to use a template without the *continue reading* link, we need to
register it under this setting in the project's configuration: ::

  ZINNIA_ENTRY_CONTENT_TEMPLATES = [
    ('zinnia/_short_entry_detail.html', 'Short entry template'),
  ]

Now we will create the ``zinnia/_short_entry_detail.html`` template with
this sample of code:

.. code-block:: html+django

  {% extends "zinnia/_entry_detail.html" %}

  {% block continue-reading %}{% endblock %}

A new template is now available in the admin interface to display the entry
without the *continue reading* link when displayed in a list.

Then if you want to have custom rendering of the detail page of the entry,
by displaying the entry fullwidth without the sidebar for example, the same
process applies. We will add this setting in the project's configuration:
::

  ZINNIA_ENTRY_DETAIL_TEMPLATES = [
      ('zinnia/fullwidth_entry_detail.html', 'Fullwidth template'),
  ]

And now we finally create the ``zinnia/fullwidth_entry_detail.html``
template with this sample of code:

.. code-block:: html+django

  {% extends "zinnia/entry_detail.html" %}

  {% block sidebar-class %}no-sidebar{% endblock %}

  {% block sidebar %}{% endblock %}

.. _zinnia-pinging:

Pinging
=======

By default Zinnia is configured to ping the directories and the external
urls embedded in your entries when a new entry is published.

If you want to completly remove these features, simply set these settings
in your project's configuration: ::

  ZINNIA_PING_EXTERNAL_URLS = False
  ZINNIA_SAVE_PING_DIRECTORIES = False

You can also edit the list of the directories to be pinged by using this
setting: ::

  ZINNIA_PING_DIRECTORIES = ('http://ping.directory.com/',
                             'http://pong.directory.com/')

.. _zinnia-twitter:

Twitter
=======

When you post a new entry on your blog you might want to tweet it as well.

In order to do that, you first need to install `tweepy`_ and add these
settings. ::

  TWITTER_CONSUMER_KEY = 'Your Consumer Key'
  TWITTER_CONSUMER_SECRET = 'Your Consumer Secret'
  TWITTER_ACCESS_KEY = 'Your Access Key'
  TWITTER_ACCESS_SECRET = 'Your Access Secret'

Note that the authentification for Twitter has changed since September 2010.
The actual authentification system is based on oAuth. That's why now you
need to set these 4 settings. If you don't know how to get these information,
follow this excellent tutorial at:

http://jmillerinc.com/2010/05/31/twitter-from-the-command-line-in-python-using-oauth/

Now in the admin, you can post an update containing your entry's title and
the shortened URL of your entry.

.. _zinnia-django-cms:

Django-CMS
==========

If you use `django-CMS`_, Zinnia can be integrated into your pages,
thanks to the plugin system.

.. warning::
   .. versionchanged:: 0.10.1

   ``zinnia.plugins`` has been removed in favor of `cmsplugin_zinnia`_.

Simply refer to `cmsplugin_zinnia`_'s documentation for more information
about the install instructions and possibilities.

.. _zinnia-tinymce:

TinyMCE
=======

If you want to replace WYMEditor by TinyMCE just install `django-tinymce`_
as described in the the `installation instructions`_.

TinyMCE can be customized by overriding the
:file:`admin/zinnia/entry/tinymce_textareas.js` template.

.. _zinnia-markup-languages:

Markup languages
================

If you doesn't want to write your entries in HTML, because you are
an über coder knowing more than 42 programming languages, you have the
possibility to use a custom markup language for editing the entries.

Currently **MarkDown**, **Textile** and **reStructuredText** are supported,
so if you want to use one of these languages, first set this
setting as appropriate in your project's settings. ::

  ZINNIA_MARKUP_LANGUAGE = 'restructuredtext'

Note that the name of the language must be in lowercase.

Then install the corresponding library to your needs:

* ``textile`` -- requires `Textile`_ >= 2.1.5
* ``markdown`` -- requires `Markdown`_ >= 2.3.1
* ``restructuredtext`` -- requires `Docutils`_ >= 0.10

.. _zinnia-xmlrpc:

XML-RPC
=======

.. module:: zinnia.xmlrpc

Zinnia provides few Webservices via XML-RPC, but before using it,
you need to install `django-xmlrpc`_.

Then register :mod:`django_xmlrpc` in your :setting:`INSTALLED_APPS`
section of your project's settings.

Now add these lines in your project's settings. ::

  from zinnia.xmlrpc import ZINNIA_XMLRPC_METHODS
  XMLRPC_METHODS = ZINNIA_XMLRPC_METHODS

:data:`ZINNIA_XMLRPC_METHODS` is a simple list of tuples containing all
the Webservices embedded in Zinnia.

If you only want to use the Pingback service import
:data:`ZINNIA_XMLRPC_PINGBACK`, or if you want you just want to enable the
`MetaWeblog API`_ import :data:`ZINNIA_XMLRPC_METAWEBLOG`.

You can also use your own mixins.

Finally we need to register the URL of the XML-RPC server.
Insert something like this in your project's urls.py: ::

  url(r'^xmlrpc/$', 'django_xmlrpc.views.handle_xmlrpc'),

.. note:: For the Pingback service check if your site is enabled for
          pingback detection.
          More information at http://hixie.ch/specs/pingback/pingback-1.0#TOC2

.. _`akismet`: http://www.voidspace.org.uk/python/modules.shtml#akismet
.. _`TypePad AntiSpam`: http://antispam.typepad.com/
.. _`Mollom`: http://mollom.com/
.. _`PyMollom`: https://github.com/itkovian/PyMollom
.. _`django-bitly`: http://bitbucket.org/discovery/django-bitly/
.. _`tweepy`: https://github.com/tweepy/tweepy
.. _`cmsplugin_zinnia`: https://github.com/Fantomas42/cmsplugin-zinnia
.. _`django-CMS`: http://www.django-cms.org/
.. _`django-tinymce`: https://github.com/aljosa/django-tinymce
.. _`installation instructions`: http://django-tinymce.readthedocs.org/en/latest/installation.html
.. _`Textile`: https://pypi.python.org/pypi/textile
.. _`Markdown`: http://pypi.python.org/pypi/Markdown
.. _`Docutils`: http://docutils.sf.net/
.. _`django-xmlrpc`: http://pypi.python.org/pypi/django-xmlrpc/
.. _`MetaWeblog API`: http://www.xmlrpc.com/metaWeblogApi
