# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="bde81-101">完了したプロジェクトを実行する方法</span><span class="sxs-lookup"><span data-stu-id="bde81-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bde81-102">前提条件</span><span class="sxs-lookup"><span data-stu-id="bde81-102">Prerequisites</span></span>

<span data-ttu-id="bde81-103">このフォルダーで完了したプロジェクトを実行するには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="bde81-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="bde81-104">[Python](https://www.python.org/)開発用コンピューターにインストールされている ( [pip](https://pypi.org/project/pip/)を使用)。</span><span class="sxs-lookup"><span data-stu-id="bde81-104">[Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="bde81-105">Python を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="bde81-105">If you do not have Python, visit the previous link for download options.</span></span> <span data-ttu-id="bde81-106">(**注:** このチュートリアルは、Python バージョン3.7.0 および Django バージョン2.1 で記述されています。</span><span class="sxs-lookup"><span data-stu-id="bde81-106">(**Note:** This tutorial was written with Python version 3.7.0 and Django version 2.1.</span></span> <span data-ttu-id="bde81-107">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。)</span><span class="sxs-lookup"><span data-stu-id="bde81-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="bde81-108">Outlook.com 上のメールボックスを持つ個人の Microsoft アカウント、または microsoft 職場または学校のアカウントのいずれか。</span><span class="sxs-lookup"><span data-stu-id="bde81-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="bde81-109">Microsoft アカウントを持っていない場合は、無料のアカウントを取得するためのオプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="bde81-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="bde81-110">[新しい個人用 Microsoft アカウントにサインアップ](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)することができます。</span><span class="sxs-lookup"><span data-stu-id="bde81-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="bde81-111">[office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="bde81-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="bde81-112">web アプリケーションをアプリケーション登録ポータルに登録する</span><span class="sxs-lookup"><span data-stu-id="bde81-112">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="bde81-113">ブラウザーを開き、[アプリケーション登録ポータル](https://apps.dev.microsoft.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="bde81-113">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="bde81-114">**個人アカウント**(別名: Microsoft アカウント) または**職場または学校のアカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="bde81-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="bde81-115">ページの上部にある [**アプリの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bde81-115">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="bde81-116">**注:** ページに [**アプリの追加**] ボタンが複数表示されている場合は、[収束した**アプリ**] リストに対応するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="bde81-116">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="bde81-117">[**アプリケーションの登録**] ページで、[**アプリケーション名**] を [ **Python Graph チュートリアル**] に設定し、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bde81-117">On the **Register your application** page, set the **Application Name** to **Python Graph Tutorial** and select **Create**.</span></span>

    ![アプリ登録ポータル web サイトで新しいアプリを作成するスクリーンショット](/Images/arp-create-app-01.png)

1. <span data-ttu-id="bde81-119">[ **Python Graph のチュートリアル登録**] ページの [**プロパティ**] セクションで、後で必要になるように**アプリケーション Id**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="bde81-119">On the **Python Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![新しく作成されたアプリケーションの ID のスクリーンショット](/Images/arp-create-app-02.png)

1. <span data-ttu-id="bde81-121">[**アプリケーションシークレット**] セクションまで下にスクロールします。</span><span class="sxs-lookup"><span data-stu-id="bde81-121">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="bde81-122">[**新しいパスワードの生成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bde81-122">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="bde81-123">[**新しいパスワードが生成さ**れました] ダイアログで、後で必要になるように、ボックスの内容をコピーします。</span><span class="sxs-lookup"><span data-stu-id="bde81-123">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="bde81-124">**重要:** このパスワードは今後表示されないため、ここでコピーしてください。</span><span class="sxs-lookup"><span data-stu-id="bde81-124">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![新しく作成されたアプリケーションのパスワードのスクリーンショット](/Images/arp-create-app-03.png)

1. <span data-ttu-id="bde81-126">[**プラットフォーム**] セクションまで下にスクロールします。</span><span class="sxs-lookup"><span data-stu-id="bde81-126">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="bde81-127">[**プラットフォームの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bde81-127">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="bde81-128">[**プラットフォームの追加**] ダイアログで、[ **Web**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bde81-128">In the **Add Platform** dialog, select **Web**.</span></span>

        ![アプリのプラットフォームを作成するスクリーンショット](/Images/arp-create-app-04.png)

    1. <span data-ttu-id="bde81-130">[ **Web**プラットフォーム] ボックスに、 `http://localhost:8000/tutorial/callback` **リダイレクト url**の url を入力します。</span><span class="sxs-lookup"><span data-stu-id="bde81-130">In the **Web** platform box, enter the URL `http://localhost:8000/tutorial/callback` for the **Redirect URLs**.</span></span>

        ![アプリケーションに新たに追加された Web プラットフォームのスクリーンショット](/Images/arp-create-app-05.png)

1. <span data-ttu-id="bde81-132">ページの一番下までスクロールして、[**保存**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="bde81-132">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="bde81-133">サンプルを構成する</span><span class="sxs-lookup"><span data-stu-id="bde81-133">Configure the sample</span></span>

1. <span data-ttu-id="bde81-134">ファイルの`oauth_settings.yml.example`名前を`oauth_settings.yml`に変更します。</span><span class="sxs-lookup"><span data-stu-id="bde81-134">Rename the `oauth_settings.yml.example` file to `oauth_settings.yml`.</span></span>
1. <span data-ttu-id="bde81-135">`oauth_settings.yml`ファイルを編集し、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="bde81-135">Edit the `oauth_settings.yml` file and make the following changes.</span></span>
    1. <span data-ttu-id="bde81-136">を`YOUR_APP_ID_HERE`アプリ登録ポータルで取得した**アプリケーション Id**に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bde81-136">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="bde81-137">を`YOUR_APP_PASSWORD_HERE`アプリ登録ポータルから取得したパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bde81-137">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="bde81-138">コマンドラインインターフェイス (CLI) で、このディレクトリに移動し、次のコマンドを実行して要件をインストールします。</span><span class="sxs-lookup"><span data-stu-id="bde81-138">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    pip install -r requirements.txt
    ```

1. <span data-ttu-id="bde81-139">CLI で、次のコマンドを実行して、アプリのデータベースを初期化します。</span><span class="sxs-lookup"><span data-stu-id="bde81-139">In your CLI, run the following command to initialize the app's database.</span></span>

    ```Shell
    python manage.py migrate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="bde81-140">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="bde81-140">Run the sample</span></span>

1. <span data-ttu-id="bde81-141">CLI で次のコマンドを実行して、アプリケーションを起動します。</span><span class="sxs-lookup"><span data-stu-id="bde81-141">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="bde81-142">Web ブラウザーを開き、`http://localhost:8000/tutorial` を参照します。</span><span class="sxs-lookup"><span data-stu-id="bde81-142">Open a browser and browse to `http://localhost:8000/tutorial`.</span></span>