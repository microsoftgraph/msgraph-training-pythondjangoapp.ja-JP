<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c5f3f-101">この演習では、 [Django](https://www.djangoproject.com/)を使用して web アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="c5f3f-102">Django がまだインストールされていない場合は、コマンドラインインターフェイス (CLI) から次のコマンドを使用してインストールできます。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install Django==3.0.4
    ```

1. <span data-ttu-id="c5f3f-103">CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="c5f3f-104">**Graph_tutorial**ディレクトリに移動し、次のコマンドを入力してローカル web サーバーを開始します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="c5f3f-105">ブラウザーを開き、`http://localhost:8000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="c5f3f-106">すべてが動作している場合は、Django ウェルカムページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="c5f3f-107">これが表示されない場合は、 [Django 入門ガイド](https://www.djangoproject.com/start/)をご確認ください。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="c5f3f-108">プロジェクトにアプリを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-108">Add an app to the project.</span></span> <span data-ttu-id="c5f3f-109">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="c5f3f-110">**/Graph_tutorial/settings.py**を開いて、新しい`tutorial`アプリを`INSTALLED_APPS`設定に追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="c5f3f-111">CLI で、次のコマンドを実行してプロジェクトのデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="c5f3f-112">`tutorial`アプリの[urlconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/)を追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="c5f3f-113">という名前`urls.py`の**チュートリアル**のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

    ```python
    from django.urls import path

    from . import views

    urlpatterns = [
      # /
      path('', views.home, name='home'),
      # TEMPORARY
      path('signin', views.home, name='signin'),
      path('signout', views.home, name='signout'),
      path('calendar', views.home, name='calendar'),
    ]
    ```

1. <span data-ttu-id="c5f3f-114">このプロジェクトをインポートするには、プロジェクトの URLconf を更新します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="c5f3f-115">**/Graph_tutorial/urls.py**を開き、内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="c5f3f-116">`tutorials`アプリに一時的なビューを追加して、URL のルーティングが機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="c5f3f-117">**/Tutorial/views.py**を開き、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-117">Open **./tutorial/views.py** and add the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="c5f3f-118">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="c5f3f-119">`http://localhost:8000` を参照します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="c5f3f-120">が表示`Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="c5f3f-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="c5f3f-121">ライブラリをインストールする</span><span class="sxs-lookup"><span data-stu-id="c5f3f-121">Install libraries</span></span>

<span data-ttu-id="c5f3f-122">に進む前に、後で使用する追加のライブラリをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="c5f3f-123">[要求-OAuthlib:](https://requests-oauthlib.readthedocs.io/en/latest/)サインインおよび oauth トークンフローを処理し、Microsoft Graph への呼び出しを行うための、人間用の oauth。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-123">[Requests-OAuthlib: OAuth for Humans](https://requests-oauthlib.readthedocs.io/en/latest/) for handling sign-in and OAuth token flows, and for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="c5f3f-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) yaml ファイルから構成を読み込むためのものです。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="c5f3f-125">Microsoft Graph から返される ISO 8601 日付文字列を解析するための[python-dateutil](https://pypi.org/project/python-dateutil/) 。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-125">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="c5f3f-126">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-126">Run the following command in your CLI.</span></span>

    ```Shell
    pip install requests_oauthlib==1.3.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="c5f3f-127">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="c5f3f-127">Design the app</span></span>

1. <span data-ttu-id="c5f3f-128">という名前`templates`の**チュートリアル**ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-128">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="c5f3f-129">**./Tutorial/templates**ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-129">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="c5f3f-130">**./Tutorial/templates/tutorial**ディレクトリに、という名前`layout.html`の新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-130">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="c5f3f-131">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-131">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="c5f3f-132">このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-132">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="c5f3f-133">また、ナビゲーションバーのあるグローバルレイアウトを定義します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-133">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="c5f3f-134">という名前`static`の**チュートリアル**ディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-134">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="c5f3f-135">**./Tutorial/static**ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-135">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="c5f3f-136">**./Tutorial/static/tutorial**ディレクトリに、という名前`app.css`の新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-136">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="c5f3f-137">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-137">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="c5f3f-138">レイアウトを使用するホームページ用のテンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-138">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="c5f3f-139">という名前`home.html`の **/tutorial/templates/tutorial**ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-139">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="c5f3f-140">`./tutorial/views.py`ファイルを開き、次の新しい関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-140">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="c5f3f-141">既存`home`のビューを次のものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-141">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="c5f3f-142">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-142">Save all of your changes and restart the server.</span></span> <span data-ttu-id="c5f3f-143">この時点で、アプリの外観は大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="c5f3f-143">Now, the app should look very different.</span></span>

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
