<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、[要求-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/)ライブラリをアプリケーションに統合します。

1. という名前`oauth_settings.yml`のプロジェクトのルートに新しいファイルを作成し、次のコンテンツを追加します。

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. を`YOUR_APP_ID_HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR_APP_SECRET_HERE`たパスワードに置き換えます。

> [!IMPORTANT]
> Git などのソース管理を使用している場合は、yml ファイルをソース管理から除外して、アプリ ID とパスワード**oauth_settings**が誤ってリークしないようにすることをお勧めします。

## <a name="implement-sign-in"></a>サインインの実装

1. という名前`auth_helper.py`の**チュートリアル**のディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    このファイルには、認証関連のすべてのメソッドが保持されます。 は`get_sign_in_url`認証 URL を生成し、メソッド`get_token_from_code`はアクセストークンの認証応答を交換します。

1. /Tutorial/views.py の先頭`import`に次のステートメントを **./tutorial/views.py**追加します。

    ```python
    from django.urls import reverse
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code
    ```

1. **/Tutorial/views.py**ファイルにサインインビューを追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. **/Tutorial/views.py**ファイルにコールバックビューを追加します。

    ```python
    def callback(request):
      # Get the state saved in session
      expected_state = request.session.pop('auth_state', '')
      # Make the token request
      token = get_token_from_code(request.get_full_path(), expected_state)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
      return HttpResponseRedirect(reverse('home'))
    ```

    これらのビューの機能について検討します。

    - この`signin`アクションは、azure AD サインイン URL を生成し`state` 、OAuth クライアントによって生成された値を保存してから、ブラウザーを azure ad サインインページにリダイレクトします。

    - この`callback`操作では、サインインの完了後に Azure がリダイレクトされます。 この操作によって`state` 、値が保存された値と一致するようになり、Azure によって送信された認証コードを使用してアクセストークンが要求されます。 その後、一時的なエラー値でアクセストークンを使用して、ホームページにリダイレクトします。 これを使用して、サインインが機能していることを確認してから、に進みます。

1. **/Tutorial/urls.py**を開き、既存`path`のステートメント`signin`を次のように置き換えます。

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. `callback`ビューの新しい`path`を追加します。

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. サーバーを起動し、を`https://localhost:8000`参照します。 [サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。 Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。 ブラウザーがアプリにリダイレクトし、トークンが表示されます。

### <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. という名前`graph_helper.py`の**チュートリアル**のディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    メソッド`get_user`は、以前に取得したアクセストークン`/me`を使用して、ユーザーのプロファイルを取得するために Microsoft GRAPH エンドポイントに get 要求を行います。

1. /Tutorial/views.py の`callback`メソッドを **./tutorial/views.py**更新して、Microsoft Graph からユーザーのプロファイルを取得します。 次の `import` ステートメントをファイルの一番上に追加します。

    ```python
    from tutorial.graph_helper import get_user
    ```

1. `callback` メソッドを次のコードに置き換えます。

    ```python
    def callback(request):
      # Get the state saved in session
      expected_state = request.session.pop('auth_state', '')
      # Make the token request
      token = get_token_from_code(request.get_full_path(), expected_state)

      # Get the user's profile
      user = get_user(token)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved',
        'debug': 'User: {0}\nToken: {1}'.format(user, token) }
      return HttpResponseRedirect(reverse('home'))
    ```

新しいコードは、メソッド`get_user`を呼び出してユーザーのプロファイルを要求します。 テストのために、ユーザーオブジェクトを一時出力に追加します。

## <a name="storing-the-tokens"></a>トークンの格納

トークンを取得できるようになったので、トークンをアプリに格納する手順を実装します。 これはサンプルアプリなので、わかりやすくするために、セッションに格納します。 実際のアプリでは、データベースのような、より信頼性の高い安全なストレージ ソリューションを使用します。

1. **/Tutorial/auth_helper py**に、次の新しいメソッドを追加します。

    ```python
    def store_token(request, token):
      request.session['oauth_token'] = token

    def store_user(request, user):
      request.session['user'] = {
        'is_authenticated': True,
        'name': user['displayName'],
        'email': user['mail'] if (user['mail'] != None) else user['userPrincipalName']
      }

    def get_token(request):
      token = request.session['oauth_token']
      return token

    def remove_user_and_token(request):
      if 'oauth_token' in request.session:
        del request.session['oauth_token']

      if 'user' in request.session:
        del request.session['user']
    ```

1. /Tutorial/views.py の`callback`関数を **./tutorial/views.py**更新してトークンをセッションに格納し、メインページにリダイレクトします。 行を`from tutorial.auth_helper import get_sign_in_url, get_token_from_code`次のように置き換えます。

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
    ```

1. `callback`メソッドを次のように置き換えます。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>サインアウトを実装する

1. /Tutorial/views.py に新しい`sign_out`ビューを **./tutorial/views.py**追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. **/Tutorial/urls.py**を開き、既存`path`のステートメント`signout`を次のように置き換えます。

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. サーバーを再起動し、サインインプロセスを実行します。 ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. 右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。 **[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>トークンの更新

この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。 これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。

ただし、このトークンは一時的なものです。 トークンが発行された後、有効期限が切れる時間になります。 ここで、更新トークンが役に立ちます。 更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。 トークンの更新を実装するために、トークン管理コードを更新します。

1. `get_token` **/Tutorial/auth_helper py**の既存のメソッドを次のように置き換えます。

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="GetTokenSnippet":::

    このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。 その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。
