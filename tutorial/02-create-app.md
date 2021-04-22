<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8d08f-101">この演習では [、Django を使用して](https://www.djangoproject.com/) Web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="8d08f-102">Django がインストールされていない場合は、次のコマンドを使用してコマンド ライン インターフェイス (CLI) からインストールできます。</span><span class="sxs-lookup"><span data-stu-id="8d08f-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install Django==3.2
    ```

1. <span data-ttu-id="8d08f-103">CLI を開き、ファイルを作成する権限を持つディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="8d08f-104">ディレクトリに移動 **graph_tutorial、** 次のコマンドを入力してローカル Web サーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="8d08f-105">ブラウザーを開き、`http://localhost:8000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="8d08f-106">すべてが機能している場合は、Django ウェルカム ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8d08f-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="8d08f-107">表示しない場合は [、Django](https://www.djangoproject.com/start/)の開始ガイドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="8d08f-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="8d08f-108">プロジェクトにアプリを追加します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-108">Add an app to the project.</span></span> <span data-ttu-id="8d08f-109">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="8d08f-110">**./graph_tutorial/settings.py を** 開き、新しい `tutorial` アプリを設定に追加 `INSTALLED_APPS` します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="8d08f-111">CLI で次のコマンドを実行して、プロジェクトのデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="8d08f-112">アプリの [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) を追加 `tutorial` します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="8d08f-113">という名前の **./tutorial ディレクトリに新しいファイルを作成** `urls.py` し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="8d08f-114">プロジェクト URLconf を更新して、この URLconf をインポートします。</span><span class="sxs-lookup"><span data-stu-id="8d08f-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="8d08f-115">**./graph_tutorial/urls.py** を開き、内容を次に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="8d08f-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="8d08f-116">アプリに一時的なビューを `tutorials` 追加して、URL ルーティングが機能しているのを確認します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="8d08f-117">**./tutorial/views.py を開き**、その内容全体を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8d08f-117">Open **./tutorial/views.py** and replace its entire contents with the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="8d08f-118">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="8d08f-119">`http://localhost:8000` を参照します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="8d08f-120">表示される `Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="8d08f-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="8d08f-121">ライブラリをインストールする</span><span class="sxs-lookup"><span data-stu-id="8d08f-121">Install libraries</span></span>

<span data-ttu-id="8d08f-122">次に進む前に、後で使用する追加のライブラリをインストールします。</span><span class="sxs-lookup"><span data-stu-id="8d08f-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="8d08f-123">[サインインおよび OAuth トークン](https://github.com/AzureAD/microsoft-authentication-library-for-python) フローを処理する Python の Microsoft 認証ライブラリ (MSAL)。</span><span class="sxs-lookup"><span data-stu-id="8d08f-123">[Microsoft Authentication Library (MSAL) for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="8d08f-124">[YAML ファイルから](https://pyyaml.org/wiki/PyYAMLDocumentation) 構成を読み込む PyYAML。</span><span class="sxs-lookup"><span data-stu-id="8d08f-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="8d08f-125">Microsoft Graph から返される ISO 8601 日付文字列を解析する[python-dateutil。](https://pypi.org/project/python-dateutil/)</span><span class="sxs-lookup"><span data-stu-id="8d08f-125">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="8d08f-126">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-126">Run the following command in your CLI.</span></span>

    ```Shell
    pip install msal==1.10.0
    pip install pyyaml==5.4.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="8d08f-127">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="8d08f-127">Design the app</span></span>

1. <span data-ttu-id="8d08f-128">という名前の **./tutorial ディレクトリに新しいディレクトリを** 作成します `templates` 。</span><span class="sxs-lookup"><span data-stu-id="8d08f-128">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="8d08f-129">**./tutorial/templates** ディレクトリで、という名前の新しいディレクトリを作成します `tutorial` 。</span><span class="sxs-lookup"><span data-stu-id="8d08f-129">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="8d08f-130">**./tutorial/templates/tutorial ディレクトリ** で、という名前の新しいファイルを作成します `layout.html` 。</span><span class="sxs-lookup"><span data-stu-id="8d08f-130">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="8d08f-131">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-131">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="8d08f-132">このコードでは、 [単純なスタイル](http://getbootstrap.com/) 設定用のブートストラップと、いくつかの簡単なアイコン [の Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) を追加します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-132">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="8d08f-133">また、ナビゲーション バーを持つグローバル レイアウトも定義します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-133">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="8d08f-134">という名前の **./tutorial ディレクトリに新しいディレクトリを** 作成します `static` 。</span><span class="sxs-lookup"><span data-stu-id="8d08f-134">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="8d08f-135">**./tutorial/static ディレクトリで**、という名前の新しいディレクトリを作成します `tutorial` 。</span><span class="sxs-lookup"><span data-stu-id="8d08f-135">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="8d08f-136">**./tutorial/static/tutorial ディレクトリ** で、という名前の新しいファイルを作成します `app.css` 。</span><span class="sxs-lookup"><span data-stu-id="8d08f-136">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="8d08f-137">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-137">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="8d08f-138">レイアウトを使用するホーム ページのテンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-138">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="8d08f-139">という名前の **./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し `home.html` 、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-139">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="8d08f-140">ファイルを開 `./tutorial/views.py` き、次の新しい関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-140">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="8d08f-141">既存のビューを `home` 次のビューに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8d08f-141">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="8d08f-142">**./tutorial/static/tutorial ディレクトリにno-profile-photo.png\*\*\*\*という名前の PNG ファイルを追加** します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-142">Add a PNG file named **no-profile-photo.png** in the **./tutorial/static/tutorial** directory.</span></span>

1. <span data-ttu-id="8d08f-143">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="8d08f-143">Save all of your changes and restart the server.</span></span> <span data-ttu-id="8d08f-144">これで、アプリは非常に異なって見える必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d08f-144">Now, the app should look very different.</span></span>

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
