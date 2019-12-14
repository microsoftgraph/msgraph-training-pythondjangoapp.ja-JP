<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7e27c-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="7e27c-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="7e27c-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="7e27c-103">この手順では、[要求-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/)ライブラリをアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-103">In this step you will integrate the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library into the application.</span></span>

<span data-ttu-id="7e27c-104">という名前`oauth_settings.yml`のプロジェクトのルートに新しいファイルを作成し、次のコンテンツを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

```text
app_id: "YOUR_APP_ID_HERE"
app_secret: "YOUR_APP_PASSWORD_HERE"
redirect: "http://localhost:8000/tutorial/callback"
scopes: "openid profile offline_access user.read calendars.read"
authority: "https://login.microsoftonline.com/common"
authorize_endpoint: "/oauth2/v2.0/authorize"
token_endpoint: "/oauth2/v2.0/token"
```

<span data-ttu-id="7e27c-105">を`YOUR_APP_ID_HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR_APP_SECRET_HERE`たパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7e27c-106">Git などのソース管理を使用している場合は、アプリ ID とパスワードを誤っ`oauth_settings.yml`てリークしないように、ソース管理からファイルを除外することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7e27c-106">If you're using source control such as git, now would be a good time to exclude the `oauth_settings.yml` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="7e27c-107">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="7e27c-107">Implement sign-in</span></span>

<span data-ttu-id="7e27c-108">という名前`./tutorial` `auth_helper.py`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-108">Create a new file in the `./tutorial` directory named `auth_helper.py` and add the following code.</span></span>

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

<span data-ttu-id="7e27c-109">このファイルには、認証関連のすべてのメソッドが保持されます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="7e27c-110">は`get_sign_in_url`認証 URL を生成し、メソッド`get_token_from_code`はアクセストークンの認証応答を交換します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-110">The `get_sign_in_url` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

<span data-ttu-id="7e27c-111">の`./tutorial/views.py`先頭に`import`次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-111">Add the following `import` statements to the top of `./tutorial/views.py`.</span></span>

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

<span data-ttu-id="7e27c-112">これで、 `./tutorial/views.py`ファイルにいくつかの新しいビューが追加されました。</span><span class="sxs-lookup"><span data-stu-id="7e27c-112">Now add a couple of new views in the `./tutorial/views.py` file.</span></span>

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

<span data-ttu-id="7e27c-113">これにより`signin` 、との 2 `callback`つの新しいビューが定義されます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-113">This defines two new views: `signin` and `callback`.</span></span>

<span data-ttu-id="7e27c-114">この`signin`アクションは、azure AD サインイン URL を生成し`state` 、OAuth クライアントによって生成された値を保存してから、ブラウザーを azure ad サインインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="7e27c-114">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

<span data-ttu-id="7e27c-115">この`callback`操作では、サインインの完了後に Azure がリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-115">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="7e27c-116">この操作によって`state` 、値が保存された値と一致するようになり、Azure によって送信された認証コードを使用してアクセストークンが要求されます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-116">That action makes sure the `state` value matches the saved value, then uses the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="7e27c-117">その後、一時的なエラー値でアクセストークンを使用して、ホームページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="7e27c-117">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="7e27c-118">これを使用して、サインインが機能していることを確認してから、に進みます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-118">You'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="7e27c-119">テストする前に、ビューをに`./tutorial/urls.py`追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e27c-119">Before you test, you need to add the views to `./tutorial/urls.py`.</span></span>

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

<span data-ttu-id="7e27c-120">の`<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` `./tutorial/templates/tutorial/home.html`行を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-120">Replace the `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` line in `./tutorial/templates/tutorial/home.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

<span data-ttu-id="7e27c-121">の`<a href="#" class="nav-link">Sign In</a>` `./tutorial/templates/tutorial/layout.html`行を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-121">Replace the `<a href="#" class="nav-link">Sign In</a>` line in `./tutorial/templates/tutorial/layout.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

<span data-ttu-id="7e27c-122">サーバーを起動し、を`https://localhost:8000/tutorial`参照します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-122">Start the server and browse to `https://localhost:8000/tutorial`.</span></span> <span data-ttu-id="7e27c-123">[サインイン] ボタンをクリックすると、に`https://login.microsoftonline.com`リダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="7e27c-124">Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="7e27c-125">ブラウザーがアプリにリダイレクトし、トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-125">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="7e27c-126">ユーザーの詳細を取得する</span><span class="sxs-lookup"><span data-stu-id="7e27c-126">Get user details</span></span>

<span data-ttu-id="7e27c-127">という名前`./tutorial` `graph_helper.py`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-127">Create a new file in the `./tutorial` directory named `graph_helper.py` and add the following code.</span></span>

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

<span data-ttu-id="7e27c-128">メソッド`get_user`は、以前に取得したアクセストークン`/me`を使用して、ユーザーのプロファイルを取得するために Microsoft GRAPH エンドポイントに get 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="7e27c-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

<span data-ttu-id="7e27c-129">Microsoft Graph `callback`からユーザー `./tutorial/views.py`のプロファイルを取得するには、のメソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-129">Update the `callback` method in `./tutorial/views.py` to get the user's profile from Microsoft Graph.</span></span>

<span data-ttu-id="7e27c-130">最初に、次`import`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-130">First, add the following `import` statement to the top of the file.</span></span>

```python
from tutorial.graph_helper import get_user
```

<span data-ttu-id="7e27c-131">`callback`メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-131">Replace the `callback` method with the following code.</span></span>

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

<span data-ttu-id="7e27c-132">新しいコードは、メソッド`get_user`を呼び出してユーザーのプロファイルを要求します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="7e27c-133">テストのために、ユーザーオブジェクトを一時出力に追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-133">It adds the user object to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="7e27c-134">トークンの格納</span><span class="sxs-lookup"><span data-stu-id="7e27c-134">Storing the tokens</span></span>

<span data-ttu-id="7e27c-135">トークンを入手できるようになったので、これをアプリに保存する方法を実装します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-135">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="7e27c-136">これはサンプルアプリなので、わかりやすくするために、セッションに格納します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-136">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="7e27c-137">実際のアプリケーションでは、データベースのような、より信頼性の高いセキュリティで保護されたストレージソリューションを使用します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-137">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="7e27c-138">に次の新しいメソッドを`./tutorial/auth_helper.py`追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-138">Add the following new methods to `./tutorial/auth_helper.py`.</span></span>

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

<span data-ttu-id="7e27c-139">その後、の`callback` `./tutorial/views.py`関数を更新してトークンをセッションに格納し、メインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="7e27c-139">Then, update the `callback` function in `./tutorial/views.py` to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="7e27c-140">行を`from tutorial.auth_helper import get_sign_in_url, get_token_from_code`次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-140">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

<span data-ttu-id="7e27c-141">`callback`メソッドを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-141">Replace the `callback` method with the following.</span></span>

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

## <a name="implement-sign-out"></a><span data-ttu-id="7e27c-142">サインアウトを実装する</span><span class="sxs-lookup"><span data-stu-id="7e27c-142">Implement sign-out</span></span>

<span data-ttu-id="7e27c-143">この新しい機能をテストする前に、サインアウトする方法を追加します。に`sign_out` `./tutorial/views.py`新しいビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-143">Before you test this new feature, add a way to sign out. Add a new `sign_out` view in `./tutorial/views.py`.</span></span>

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

<span data-ttu-id="7e27c-144">これで、このビュー `./tutorial/urls.py`をに追加します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-144">Now add this view to `./tutorial/urls.py`.</span></span>

```python
path('signout', views.sign_out, name='signout'),
```

<span data-ttu-id="7e27c-145">この新しい\*\*\*\* ビューを使用する`./tutorial/templates/tutorial/layout.html`には、のサインアウトリンクを更新します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-145">Update the **Sign Out** link in `./tutorial/templates/tutorial/layout.html` to use this new view.</span></span> <span data-ttu-id="7e27c-146">行を`<a href="#" class="dropdown-item">Sign Out</a>`次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-146">Replace the `<a href="#" class="dropdown-item">Sign Out</a>` line with the following.</span></span>

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

<span data-ttu-id="7e27c-147">サーバーを再起動し、サインインプロセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-147">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="7e27c-148">ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7e27c-148">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![サインイン後のホームページのスクリーンショット](./images/add-aad-auth-01.png)

<span data-ttu-id="7e27c-150">右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="7e27c-150">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="7e27c-151">[**サインアウト**] をクリックすると、セッションがリセットされ、ホームページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="7e27c-151">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![[サインアウト] リンクがあるドロップダウンメニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="7e27c-153">トークンの更新</span><span class="sxs-lookup"><span data-stu-id="7e27c-153">Refreshing tokens</span></span>

<span data-ttu-id="7e27c-154">この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。</span><span class="sxs-lookup"><span data-stu-id="7e27c-154">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="7e27c-155">これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。</span><span class="sxs-lookup"><span data-stu-id="7e27c-155">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="7e27c-156">ただし、このトークンは存続期間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="7e27c-156">However, this token is short-lived.</span></span> <span data-ttu-id="7e27c-157">トークンが発行された後、有効期限が切れる時間になります。</span><span class="sxs-lookup"><span data-stu-id="7e27c-157">The token expires an hour after it is issued.</span></span> <span data-ttu-id="7e27c-158">ここでは、更新トークンが有効になります。</span><span class="sxs-lookup"><span data-stu-id="7e27c-158">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="7e27c-159">更新トークンを使用すると、ユーザーが再度サインインする必要なく、新しいアクセストークンをアプリで要求できます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-159">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="7e27c-160">トークンの更新を実装するために、トークン管理コードを更新します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-160">Update the token management code to implement token refresh.</span></span>

<span data-ttu-id="7e27c-161">の`get_token` `./tutorial/auth_helper.py`既存のメソッドを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7e27c-161">Replace the existing `get_token` method in `./tutorial/auth_helper.py` with the following.</span></span>

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

<span data-ttu-id="7e27c-162">このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-162">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="7e27c-163">その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。</span><span class="sxs-lookup"><span data-stu-id="7e27c-163">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
