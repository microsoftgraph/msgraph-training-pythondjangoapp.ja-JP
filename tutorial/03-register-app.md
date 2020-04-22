<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d3f84-101">この演習では、Azure Active Directory 管理センターを使用して、新しい Azure AD web アプリケーション登録を作成します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="d3f84-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)へ移動します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="d3f84-103">**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="d3f84-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="d3f84-104">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="d3f84-105">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="d3f84-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="d3f84-106">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-106">Select **New registration**.</span></span> <span data-ttu-id="d3f84-107">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="d3f84-108">`Python Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-108">Set **Name** to `Python Graph Tutorial`.</span></span>
    - <span data-ttu-id="d3f84-109">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="d3f84-110">**[リダイレクト URI]** で、最初のドロップダウン リストを `Web` に設定し、それから `http://localhost:8000/callback` に値を設定します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:8000/callback`.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="d3f84-112">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-112">Select **Register**.</span></span> <span data-ttu-id="d3f84-113">[ **Python Graph のチュートリアル**] ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="d3f84-113">On the **Python Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. <span data-ttu-id="d3f84-115">**[管理]** で **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-115">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="d3f84-116">
            \*\*[新しいクライアント シークレット]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-116">Select the **New client secret** button.</span></span> <span data-ttu-id="d3f84-117">**[説明]** に値を入力して、**[有効期限]** のオプションのいずれかを選び、**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d3f84-117">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![[クライアントシークレットの追加] ダイアログのスクリーンショット](./images/aad-new-client-secret.png)

1. <span data-ttu-id="d3f84-119">このページを離れる前に、クライアント シークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="d3f84-119">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="d3f84-120">次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="d3f84-120">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="d3f84-121">このクライアント シークレットは今後表示されないため、この段階で必ずコピーするようにしてください。</span><span class="sxs-lookup"><span data-stu-id="d3f84-121">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![新規追加されたクライアント シークレットのスクリーンショット](./images/aad-copy-client-secret.png)
