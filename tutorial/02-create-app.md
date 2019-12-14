<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="bfd6b-101">この演習では、 [Django](https://www.djangoproject.com/)を使用して web アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span> <span data-ttu-id="bfd6b-102">Django がまだインストールされていない場合は、コマンドラインインターフェイス (CLI) から次のコマンドを使用してインストールできます。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

```Shell
pip install Django==3.0
```

<span data-ttu-id="bfd6b-103">CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

```Shell
django-admin startproject graph_tutorial
```

<span data-ttu-id="bfd6b-104">Django は、スキャフォールディングという`graph_tutorial`名前の新しいディレクトリを作成し、Django web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-104">Django creates a new directory called `graph_tutorial` and scaffolds a Django web app.</span></span> <span data-ttu-id="bfd6b-105">この新しいディレクトリに移動し、次のコマンドを入力してローカル web サーバーを開始します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-105">Navigate to this new directory and enter the following command to start a local web server.</span></span>

```Shell
python manage.py runserver
```

<span data-ttu-id="bfd6b-106">ブラウザーを開き、`http://localhost:8000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-106">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="bfd6b-107">すべてが動作している場合は、Django ウェルカムページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-107">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="bfd6b-108">これが表示されない場合は、 [Django 入門ガイド](https://www.djangoproject.com/start/)をご確認ください。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-108">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

<span data-ttu-id="bfd6b-109">プロジェクトの確認が完了したので、プロジェクトにアプリを追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-109">Now that you've verified the project, add an app to the project.</span></span> <span data-ttu-id="bfd6b-110">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-110">Run the following command in your CLI.</span></span>

```Shell
python manage.py startapp tutorial
```

<span data-ttu-id="bfd6b-111">これにより、 `./tutorial`ディレクトリに新しいアプリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-111">This creates a new app in the `./tutorial` directory.</span></span> <span data-ttu-id="bfd6b-112">を開い`./graph_tutorial/settings.py`て、新しい`tutorial`アプリを`INSTALLED_APPS`設定に追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-112">Open the `./graph_tutorial/settings.py` and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tutorial',
]
```

<span data-ttu-id="bfd6b-113">CLI で、次のコマンドを実行してプロジェクトのデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-113">In your CLI, run the following command to initialize the database for the project.</span></span>

```Shell
python manage.py migrate
```

<span data-ttu-id="bfd6b-114">`tutorial`アプリの[urlconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/)を追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-114">Add a [URLconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="bfd6b-115">という名前`./tutorial` `urls.py`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-115">Create a new file in the `./tutorial` directory named `urls.py` and add the following code.</span></span>

```python
from django.urls import path

from . import views

urlpatterns = [
  # /tutorial
  path('', views.home, name='home'),
]
```

<span data-ttu-id="bfd6b-116">これで、プロジェクトの URLconf を更新してインポートします。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-116">Now update the project URLconf to import this one.</span></span> <span data-ttu-id="bfd6b-117">`./graph_tutorial/urls.py`ファイルを開き、内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-117">Open the `./graph_tutorial/urls.py` file and replace the contents with the following.</span></span>

```python
from django.contrib import admin
from django.urls import path, include
from tutorial import views

urlpatterns = [
    path('tutorial/', include('tutorial.urls')),
    path('admin/', admin.site.urls),
]
```

<span data-ttu-id="bfd6b-118">最後に、一時的なビューを`tutorials`アプリに追加して、URL のルーティングが機能していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-118">Finally add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="bfd6b-119">`./tutorial/views.py` ファイルを開き、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-119">Open the `./tutorial/views.py` file and add the following code.</span></span>

```python
from django.shortcuts import render
from django.http import HttpResponse, HttpResponseRedirect

def home(request):
  # Temporary!
  return HttpResponse("Welcome to the tutorial.")
```

<span data-ttu-id="bfd6b-120">すべての変更を保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-120">Save all of your changes and restart the server.</span></span> <span data-ttu-id="bfd6b-121">`http://localhost:8000/tutorial` を参照します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-121">Browse to `http://localhost:8000/tutorial`.</span></span> <span data-ttu-id="bfd6b-122">が表示`Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="bfd6b-122">You should see `Welcome to the tutorial.`</span></span>

<span data-ttu-id="bfd6b-123">に進む前に、後で使用する追加のライブラリをインストールします。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-123">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="bfd6b-124">[要求-OAuthlib:](https://requests-oauthlib.readthedocs.io/en/latest/)サインインおよび oauth トークンフローを処理し、Microsoft Graph への呼び出しを行うための、人間用の oauth。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-124">[Requests-OAuthlib: OAuth for Humans](https://requests-oauthlib.readthedocs.io/en/latest/) for handling sign-in and OAuth token flows, and for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="bfd6b-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) yaml ファイルから構成を読み込むためのものです。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="bfd6b-126">Microsoft Graph から返される ISO 8601 日付文字列を解析するための[python-dateutil](https://pypi.org/project/python-dateutil/) 。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-126">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

<span data-ttu-id="bfd6b-127">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-127">Run the following command in your CLI.</span></span>

```Shell
pip install requests_oauthlib==1.3.0
pip install pyyaml==5.2
pip install python-dateutil==2.8.1
```

## <a name="design-the-app"></a><span data-ttu-id="bfd6b-128">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="bfd6b-128">Design the app</span></span>

<span data-ttu-id="bfd6b-129">最初に、テンプレートディレクトリを作成し、アプリのグローバルレイアウトを定義します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-129">Start by creating a templates directory and defining a global layout for the app.</span></span> <span data-ttu-id="bfd6b-130">という名前`./tutorial` `templates`のディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-130">Create a new directory in the `./tutorial` directory named `templates`.</span></span> <span data-ttu-id="bfd6b-131">`templates`ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-131">In the `templates` directory, create a new directory named `tutorial`.</span></span> <span data-ttu-id="bfd6b-132">最後に、という名前`layout.html`のこのディレクトリに新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-132">Finally, create a new file in this directory named `layout.html`.</span></span> <span data-ttu-id="bfd6b-133">プロジェクトのルートからの相対パスがで`./tutorial/templates/tutorial/layout.html`ある必要があります。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-133">The relative path from the root of your project should be `./tutorial/templates/tutorial/layout.html`.</span></span> <span data-ttu-id="bfd6b-134">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-134">Add the following code in that file.</span></span>

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Python Graph Tutorial</title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
      integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
      integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    {% load static %}
    <link rel="stylesheet" href="{% static "tutorial/app.css" %}">
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
      integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
      integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <a href="{% url 'home' %}" class="navbar-brand">Python Graph Tutorial</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
          aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a href="{% url 'home' %}" class="nav-link{% if request.resolver_match.view_name == 'home' %} active{% endif %}">Home</a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item" data-turbolinks="false">
                <a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>
              </li>
            {% endif %}
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                  {% if user.avatar %}
                    <img src="{{ user.avatar }}" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  {% else %}
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  {% endif %}
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0">{{ user.name }}</h5>
                  <p class="dropdown-item-text text-muted mb-0">{{ user.email }}</p>
                  <div class="dropdown-divider"></div>
                  <a href="#" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            {% else %}
              <li class="nav-item">
                <a href="#" class="nav-link">Sign In</a>
              </li>
            {% endif %}
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      {% if errors %}
        {% for error in errors %}
          <div class="alert alert-danger" role="alert">
            <p class="mb-3">{{ error.message }}</p>
            {% if error.debug %}
              <pre class="alert-pre border bg-light p-2"><code>{{ error.debug }}</code></pre>
            {% endif %}
          </div>
        {% endfor %}
      {% endif %}
      {% block content %}{% endblock %}
    </main>
  </body>
</html>
```

<span data-ttu-id="bfd6b-135">このコードでは、単純なスタイル設定のために[ブートストラップ](http://getbootstrap.com/)が追加さ[れてい](https://fontawesome.com/)ます。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-135">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="bfd6b-136">また、ナビゲーションバーのあるグローバルレイアウトを定義します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-136">It also defines a global layout with a nav bar.</span></span>

<span data-ttu-id="bfd6b-137">で、 `./tutorial`という名前`static`のディレクトリに新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-137">Now create a new directory in the `./tutorial` directory named `static`.</span></span> <span data-ttu-id="bfd6b-138">`static`ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-138">In the `static` directory, create a new directory named `tutorial`.</span></span> <span data-ttu-id="bfd6b-139">最後に、という名前`app.css`のこのディレクトリに新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-139">Finally, create a new file in this directory named `app.css`.</span></span> <span data-ttu-id="bfd6b-140">プロジェクトのルートからの相対パスがで`./tutorial/static/tutorial/app.css`ある必要があります。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-140">The relative path from the root of your project should be `./tutorial/static/tutorial/app.css`.</span></span> <span data-ttu-id="bfd6b-141">そのファイルに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-141">Add the following code in that file.</span></span>

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

<span data-ttu-id="bfd6b-142">次に、そのレイアウトを使用するホームページ用のテンプレートを作成します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-142">Next, create a template for the home page that uses the layout.</span></span> <span data-ttu-id="bfd6b-143">という名前`./tutorial/templates/tutorial` `home.html`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-143">Create a new file in the `./tutorial/templates/tutorial` directory named `home.html` and add the following code.</span></span>

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<div class="jumbotron">
  <h1>Python Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from Python</p>
  {% if user.is_authenticated %}
    <h4>Welcome {{ user.name }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  {% else %}
    <a href="#" class="btn btn-primary btn-large">Click here to sign in</a>
  {% endif %}
</div>
{% endblock %}
```

<span data-ttu-id="bfd6b-144">このテンプレート`home`を使用するようにビューを更新します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-144">Update the `home` view to use this template.</span></span> <span data-ttu-id="bfd6b-145">`./tutorial/views.py`ファイルを開き、次の新しい関数を追加します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-145">Open the `./tutorial/views.py` file and add the following new function.</span></span>

```python
def initialize_context(request):
  context = {}

  # Check for any errors in the session
  error = request.session.pop('flash_error', None)

  if error != None:
    context['errors'] = []
    context['errors'].append(error)

  # Check for user in the session
  context['user'] = request.session.get('user', {'is_authenticated': False})
  return context
```

<span data-ttu-id="bfd6b-146">次に、既存`home`のビューを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-146">Then replace the existing `home` view with the following.</span></span>

```python
def home(request):
  context = initialize_context(request)

  return render(request, 'tutorial/home.html', context)
```

<span data-ttu-id="bfd6b-147">すべての変更を保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-147">Save all of your changes and restart the server.</span></span> <span data-ttu-id="bfd6b-148">この時点で、アプリの外観は大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="bfd6b-148">Now, the app should look very different.</span></span>

![再設計されたホームページのスクリーンショット](./images/create-app-01.png)
