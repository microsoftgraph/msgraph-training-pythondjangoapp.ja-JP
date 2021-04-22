<!-- markdownlint-disable MD002 MD041 -->

この演習では [、Django を使用して](https://www.djangoproject.com/) Web アプリを作成します。

1. Django がインストールされていない場合は、次のコマンドを使用してコマンド ライン インターフェイス (CLI) からインストールできます。

    ```Shell
    pip install Django==3.2
    ```

1. CLI を開き、ファイルを作成する権限を持つディレクトリに移動し、次のコマンドを実行して新しい Django アプリを作成します。

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. ディレクトリに移動 **graph_tutorial、** 次のコマンドを入力してローカル Web サーバーを起動します。

    ```Shell
    python manage.py runserver
    ```

1. ブラウザーを開き、`http://localhost:8000` に移動します。 すべてが機能している場合は、Django ウェルカム ページが表示されます。 表示しない場合は [、Django](https://www.djangoproject.com/start/)の開始ガイドを確認してください。

1. プロジェクトにアプリを追加します。 CLI で次のコマンドを実行します。

    ```Shell
    python manage.py startapp tutorial
    ```

1. **./graph_tutorial/settings.py を** 開き、新しい `tutorial` アプリを設定に追加 `INSTALLED_APPS` します。

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. CLI で次のコマンドを実行して、プロジェクトのデータベースを初期化します。

    ```Shell
    python manage.py migrate
    ```

1. アプリの [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) を追加 `tutorial` します。 という名前の **./tutorial ディレクトリに新しいファイルを作成** `urls.py` し、次のコードを追加します。

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

1. プロジェクト URLconf を更新して、この URLconf をインポートします。 **./graph_tutorial/urls.py** を開き、内容を次に置き換えてください。

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. アプリに一時的なビューを `tutorials` 追加して、URL ルーティングが機能しているのを確認します。 **./tutorial/views.py を開き**、その内容全体を次のコードに置き換えます。

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. 変更内容をすべて保存し、サーバーを再起動します。 `http://localhost:8000` を参照します。 表示される `Welcome to the tutorial.`

## <a name="install-libraries"></a>ライブラリをインストールする

次に進む前に、後で使用する追加のライブラリをインストールします。

- [サインインおよび OAuth トークン](https://github.com/AzureAD/microsoft-authentication-library-for-python) フローを処理する Python の Microsoft 認証ライブラリ (MSAL)。
- [YAML ファイルから](https://pyyaml.org/wiki/PyYAMLDocumentation) 構成を読み込む PyYAML。
- Microsoft Graph から返される ISO 8601 日付文字列を解析する[python-dateutil。](https://pypi.org/project/python-dateutil/)

1. CLI で次のコマンドを実行します。

    ```Shell
    pip install msal==1.10.0
    pip install pyyaml==5.4.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>アプリを設計する

1. という名前の **./tutorial ディレクトリに新しいディレクトリを** 作成します `templates` 。

1. **./tutorial/templates** ディレクトリで、という名前の新しいディレクトリを作成します `tutorial` 。

1. **./tutorial/templates/tutorial ディレクトリ** で、という名前の新しいファイルを作成します `layout.html` 。 そのファイルに次のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    このコードでは、 [単純なスタイル](http://getbootstrap.com/) 設定用のブートストラップと、いくつかの簡単なアイコン [の Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) を追加します。 また、ナビゲーション バーを持つグローバル レイアウトも定義します。

1. という名前の **./tutorial ディレクトリに新しいディレクトリを** 作成します `static` 。

1. **./tutorial/static ディレクトリで**、という名前の新しいディレクトリを作成します `tutorial` 。

1. **./tutorial/static/tutorial ディレクトリ** で、という名前の新しいファイルを作成します `app.css` 。 そのファイルに次のコードを追加します。

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. レイアウトを使用するホーム ページのテンプレートを作成します。 という名前の **./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し `home.html` 、次のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. ファイルを開 `./tutorial/views.py` き、次の新しい関数を追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. 既存のビューを `home` 次のビューに置き換えます。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. **./tutorial/static/tutorial ディレクトリにno-profile-photo.png****という名前の PNG ファイルを追加** します。

1. 変更内容をすべて保存し、サーバーを再起動します。 これで、アプリは非常に異なって見える必要があります。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
