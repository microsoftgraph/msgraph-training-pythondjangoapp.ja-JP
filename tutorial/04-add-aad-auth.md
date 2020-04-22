<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="40bb5-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="40bb5-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="40bb5-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="40bb5-103">この手順では、[要求-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/)ライブラリをアプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-103">In this step you will integrate the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library into the application.</span></span>

1. <span data-ttu-id="40bb5-104">という名前`oauth_settings.yml`のプロジェクトのルートに新しいファイルを作成し、次のコンテンツを追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. <span data-ttu-id="40bb5-105">を`YOUR_APP_ID_HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR_APP_SECRET_HERE`たパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40bb5-106">Git などのソース管理を使用している場合は、yml ファイルをソース管理から除外して、アプリ ID とパスワード**oauth_settings**が誤ってリークしないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="40bb5-106">If you're using source control such as git, now would be a good time to exclude the **oauth_settings.yml** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="40bb5-107">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="40bb5-107">Implement sign-in</span></span>

1. <span data-ttu-id="40bb5-108">という名前`auth_helper.py`の**チュートリアル**のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-108">Create a new file in the **./tutorial** directory named `auth_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="40bb5-109">このファイルには、認証関連のすべてのメソッドが保持されます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="40bb5-110">は`get_sign_in_url`認証 URL を生成し、メソッド`get_token_from_code`はアクセストークンの認証応答を交換します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-110">The `get_sign_in_url` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

1. <span data-ttu-id="40bb5-111">/Tutorial/views.py の先頭`import`に次のステートメントを **./tutorial/views.py**追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-111">Add the following `import` statements to the top of **./tutorial/views.py**.</span></span>

    ```python
    from django.urls import reverse
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code
    ```

1. <span data-ttu-id="40bb5-112">**/Tutorial/views.py**ファイルにサインインビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-112">Add a sign-in view in the **./tutorial/views.py** file.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. <span data-ttu-id="40bb5-113">**/Tutorial/views.py**ファイルにコールバックビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-113">Add a callback view in the **./tutorial/views.py** file.</span></span>

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

    <span data-ttu-id="40bb5-114">これらのビューの機能について検討します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-114">Consider what these views do:</span></span>

    - <span data-ttu-id="40bb5-115">この`signin`アクションは、azure AD サインイン URL を生成し`state` 、OAuth クライアントによって生成された値を保存してから、ブラウザーを azure ad サインインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="40bb5-115">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    - <span data-ttu-id="40bb5-116">この`callback`操作では、サインインの完了後に Azure がリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-116">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="40bb5-117">この操作によって`state` 、値が保存された値と一致するようになり、Azure によって送信された認証コードを使用してアクセストークンが要求されます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-117">That action makes sure the `state` value matches the saved value, then uses the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="40bb5-118">その後、一時的なエラー値でアクセストークンを使用して、ホームページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="40bb5-118">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="40bb5-119">これを使用して、サインインが機能していることを確認してから、に進みます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-119">You'll use this to verify that our sign-in is working before moving on.</span></span>

1. <span data-ttu-id="40bb5-120">**/Tutorial/urls.py**を開き、既存`path`のステートメント`signin`を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-120">Open **./tutorial/urls.py** and replace the existing `path` statements for `signin` with the following.</span></span>

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. <span data-ttu-id="40bb5-121">`callback`ビューの新しい`path`を追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-121">Add a new `path` for the `callback` view.</span></span>

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. <span data-ttu-id="40bb5-122">サーバーを起動し、を`https://localhost:8000`参照します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-122">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="40bb5-123">[サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="40bb5-124">Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="40bb5-125">ブラウザーがアプリにリダイレクトし、トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-125">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="40bb5-126">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="40bb5-126">Get user details</span></span>

1. <span data-ttu-id="40bb5-127">という名前`graph_helper.py`の**チュートリアル**のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-127">Create a new file in the **./tutorial** directory named `graph_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="40bb5-128">メソッド`get_user`は、以前に取得したアクセストークン`/me`を使用して、ユーザーのプロファイルを取得するために Microsoft GRAPH エンドポイントに get 要求を行います。</span><span class="sxs-lookup"><span data-stu-id="40bb5-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

1. <span data-ttu-id="40bb5-129">/Tutorial/views.py の`callback`メソッドを **./tutorial/views.py**更新して、Microsoft Graph からユーザーのプロファイルを取得します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-129">Update the `callback` method in **./tutorial/views.py** to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="40bb5-130">次の `import` ステートメントをファイルの一番上に追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-130">Add the following `import` statement to the top of the file.</span></span>

    ```python
    from tutorial.graph_helper import get_user
    ```

1. <span data-ttu-id="40bb5-131">`callback` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-131">Replace the `callback` method with the following code.</span></span>

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

<span data-ttu-id="40bb5-132">新しいコードは、メソッド`get_user`を呼び出してユーザーのプロファイルを要求します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="40bb5-133">テストのために、ユーザーオブジェクトを一時出力に追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-133">It adds the user object to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="40bb5-134">トークンの格納</span><span class="sxs-lookup"><span data-stu-id="40bb5-134">Storing the tokens</span></span>

<span data-ttu-id="40bb5-135">トークンを取得できるようになったので、トークンをアプリに格納する手順を実装します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-135">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="40bb5-136">これはサンプルアプリなので、わかりやすくするために、セッションに格納します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-136">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="40bb5-137">実際のアプリでは、データベースのような、より信頼性の高い安全なストレージ ソリューションを使用します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-137">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="40bb5-138">**/Tutorial/auth_helper py**に、次の新しいメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-138">Add the following new methods to **./tutorial/auth_helper.py**.</span></span>

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

1. <span data-ttu-id="40bb5-139">/Tutorial/views.py の`callback`関数を **./tutorial/views.py**更新してトークンをセッションに格納し、メインページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="40bb5-139">Update the `callback` function in **./tutorial/views.py** to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="40bb5-140">行を`from tutorial.auth_helper import get_sign_in_url, get_token_from_code`次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-140">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
    ```

1. <span data-ttu-id="40bb5-141">`callback`メソッドを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-141">Replace the `callback` method with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="40bb5-142">サインアウトを実装する</span><span class="sxs-lookup"><span data-stu-id="40bb5-142">Implement sign-out</span></span>

1. <span data-ttu-id="40bb5-143">/Tutorial/views.py に新しい`sign_out`ビューを **./tutorial/views.py**追加します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-143">Add a new `sign_out` view in **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. <span data-ttu-id="40bb5-144">**/Tutorial/urls.py**を開き、既存`path`のステートメント`signout`を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-144">Open **./tutorial/urls.py** and replace the existing `path` statements for `signout` with the following.</span></span>

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. <span data-ttu-id="40bb5-145">サーバーを再起動し、サインインプロセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-145">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="40bb5-146">ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="40bb5-146">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. <span data-ttu-id="40bb5-148">右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="40bb5-148">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="40bb5-149">**[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="40bb5-149">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="40bb5-151">トークンの更新</span><span class="sxs-lookup"><span data-stu-id="40bb5-151">Refreshing tokens</span></span>

<span data-ttu-id="40bb5-152">この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。</span><span class="sxs-lookup"><span data-stu-id="40bb5-152">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="40bb5-153">これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。</span><span class="sxs-lookup"><span data-stu-id="40bb5-153">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="40bb5-154">ただし、このトークンは一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="40bb5-154">However, this token is short-lived.</span></span> <span data-ttu-id="40bb5-155">トークンが発行された後、有効期限が切れる時間になります。</span><span class="sxs-lookup"><span data-stu-id="40bb5-155">The token expires an hour after it is issued.</span></span> <span data-ttu-id="40bb5-156">ここで、更新トークンが役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-156">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="40bb5-157">更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-157">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="40bb5-158">トークンの更新を実装するために、トークン管理コードを更新します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-158">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="40bb5-159">`get_token` **/Tutorial/auth_helper py**の既存のメソッドを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="40bb5-159">Replace the existing `get_token` method in **./tutorial/auth_helper.py** with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="GetTokenSnippet":::

    <span data-ttu-id="40bb5-160">このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-160">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="40bb5-161">その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。</span><span class="sxs-lookup"><span data-stu-id="40bb5-161">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
