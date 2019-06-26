<!-- markdownlint-disable MD002 MD041 -->

この演習では、 [Django](https://www.djangoproject.com/)を使用して web アプリを構築します。 Django がまだインストールされていない場合は、コマンドラインインターフェイス (CLI) から次のコマンドを使用してインストールできます。

```Shell
pip install Django=2.2.2
```

CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。

```Shell
django-admin startproject graph_tutorial
```

Django は、スキャフォールディングという`graph_tutorial`名前の新しいディレクトリを作成し、Django web アプリを作成します。 この新しいディレクトリに移動し、次のコマンドを入力してローカル web サーバーを開始します。

```Shell
python manage.py runserver
```

ブラウザーを開き、`http://localhost:8000` に移動します。 すべてが動作している場合は、Django ウェルカムページが表示されます。 これが表示されない場合は、 [Django 入門ガイド](https://www.djangoproject.com/start/)をご確認ください。

プロジェクトの確認が完了したので、プロジェクトにアプリを追加します。 CLI で次のコマンドを実行します。

```Shell
python manage.py startapp tutorial
```

これにより、 `./tutorial`ディレクトリに新しいアプリが作成されます。 を開い`./graph_tutorial/settings.py`て、新しい`tutorial`アプリを`INSTALLED_APPS`設定に追加します。

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

CLI で、次のコマンドを実行してプロジェクトのデータベースを初期化します。

```Shell
python manage.py migrate
```

`tutorial`アプリの[urlconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/)を追加します。 という名前`./tutorial` `urls.py`のディレクトリに新しいファイルを作成し、次のコードを追加します。

```python
from django.urls import path

from . import views

urlpatterns = [
  # /tutorial
  path('', views.home, name='home'),
]
```

これで、プロジェクトの URLconf を更新してインポートします。 `./graph_tutorial/urls.py`ファイルを開き、内容を次のように置き換えます。

```python
from django.contrib import admin
from django.urls import path, include
from tutorial import views

urlpatterns = [
    path('tutorial/', include('tutorial.urls')),
    path('admin/', admin.site.urls),
]
```

最後に、一時的なビューを`tutorials`アプリに追加して、URL のルーティングが機能していることを確認します。 `./tutorial/views.py` ファイルを開き、次のコードを追加します。

```python
from django.http import HttpResponse, HttpResponseRedirect

def home(request):
  # Temporary!
  return HttpResponse("Welcome to the tutorial.")
```

すべての変更を保存し、サーバーを再起動します。 `http://localhost:8000/tutorial` を参照します。 が表示`Welcome to the tutorial.`

に進む前に、後で使用する追加のライブラリをインストールします。

- [要求-OAuthlib:](https://requests-oauthlib.readthedocs.io/en/latest/)サインインおよび oauth トークンフローを処理し、Microsoft Graph への呼び出しを行うための、人間用の oauth。
- [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) yaml ファイルから構成を読み込むためのものです。
- Microsoft Graph から返される ISO 8601 日付文字列を解析するための[python-dateutil](https://pypi.org/project/python-dateutil/) 。

CLI で次のコマンドを実行します。

```Shell
pip install requests_oauthlib==1.2.0
pip install pyyaml==5.1
pip install python-dateutil==2.8.0
```

## <a name="design-the-app"></a>アプリを設計する

最初に、テンプレートディレクトリを作成し、アプリのグローバルレイアウトを定義します。 という名前`./tutorial` `templates`のディレクトリに新しいディレクトリを作成します。 `templates`ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。 最後に、という名前`layout.html`のこのディレクトリに新しいファイルを作成します。 プロジェクトのルートからの相対パスがで`./tutorial/templates/tutorial/layout.html`ある必要があります。 そのファイルに次のコードを追加します。

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

このコードでは、単純なスタイル設定[](https://fontawesome.com/)のために[ブートストラップ](http://getbootstrap.com/)が追加されています。 また、ナビゲーションバーのあるグローバルレイアウトを定義します。

で、 `./tutorial`という名前`static`のディレクトリに新しいディレクトリを作成します。 `static`ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。 最後に、という名前`app.css`のこのディレクトリに新しいファイルを作成します。 プロジェクトのルートからの相対パスがで`./tutorial/static/tutorial/app.css`ある必要があります。 そのファイルに次のコードを追加します。

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

次に、そのレイアウトを使用するホームページ用のテンプレートを作成します。 という名前`./tutorial/templates/tutorial` `home.html`のディレクトリに新しいファイルを作成し、次のコードを追加します。

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

このテンプレート`home`を使用するようにビューを更新します。 `./tutorial/views.py`ファイルを開き、次の新しい関数を追加します。

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

次に、既存`home`のビューを次のように置き換えます。

```python
def home(request):
  context = initialize_context(request)

  return render(request, 'tutorial/home.html', context)
```

すべての変更を保存し、サーバーを再起動します。 この時点で、アプリの外観は大きく異なります。

![再設計されたホームページのスクリーンショット](./images/create-app-01.png)
