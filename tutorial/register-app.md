<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c4f5b-101">この演習では、Azure Active Directory 管理センターを使用して、新しい Azure AD web アプリケーション登録を作成します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="c4f5b-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="c4f5b-103">**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="c4f5b-104">左側のナビゲーションで [ **Azure Active Directory** ] を選択し、[**管理**] の下にある [**アプリの登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="c4f5b-105">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="c4f5b-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="c4f5b-106">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-106">Select **New registration**.</span></span> <span data-ttu-id="c4f5b-107">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="c4f5b-108">`Python Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-108">Set **Name** to `Python Graph Tutorial`.</span></span>
    - <span data-ttu-id="c4f5b-109">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="c4f5b-110">**[リダイレクト URI]** で、最初のドロップダウン リストを `Web` に設定し、それから `http://localhost:8000/tutorial/callback` に値を設定します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:8000/tutorial/callback`.</span></span>

    ![[アプリケーションの登録] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="c4f5b-112">[**登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-112">Choose **Register**.</span></span> <span data-ttu-id="c4f5b-113">[ **Python Graph のチュートリアル**] ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-113">On the **Python Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリの登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. <span data-ttu-id="c4f5b-115">**[管理]** で **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-115">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="c4f5b-116">**[新しいクライアント シークレット]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-116">Select the **New client secret** button.</span></span> <span data-ttu-id="c4f5b-117">**[説明]** に値を入力して、**[有効期限]** のオプションのいずれかを選び、**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-117">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![[クライアントシークレットの追加] ダイアログのスクリーンショット](./images/aad-new-client-secret.png)

1. <span data-ttu-id="c4f5b-119">クライアント シークレットの値をコピーしてから、このページから移動します。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-119">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="c4f5b-120">次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-120">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="c4f5b-121">このクライアント シークレットは今後表示されないため、今必ずコピーするようにしてください。</span><span class="sxs-lookup"><span data-stu-id="c4f5b-121">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![新しく追加されたクライアントシークレットのスクリーンショット](./images/aad-copy-client-secret.png)
