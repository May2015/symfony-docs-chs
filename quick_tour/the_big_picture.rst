概述
====

花10分钟上手Symfony：本文将向你介绍Symfony几个最重要的概念，并用一个简单的例子来演示如何基于Symfony进行Web开发。

如果你之前使用过其他的Web开发框架（译注：如RoR、Django、Spring等），那你对Symfony的开发风格一定不会感到陌生。

在阅读本文之前，请确保你的系统上安装了 **5.4及以上版本的PHP** 。如果你用的是WAMP、XAMP或MAMP之类的一键安装包，也请确认其捆绑的PHP版本是否足够新。你可以用下面的命令行指令来检查PHP的版本：

.. code-block:: bash

    $ php --version

.. _installing-symfony2:

安装Symfony
-----------

过去你需要为每个项目分别手动安装一次Symfony；现在， 有了 **Symfony安装工具** ，安装过程变得更加轻松了。

在 **Linux** 或者 **Mac OS X** 系统上，你可以运行下面的命令行指令来获得Symfony安装工具：

.. code-block:: bash

    $ curl -LsS http://symfony.com/installer > symfony.phar
    $ sudo mv symfony.phar /usr/local/bin/symfony

.. note::

    如果你的系统上没有安装cURL，你可以改用下面的指令：

    .. code-block:: bash

        $ php -r "readfile('http://symfony.com/installer');" > symfony.phar
        $ sudo mv symfony.phar /usr/local/bin/symfony

指令成功运行后，你（可能）需要进入新打开的命令行窗口，以使 ``symfony`` 命令生效。

.. code-block:: bash

    $ symfony

在 **Windows** 系统上，运行下面的指令即可：

.. code-block:: bash

    c:\> php -r "readfile('http://symfony.com/installer');" > symfony.phar

以上指令将下载一个名为 ``symfony.phar`` 的文件，这个文件包含了Symfony安装工具。把这个文件移动到你打算创建Symfony项目的目录，比如，在C盘根目录下使用这个工具，则调用方式为：

.. code-block:: bash

    c:\> php symfony.phar

创建你的第一个Symfony项目
-------------------------

确认Symfony安装工具能运行了，你就可以使用其提供的 ``new`` 选项来创建新的Symfony项目。我们下面把示例项目命名为 ``myproject`` ：

.. code-block:: bash

    # Linux 或 Mac OS X
    $ symfony new myproject

    # Windows
    c:\> php symfony.phar new myproject

上述指令将下载最新稳定版的Symfony，并在 ``myproject/`` 文件夹里初始化一个空的项目——好了，你可以开始写代码了！

.. _running-symfony2:

运行Symfony
-----------

本文介绍的，是使用PHP自带的Web服务器来运行Symfony。你只需要进入Symfony项目代码所在的目录，执行下面的指令：

.. code-block:: bash

    $ cd myproject/
    $ php app/console server:run

然后打开浏览器，访问 ``http://localhost:8000`` ，你将能看到Symfony的欢迎页：

.. image:: /images/quick_tour/welcome.png
   :align: center
   :alt:   Symfony Welcome Page

热烈祝贺！你的第一个Symfony项目运行起来了！

.. note::

    如果你看到的是空白页或错误信息页，而不是上图所示的欢迎页，则很有可能是文件权限不正确。
    针对部分操作系统，我们提供了解决这个问题的办法：
    :ref:`Setting up Permissions <book-installation-permissions>`

关闭这个Web服务非常简单：

.. code-block:: bash

    $ php app/console server:stop

.. tip::

    如果你偏好通过Apache或Nginx这类专门的Web服务器来运行Symfony，你可以参考：
    :doc:`/cookbook/configuration/web_server_configuration` 。

了解基本概念
------------

使用开发框架的主要目的之一是更好地组织代码，避免数据库操作、HTML和业务逻辑等等不同方面的代码混在一起。要使用Symfony来达成这个目标，你首先应了解一些基本的概念。

开发Symfony项目，程序员所做的，就是把用户的 *请求* （比如，访问欢迎页的地址： ``http://localhost:8000/`` ）指向与其相关的 *资源* （比如，欢迎页的内容）。

用来响应用户请求的目标代码，称作 **动作** 和 **控制器** 。而将目标代码与用户的请求对应起来的，是被称为 **路由规则** 的配置。最终显示在浏览器里的内容，一般是通过 **模板** 来生成的（渲染）。

当你访问 ``http://localhost:8000/`` 时，Symfony执行了在 ``src/AppBundle/Controller/DefaultController.php`` 文件里定义的控制器，并输出了由 ``app/Resources/views/default/index.html.twig`` 模板生成的内容。接下来，你将了解到更多关于Symfony控制器、路由、模板的细节：

动作与控制器
~~~~~~~~~~~~

打开 ``src/AppBundle/Controller/DefaultController.php`` 文件，你会看到如下的代码（暂时不必关心 ``@Route`` 这个配置，后面会再讲到）： ::

    namespace AppBundle\Controller;

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class DefaultController extends Controller
    {
        /**
         * @Route("/", name="homepage")
         */
        public function indexAction()
        {
            return $this->render('default/index.html.twig');
        }
    }

在Symfony项目里， **控制器** 一般被实现为命名里包含 ``Controller`` 后缀的PHP类。在这个例子里，控制器的名字是 ``Default`` ，PHP类则命名为 ``DefaultController`` 。

控制器类的成员方法，称作 **动作** ，这些方法一般与项目的某个URL对应，方法名以 ``Action`` 为后缀。例子里， ``Default`` 控制器里只以 ``indexAction`` 方法定义了一个名为 ``index`` 的动作。

一般情况下，“动作”的代码行数不多（10～15行左右），因为“动作”的作用就像粘合剂一样，是通过调用项目里其他部分的代码，来获得所需的数据，并最终按模板来生成返回给用户的内容。

再回到上面的例子，因为并不需要调用项目里的其他代码， ``index`` 动作只有一行代码。而这仅有的代码，所做的就是按模板生成欢迎页。

路由
~~~~

Symfony将用户所请求的URL与路由配置进行匹配，并把用户的请求指向与之相应的动作代码。你在
``src/AppBundle/Controller/DefaultController.php`` 文件里可以看到 ``indexAction`` 方法前面有3行代码：

.. code-block:: php

    // src/AppBundle/Controller/DefaultController.php
    namespace AppBundle\Controller;

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class DefaultController extends Controller
    {
        /**
         * @Route("/", name="homepage")
         */
        public function indexAction()
        {
            return $this->render('default/index.html.twig');
        }
    }

这3行代码用 ``@Route()`` 注解定义了一个路由配置。 **PHP注解** 是一种为PHP类的成员方法定义配置的方式，你可以把配置写在注释里，而不必写PHP代码。需要注意的是，Symfony约定了注解的开头必须是 ``/**`` ，而常规的PHP注释是以 ``/*`` 开头。

``@Route()`` 注解的第一个参数定义了什么样的URL可以触发动作代码。路由URL都是相对地址，一般称作 *路径* （ *paths* ），因此你不需要写出域名。明显的，例子里的 ``/`` 指的是首页。注解的第二个参数（ ``name="homepage"`` ）定义的是路由的名称，但并不是必须指定的。虽然例子里并没有用到，路由有了名称可以使创建链接变得很方便。

综上， ``@Route("/", name="homepage")`` 注解所创建的路由名称为  ``homepage`` ，其作用是，当用户访问  ``/`` 时，Symfony将运行  ``Default`` 控制器的 ``index`` 动作。

.. tip::

    除了PHP注解，路由还可以用YAML，XML，PHP代码来定义，详情可参考
    `the Routing chapter of the Symfony book`_ 。
    灵活丰富的配置方式，是Symfony的重要特性，使你可以选你所好。

模板
~~~~

``index`` 动作里只有一行PHP代码：

.. code-block:: php

    return $this->render('default/index.html.twig');

Symfony在 ``Controller`` 基类里提供了一些常见操作的便捷方法，``$this->render()`` 就是其中之一，其功能是渲染模板。

Symfony项目里，模板文件的默认路径是 ``app/Resources/views/`` 。因此， ``default/index.html.twig`` 对应的模板文件实际是
``app/Resources/views/default/index.html.twig`` 。打开这个文件，其内容是：

.. code-block:: html+jinja

    {# app/Resources/views/default/index.html.twig #}
    {% extends 'base.html.twig' %}

    {% block body %}
        <h1>Welcome to Symfony!</h1>
    {% endblock %}

这是 `Twig`_ （模板引擎）所支持的模板格式。 :doc:`快速入门的第二部分 </quick_tour/the_view>`
将介绍如何在Symfony里使用Twig模板。

.. _quick-tour-big-picture-environments:

运行环境
--------

现在你对Symfony的运行机制应该有所了解了，请仔细看看欢迎页的底部——没错，那里有个带Symfony logo的工具条。这就是程序员开发Symfony项目时的好伙伴：“Web调试工具条”。

.. image:: /images/quick_tour/web_debug_toolbar.png
   :align: center

好戏还在后头：随便点工具条上任意的按钮，都将进入Symfony的分析页面，分析页面里有更详细的关于用户请求，访问权限，数据库查询等的运行信息：

.. image:: /images/quick_tour/profiler.png
   :align: center

看到这里，你可能会担心了，这么详细的信息，让用户随便访问是不是太不安全了？请放心，因为Symfony在生产环境下并不会显示调试工具条。

那么，Symfony是如何知道你是在本地开发还是部署到公网的生产服务器上了？请继续阅读以了解 **运行环境** 的概念。

.. _quick-tour-big-picture-environments-intro:

什么是运行环境？
~~~~~~~~~~~~~~~~

:term:`运行环境<Environment>` ，简言之就是项目运行所采用的配置。Symfony默认提供了两套配置： ``dev``
（适合用于本地开发）和 ``prod`` （针对在生产环境的运行进行了优化）。

When you visit the ``http://localhost:8000`` URL in your browser, you're executing
your Symfony application in the ``dev`` environment. To visit your application
in the ``prod`` environment, visit the ``http://localhost:8000/app.php`` URL instead.
If you prefer to always show the ``dev`` environment in the URL, you can visit
``http://localhost:8000/app_dev.php`` URL.

The main difference between environments is that ``dev`` is optimized to provide
lots of information to the developer, which means worse application performance.
Meanwhile, ``prod`` is optimized to get the best performance, which means that
debug information is disabled, as well as the Web Debug Toolbar.

The other difference between environments is the configuration options used to
execute the application. When you access the ``dev`` environment, Symfony loads
the ``app/config/config_dev.yml`` configuration file. When you access the ``prod``
environment, Symfony loads ``app/config/config_prod.yml`` file.

Typically, the environments share a large amount of configuration options. For
that reason, you put your common configuration in ``config.yml`` and override
the specific configuration file for each environment where necessary:

.. code-block:: yaml

    # app/config/config_dev.yml
    imports:
        - { resource: config.yml }

    web_profiler:
        toolbar: true
        intercept_redirects: false

In this example, the ``config_dev.yml`` configuration file imports the common
``config.yml`` file and then overrides any existing web debug toolbar configuration
with its own options.

For more details on environments, see
":ref:`Environments & Front Controllers <page-creation-environments>`" article.

Final Thoughts
--------------

Congratulations! You've had your first taste of Symfony code. That wasn't so
hard, was it? There's a lot more to explore, but you should already see how
Symfony makes it really easy to implement web sites better and faster. If you
are eager to learn more about Symfony, dive into the next section:
":doc:`The View <the_view>`".

.. _Composer: https://getcomposer.org/
.. _executable installer: http://getcomposer.org/download
.. _Twig: http://twig.sensiolabs.org/
.. _the Routing chapter of the Symfony book: http://symfony.com/doc/current/book/routing.html
