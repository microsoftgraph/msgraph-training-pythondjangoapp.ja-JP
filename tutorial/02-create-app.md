<!-- markdownlint-disable MD002 MD041 -->

この演習では [、Django を使用して](https://www.djangoproject.com/) Web アプリを構築します。

1. Django をまだインストールしていない場合は、次のコマンドを使用してコマンド ライン インターフェイス (CLI) からインストールできます。

    ```Shell
    pip install --user Django==3.1.4
    ```

1. CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. graph_tutorial ディレクトリ **に移動** し、次のコマンドを入力してローカル Web サーバーを起動します。

    ```Shell
    python manage.py runserver
    ```

1. ブラウザーを開き、`http://localhost:8000` に移動します。 すべてが動作している場合は、Django のウェルカム ページが表示されます。 見当たらない場合は [、Django の開始ガイドをご覧ください](https://www.djangoproject.com/start/)。

1. アプリをプロジェクトに追加します。 CLI で次のコマンドを実行します。

    ```Shell
    python manage.py startapp tutorial
    ```

1. **./graph_tutorial/settings.py を** 開き、新しい `tutorial` アプリを設定に追加 `INSTALLED_APPS` します。

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. CLI で次のコマンドを実行して、プロジェクトのデータベースを初期化します。

    ```Shell
    python manage.py migrate
    ```

1. アプリの [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) を追加 `tutorial` します。 ./tutorial ディレクトリに新しい **ファイルを作成** し、 `urls.py` 次のコードを追加します。

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

1. プロジェクト URLconf を更新して、これをインポートします。 **./graph_tutorial/urls.py** を開き、内容を次の内容に置き換えてください。

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. アプリに一時的なビューを `tutorials` 追加して、URL ルーティングが機能しているのを確認します。 **./tutorial/views.py を開き**、その内容全体を次のコードに置き換えます。

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

1. 変更内容をすべて保存し、サーバーを再起動します。 `http://localhost:8000` を参照します。 表示される `Welcome to the tutorial.`

## <a name="install-libraries"></a>ライブラリをインストールする

次に進む前に、後で使用する追加のライブラリをインストールします。

- サインインおよび OAuth トークン フローを処理する Python 用 Microsoft 認証ライブラリ[(MSAL)。](https://github.com/AzureAD/microsoft-authentication-library-for-python)
- [要求: Microsoft Graph を呼び](https://requests.readthedocs.io/en/master/) 出す人間向け HTTP。
- [YAML ファイルから](https://pyyaml.org/wiki/PyYAMLDocumentation) 構成を読み込む PyYAML。
- Microsoft Graph から返される ISO 8601 日付文字列を解析する[python-dateutil。](https://pypi.org/project/python-dateutil/)

1. CLI で次のコマンドを実行します。

    ```Shell
    pip install msal==1.7.0
    pip install requests==2.25.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>アプリを設計する

1. ./tutorial ディレクトリに新 **しいディレクトリを作成** します `templates` 。

1. **./tutorial/templates** ディレクトリで、新しいディレクトリを作成します `tutorial` 。

1. **./tutorial/templates/tutorial** ディレクトリで、新しいファイルを作成します `layout.html` 。 そのファイルに次のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    このコードは、 [シンプルなスタイル設定用の Bootstrap](http://getbootstrap.com/) と、いくつかの単純なアイコン用の Fabric [Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) を追加します。 また、ナビゲーション バーを含むグローバル レイアウトも定義します。

1. ./tutorial ディレクトリに新 **しいディレクトリを作成** します `static` 。

1. **./tutorial/static ディレクトリで**、新しいディレクトリを作成します `tutorial` 。

1. **./tutorial/static/tutorial ディレクトリ** で、新しいファイルを作成します `app.css` 。 そのファイルに次のコードを追加します。

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. レイアウトを使用するホーム ページのテンプレートを作成します。 **./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し、次 `home.html` のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. ファイルを `./tutorial/views.py` 開き、次の新しい関数を追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. 既存のビューを `home` 次のビューに置き換えます。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. **./tutorial/static/tutorial** ディレクトリに **no-profile-photo.png** という名前の PNG ファイルを追加します。

1. 変更内容をすべて保存し、サーバーを再起動します。 これで、アプリは非常に異なって表示されます。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
