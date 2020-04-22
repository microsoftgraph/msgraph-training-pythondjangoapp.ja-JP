<!-- markdownlint-disable MD002 MD041 -->

この演習では、 [Django](https://www.djangoproject.com/)を使用して web アプリを構築します。

1. Django がまだインストールされていない場合は、コマンドラインインターフェイス (CLI) から次のコマンドを使用してインストールできます。

    ```Shell
    pip install Django==3.0.4
    ```

1. CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. **Graph_tutorial**ディレクトリに移動し、次のコマンドを入力してローカル web サーバーを開始します。

    ```Shell
    python manage.py runserver
    ```

1. ブラウザーを開き、`http://localhost:8000` に移動します。 すべてが動作している場合は、Django ウェルカムページが表示されます。 これが表示されない場合は、 [Django 入門ガイド](https://www.djangoproject.com/start/)をご確認ください。

1. プロジェクトにアプリを追加します。 CLI で次のコマンドを実行します。

    ```Shell
    python manage.py startapp tutorial
    ```

1. **/Graph_tutorial/settings.py**を開いて、新しい`tutorial`アプリを`INSTALLED_APPS`設定に追加します。

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. CLI で、次のコマンドを実行してプロジェクトのデータベースを初期化します。

    ```Shell
    python manage.py migrate
    ```

1. `tutorial`アプリの[urlconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/)を追加します。 という名前`urls.py`の**チュートリアル**のディレクトリに新しいファイルを作成し、次のコードを追加します。

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

1. このプロジェクトをインポートするには、プロジェクトの URLconf を更新します。 **/Graph_tutorial/urls.py**を開き、内容を次のように置き換えます。

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. `tutorials`アプリに一時的なビューを追加して、URL のルーティングが機能していることを確認します。 **/Tutorial/views.py**を開き、次のコードを追加します。

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. 変更内容をすべて保存し、サーバーを再起動します。 `http://localhost:8000` を参照します。 が表示`Welcome to the tutorial.`

## <a name="install-libraries"></a>ライブラリをインストールする

に進む前に、後で使用する追加のライブラリをインストールします。

- [要求-OAuthlib:](https://requests-oauthlib.readthedocs.io/en/latest/)サインインおよび oauth トークンフローを処理し、Microsoft Graph への呼び出しを行うための、人間用の oauth。
- [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) yaml ファイルから構成を読み込むためのものです。
- Microsoft Graph から返される ISO 8601 日付文字列を解析するための[python-dateutil](https://pypi.org/project/python-dateutil/) 。

1. CLI で次のコマンドを実行します。

    ```Shell
    pip install requests_oauthlib==1.3.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>アプリを設計する

1. という名前`templates`の**チュートリアル**ディレクトリに新しいディレクトリを作成します。

1. **./Tutorial/templates**ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。

1. **./Tutorial/templates/tutorial**ディレクトリに、という名前`layout.html`の新しいファイルを作成します。 そのファイルに次のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。 また、ナビゲーションバーのあるグローバルレイアウトを定義します。

1. という名前`static`の**チュートリアル**ディレクトリに新しいディレクトリを作成します。

1. **./Tutorial/static**ディレクトリに、という名前`tutorial`の新しいディレクトリを作成します。

1. **./Tutorial/static/tutorial**ディレクトリに、という名前`app.css`の新しいファイルを作成します。 そのファイルに次のコードを追加します。

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. レイアウトを使用するホームページ用のテンプレートを作成します。 という名前`home.html`の **/tutorial/templates/tutorial**ディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. `./tutorial/views.py`ファイルを開き、次の新しい関数を追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. 既存`home`のビューを次のものに置き換えます。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. 変更内容をすべて保存し、サーバーを再起動します。 この時点で、アプリの外観は大きく異なります。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
