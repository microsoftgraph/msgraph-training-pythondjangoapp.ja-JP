<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="48f7e-101">この演習では [、Django を使用して](https://www.djangoproject.com/) Web アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="48f7e-102">Django をまだインストールしていない場合は、次のコマンドを使用してコマンド ライン インターフェイス (CLI) からインストールできます。</span><span class="sxs-lookup"><span data-stu-id="48f7e-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install --user Django==3.1.4
    ```

1. <span data-ttu-id="48f7e-103">CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="48f7e-104">graph_tutorial ディレクトリ **に移動** し、次のコマンドを入力してローカル Web サーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="48f7e-105">ブラウザーを開き、`http://localhost:8000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="48f7e-106">すべてが動作している場合は、Django のウェルカム ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="48f7e-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="48f7e-107">見当たらない場合は [、Django の開始ガイドをご覧ください](https://www.djangoproject.com/start/)。</span><span class="sxs-lookup"><span data-stu-id="48f7e-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="48f7e-108">アプリをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-108">Add an app to the project.</span></span> <span data-ttu-id="48f7e-109">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="48f7e-110">**./graph_tutorial/settings.py を** 開き、新しい `tutorial` アプリを設定に追加 `INSTALLED_APPS` します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="48f7e-111">CLI で次のコマンドを実行して、プロジェクトのデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="48f7e-112">アプリの [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) を追加 `tutorial` します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="48f7e-113">./tutorial ディレクトリに新しい **ファイルを作成** し、 `urls.py` 次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="48f7e-114">プロジェクト URLconf を更新して、これをインポートします。</span><span class="sxs-lookup"><span data-stu-id="48f7e-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="48f7e-115">**./graph_tutorial/urls.py** を開き、内容を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="48f7e-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="48f7e-116">アプリに一時的なビューを `tutorials` 追加して、URL ルーティングが機能しているのを確認します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="48f7e-117">**./tutorial/views.py を開き**、その内容全体を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="48f7e-117">Open **./tutorial/views.py** and replace its entire contents with the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta
    from dateutil import tz, parser

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="48f7e-118">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="48f7e-119">`http://localhost:8000` を参照します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="48f7e-120">表示される `Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="48f7e-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="48f7e-121">ライブラリをインストールする</span><span class="sxs-lookup"><span data-stu-id="48f7e-121">Install libraries</span></span>

<span data-ttu-id="48f7e-122">次に進む前に、後で使用する追加のライブラリをインストールします。</span><span class="sxs-lookup"><span data-stu-id="48f7e-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="48f7e-123">サインインおよび OAuth トークン フローを処理する Python 用 Microsoft 認証ライブラリ[(MSAL)。](https://github.com/AzureAD/microsoft-authentication-library-for-python)</span><span class="sxs-lookup"><span data-stu-id="48f7e-123">[Microsoft Authentication Library (MSAL) for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="48f7e-124">[要求: Microsoft Graph を呼び](https://requests.readthedocs.io/en/master/) 出す人間向け HTTP。</span><span class="sxs-lookup"><span data-stu-id="48f7e-124">[Requests: HTTP for Humans](https://requests.readthedocs.io/en/master/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="48f7e-125">[YAML ファイルから](https://pyyaml.org/wiki/PyYAMLDocumentation) 構成を読み込む PyYAML。</span><span class="sxs-lookup"><span data-stu-id="48f7e-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="48f7e-126">Microsoft Graph から返される ISO 8601 日付文字列を解析する[python-dateutil。](https://pypi.org/project/python-dateutil/)</span><span class="sxs-lookup"><span data-stu-id="48f7e-126">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="48f7e-127">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-127">Run the following command in your CLI.</span></span>

    ```Shell
    pip install msal==1.7.0
    pip install requests==2.25.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="48f7e-128">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="48f7e-128">Design the app</span></span>

1. <span data-ttu-id="48f7e-129">./tutorial ディレクトリに新 **しいディレクトリを作成** します `templates` 。</span><span class="sxs-lookup"><span data-stu-id="48f7e-129">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="48f7e-130">**./tutorial/templates** ディレクトリで、新しいディレクトリを作成します `tutorial` 。</span><span class="sxs-lookup"><span data-stu-id="48f7e-130">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="48f7e-131">**./tutorial/templates/tutorial** ディレクトリで、新しいファイルを作成します `layout.html` 。</span><span class="sxs-lookup"><span data-stu-id="48f7e-131">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="48f7e-132">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-132">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="48f7e-133">このコードは、 [シンプルなスタイル設定用の Bootstrap](http://getbootstrap.com/) と、いくつかの単純なアイコン用の Fabric [Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) を追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-133">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="48f7e-134">また、ナビゲーション バーを含むグローバル レイアウトも定義します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-134">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="48f7e-135">./tutorial ディレクトリに新 **しいディレクトリを作成** します `static` 。</span><span class="sxs-lookup"><span data-stu-id="48f7e-135">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="48f7e-136">**./tutorial/static ディレクトリで**、新しいディレクトリを作成します `tutorial` 。</span><span class="sxs-lookup"><span data-stu-id="48f7e-136">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="48f7e-137">**./tutorial/static/tutorial ディレクトリ** で、新しいファイルを作成します `app.css` 。</span><span class="sxs-lookup"><span data-stu-id="48f7e-137">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="48f7e-138">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-138">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="48f7e-139">レイアウトを使用するホーム ページのテンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-139">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="48f7e-140">**./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し、次 `home.html` のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-140">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="48f7e-141">ファイルを `./tutorial/views.py` 開き、次の新しい関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-141">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="48f7e-142">既存のビューを `home` 次のビューに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="48f7e-142">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="48f7e-143">**./tutorial/static/tutorial** ディレクトリに **no-profile-photo.png** という名前の PNG ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-143">Add a PNG file named **no-profile-photo.png** in the **./tutorial/static/tutorial** directory.</span></span>

1. <span data-ttu-id="48f7e-144">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="48f7e-144">Save all of your changes and restart the server.</span></span> <span data-ttu-id="48f7e-145">これで、アプリは非常に異なって表示されます。</span><span class="sxs-lookup"><span data-stu-id="48f7e-145">Now, the app should look very different.</span></span>

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
