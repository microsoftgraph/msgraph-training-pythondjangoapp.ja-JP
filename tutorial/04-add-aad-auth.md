<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、[要求-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/)ライブラリをアプリケーションに統合します。

という名前`oauth_settings.yml`のプロジェクトのルートに新しいファイルを作成し、次のコンテンツを追加します。

```text
app_id: "YOUR_APP_ID_HERE"
app_secret: "YOUR_APP_PASSWORD_HERE"
redirect: "http://localhost:8000/tutorial/callback"
scopes: "openid profile offline_access user.read calendars.read"
authority: "https://login.microsoftonline.com/common"
authorize_endpoint: "/oauth2/v2.0/authorize"
token_endpoint: "/oauth2/v2.0/token"
```

を`YOUR_APP_ID_HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR_APP_SECRET_HERE`たパスワードに置き換えます。

> [!IMPORTANT]
> Git などのソース管理を使用している場合は、アプリ ID とパスワードを誤っ`oauth_settings.yml`てリークしないように、ソース管理からファイルを除外することをお勧めします。

## <a name="implement-sign-in"></a>サインインの実装

という名前`./tutorial` `auth_helper.py`のディレクトリに新しいファイルを作成し、次のコードを追加します。

```python
import yaml
from requests_oauthlib import OAuth2Session
import os
import time

# This is necessary for testing with non-HTTPS localhost
# Remove this if deploying to production
os.environ['OAUTHLIB_INSECURE_TRANSPORT'] = '1'

# This is necessary because Azure does not guarantee
# to return scopes in the same case and order as requested
os.environ['OAUTHLIB_RELAX_TOKEN_SCOPE'] = '1'
os.environ['OAUTHLIB_IGNORE_SCOPE_CHANGE'] = '1'

# Load the oauth_settings.yml file
stream = open('oauth_settings.yml', 'r')
settings = yaml.load(stream, yaml.SafeLoader)
authorize_url = '{0}{1}'.format(settings['authority'], settings['authorize_endpoint'])
token_url = '{0}{1}'.format(settings['authority'], settings['token_endpoint'])

# Method to generate a sign-in url
def get_sign_in_url():
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  sign_in_url, state = aad_auth.authorization_url(authorize_url, prompt='login')

  return sign_in_url, state

# Method to exchange auth code for access token
def get_token_from_code(callback_url, expected_state):
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    state=expected_state,
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  token = aad_auth.fetch_token(token_url,
    client_secret = settings['app_secret'],
    authorization_response=callback_url)

  return token
```

このファイルには、認証関連のすべてのメソッドが保持されます。 は`get_sign_in_url`認証 URL を生成し、メソッド`get_token_from_code`はアクセストークンの認証応答を交換します。

の`./tutorial/views.py`先頭に`import`次のステートメントを追加します。

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

これで、 `./tutorial/views.py`ファイルにいくつかの新しいビューが追加されました。

```python
def sign_in(request):
  # Get the sign-in URL
  sign_in_url, state = get_sign_in_url()
  # Save the expected state so we can validate in the callback
  request.session['auth_state'] = state
  # Redirect to the Azure sign-in page
  return HttpResponseRedirect(sign_in_url)

def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)
  # Temporary! Save the response in an error so it's displayed
  request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
  return HttpResponseRedirect(reverse('home'))
```

これにより`signin` 、との 2 `callback`つの新しいビューが定義されます。

この`signin`アクションは、azure AD サインイン URL を生成し`state` 、OAuth クライアントによって生成された値を保存してから、ブラウザーを azure ad サインインページにリダイレクトします。

この`callback`操作では、サインインの完了後に Azure がリダイレクトされます。 この操作によって`state` 、値が保存された値と一致するようになり、Azure によって送信された認証コードを使用してアクセストークンが要求されます。 その後、一時的なエラー値でアクセストークンを使用して、ホームページにリダイレクトします。 これを使用して、サインインが機能していることを確認してから、に進みます。 テストする前に、ビューをに`./tutorial/urls.py`追加する必要があります。

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

の`<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` `./tutorial/templates/tutorial/home.html`行を次のように置き換えます。

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

の`<a href="#" class="nav-link">Sign In</a>` `./tutorial/templates/tutorial/layout.html`行を次のように置き換えます。

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

サーバーを起動し、を`https://localhost:8000/tutorial`参照します。 [サインイン] ボタンをクリックすると、に`https://login.microsoftonline.com`リダイレクトされます。 Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。 ブラウザーがアプリにリダイレクトし、トークンが表示されます。

### <a name="get-user-details"></a>ユーザーの詳細を取得する

という名前`./tutorial` `graph_helper.py`のディレクトリに新しいファイルを作成し、次のコードを追加します。

```python
from requests_oauthlib import OAuth2Session

graph_url = 'https://graph.microsoft.com/v1.0'

def get_user(token):
  graph_client = OAuth2Session(token=token)
  # Send GET to /me
  user = graph_client.get('{0}/me'.format(graph_url))
  # Return the JSON result
  return user.json()
```

メソッド`get_user`は、以前に取得したアクセストークン`/me`を使用して、ユーザーのプロファイルを取得するために Microsoft GRAPH エンドポイントに get 要求を行います。

Microsoft Graph `callback`からユーザー `./tutorial/views.py`のプロファイルを取得するには、のメソッドを更新します。

最初に、次`import`のステートメントをファイルの先頭に追加します。

```python
from tutorial.graph_helper import get_user
```

`callback`メソッドを次のコードに置き換えます。

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

トークンを入手できるようになったので、これをアプリに保存する方法を実装します。 これはサンプルアプリなので、わかりやすくするために、セッションに格納します。 実際のアプリケーションでは、データベースのような、より信頼性の高いセキュリティで保護されたストレージソリューションを使用します。

に次の新しいメソッドを`./tutorial/auth_helper.py`追加します。

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

その後、の`callback` `./tutorial/views.py`関数を更新してトークンをセッションに格納し、メインページにリダイレクトします。 行を`from tutorial.auth_helper import get_sign_in_url, get_token_from_code`次のように置き換えます。

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

`callback`メソッドを次のように置き換えます。

```python
def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)

  # Get the user's profile
  user = get_user(token)

  # Save token and user
  store_token(request, token)
  store_user(request, user)

  return HttpResponseRedirect(reverse('home'))
```

## <a name="implement-sign-out"></a>サインアウトを実装する

この新しい機能をテストする前に、サインアウトする方法を追加します。に`sign_out` `./tutorial/views.py`新しいビューを追加します。

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

これで、このビュー `./tutorial/urls.py`をに追加します。

```python
path('signout', views.sign_out, name='signout'),
```

この新しい**** ビューを使用する`./tutorial/templates/tutorial/layout.html`には、のサインアウトリンクを更新します。 行を`<a href="#" class="dropdown-item">Sign Out</a>`次のように置き換えます。

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

サーバーを再起動し、サインインプロセスを実行します。 ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。

![サインイン後のホームページのスクリーンショット](./images/add-aad-auth-01.png)

右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。 [**サインアウト**] をクリックすると、セッションがリセットされ、ホームページに戻ります。

![[サインアウト] リンクがあるドロップダウンメニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>トークンの更新

この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。 これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。

ただし、このトークンは存続期間が短くなります。 トークンが発行された後、有効期限が切れる時間になります。 ここでは、更新トークンが有効になります。 更新トークンを使用すると、ユーザーが再度サインインする必要なく、新しいアクセストークンをアプリで要求できます。 トークンの更新を実装するために、トークン管理コードを更新します。

の`get_token` `./tutorial/auth_helper.py`既存のメソッドを次のように置き換えます。

```python
def get_token(request):
  token = request.session['oauth_token']
  if token != None:
    # Check expiration
    now = time.time()
    # Subtract 5 minutes from expiration to account for clock skew
    expire_time = token['expires_at'] - 300
    if now >= expire_time:
      # Refresh the token
      aad_auth = OAuth2Session(settings['app_id'],
        token = token,
        scope=settings['scopes'],
        redirect_uri=settings['redirect'])

      refresh_params = {
        'client_id': settings['app_id'],
        'client_secret': settings['app_secret'],
      }
      new_token = aad_auth.refresh_token(token_url, **refresh_params)

      # Save new token
      store_token(request, new_token)

      # Return new access token
      return new_token

    else:
      # Token still valid, just return it
      return token
```

このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。 その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。
