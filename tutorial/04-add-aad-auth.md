<!-- markdownlint-disable MD002 MD041 -->

この演習では、前の演習からアプリケーションを拡張して、Azure サーバーでの認証をサポートAD。 これは、Microsoft Graph を呼び出す必要がある OAuth アクセス トークンを取得するために必要です。 この手順では [、MSAL for Python ライブラリを](https://github.com/AzureAD/microsoft-authentication-library-for-python) アプリケーションに統合します。

1. という名前のプロジェクトのルートに新しいファイルを作成 `oauth_settings.yml` し、次のコンテンツを追加します。

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. アプリケーション `YOUR_APP_ID_HERE` 登録ポータルのアプリケーション ID に置き換え、生成 `YOUR_APP_SECRET_HERE` したパスワードに置き換える。

> [!IMPORTANT]
> git などのソース管理を使用している場合は **、oauth_settings.yml** ファイルをソース管理から除外して、アプリ ID とパスワードが誤って漏洩しないようにする良い時期です。

## <a name="implement-sign-in"></a>サインインの実装

1. という名前の **./tutorial ディレクトリに新しいファイルを作成** `auth_helper.py` し、次のコードを追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    このファイルには、すべての認証関連の方法が保持されます。 承認 `get_sign_in_flow` URL が生成され、メソッドはアクセス トークンの `get_token_from_code` 承認応答を交換します。

1. `import` **./tutorial/views.py の上部に次のステートメントを追加します**。

    ```python
    from dateutil import tz, parser
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. **./tutorial/views.py ファイルにサインイン ビューを追加** します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. **./tutorial/views.py ファイルにコールバック ビューを追加** します。

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    これらのビューの機能を検討します。

    - このアクションでは、Azure AD サインイン URL が生成され、OAuth クライアントによって生成されたフローが保存され、ブラウザーが Azure ADサインイン ページ `signin` にリダイレクトされます。

    - アクション `callback` は、サインインが完了した後に Azure がリダイレクトする場所です。 このアクションでは、保存されたフローと Azure から送信されたクエリ文字列を使用して、アクセス トークンを要求します。 その後、一時的なエラー値で応答を使用してホーム ページにリダイレクトします。 これを使用して、サインインが動作しているか確認してから、次に進む必要があります。

1. **./tutorial/urls.py を** 開き、既存のステートメントを次のステートメント `path` `signin` に置き換える。

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. ビューに新 `path` しいビューを追加 `callback` します。

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. サーバーを起動し、を参照します `https://localhost:8000` 。 [サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。 Microsoft アカウントでログインし、要求されたアクセス許可に同意します。 ブラウザーはアプリにリダイレクトし、アクセス トークンを含む応答を表示します。

### <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. という名前の **./tutorial ディレクトリに新しいファイルを作成** `graph_helper.py` し、次のコードを追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    このメソッドは、以前に取得したアクセス トークンを使用して、Microsoft Graph エンドポイントに GET 要求を行い、ユーザーのプロファイル `get_user` `/me` を取得します。

1. `callback` **./tutorial/views.py** のメソッドを更新して、Microsoft Graph からユーザーのプロファイルを取得します。 次の `import` ステートメントをファイルの一番上に追加します。

    ```python
    from tutorial.graph_helper import *
    ```

1. `callback` メソッドを次のコードに置き換えます。

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)

      #Get the user's profile
      user = get_user(result['access_token'])
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': 'User: {0}\nToken: {1}'.format(user, result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    新しいコードは、ユーザー `get_user` のプロファイルを要求するメソッドを呼び出します。 テスト用の一時出力にユーザー オブジェクトを追加します。

1. **./tutorial/auth_helper.py に次の新しいメソッドを追加します**。

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. ./tutorial/views.py の関数を更新して、ユーザーをセッションに保存し、メイン `callback` ページにリダイレクトします。  行を `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` 次に置き換える。

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. メソッドを `callback` 次に置き換える。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>サインアウトの実装

1. `sign_out` **./tutorial/views.py で新しいビューを追加します**。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. **./tutorial/urls.py を** 開き、既存のステートメントを次のステートメント `path` `signout` に置き換える。

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. サーバーを再起動し、サインイン プロセスを実行します。 ホーム ページに戻る必要がありますが、サインイン済みかどうかを示す UI が変更される必要があります。

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. 右上隅にあるユーザー アバターをクリックして、[サインアウト] **リンクにアクセス** します。 **[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>トークンの更新

この時点で、アプリケーションはアクセス トークンを持ち、API 呼び出しの `Authorization` ヘッダーに送信されます。 これは、アプリがユーザーの代わりに Microsoft Graph にアクセスできるトークンです。

ただし、このトークンは一時的なものです。 トークンは発行後 1 時間で期限切れになります。 ここで、更新トークンが役に立ちます。 更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。

このサンプルでは MSAL を使用しますので、トークンを更新するために特定のコードを記述する必要はありません。 MSAL のメソッドは `acquire_token_silent` 、必要に応じてトークンの更新を処理します。
