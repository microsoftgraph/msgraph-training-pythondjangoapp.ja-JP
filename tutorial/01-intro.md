<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="59624-101">このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Python Django Web アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="59624-101">This tutorial teaches you how to build a Python Django web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="59624-102">完成したチュートリアルをダウンロードするだけの場合は、2 つの方法でダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="59624-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="59624-103">Python クイック [スタートをダウンロードして、](https://developer.microsoft.com/graph/quick-start?platform=option-Python) 作業コードを数分で取得します。</span><span class="sxs-lookup"><span data-stu-id="59624-103">Download the [Python quick start](https://developer.microsoft.com/graph/quick-start?platform=option-Python) to get working code in minutes.</span></span>
> - <span data-ttu-id="59624-104">GitHub リポジトリを [ダウンロードまたは複製します](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)。</span><span class="sxs-lookup"><span data-stu-id="59624-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="59624-105">前提条件</span><span class="sxs-lookup"><span data-stu-id="59624-105">Prerequisites</span></span>

<span data-ttu-id="59624-106">このチュートリアルを開始する前に、開発マシンに [Python](https://www.python.org/) ( [ピ](https://pypi.org/project/pip/)ップ付き) がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="59624-106">Before you start this tutorial, you should have [Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="59624-107">Python をお持ちではない場合は、ダウンロード オプションの前のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="59624-107">If you do not have Python, visit the previous link for download options.</span></span>

<span data-ttu-id="59624-108">また、ユーザーにメールボックスを持つ個人用 Microsoft アカウント Outlook.com、Microsoft の仕事用または学校用のアカウントを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59624-108">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="59624-109">Microsoft アカウントをお持ちでない場合は、無料アカウントを取得するためのオプションが 2 つご利用できます。</span><span class="sxs-lookup"><span data-stu-id="59624-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="59624-110">新しい [個人用 Microsoft アカウントにサインアップできます](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。</span><span class="sxs-lookup"><span data-stu-id="59624-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="59624-111">[365 開発者プログラムにサインアップOffice 365](https://developer.microsoft.com/office/dev-program)サブスクリプションの無料Office取得できます。</span><span class="sxs-lookup"><span data-stu-id="59624-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="59624-112">このチュートリアルは、Python バージョン 3.9.2 と Django バージョン 3.2 で記述されています。</span><span class="sxs-lookup"><span data-stu-id="59624-112">This tutorial was written with Python version 3.9.2 and Django version 3.2.</span></span> <span data-ttu-id="59624-113">このガイドの手順は、他のバージョンでも動作しますが、テストされていない場合があります。</span><span class="sxs-lookup"><span data-stu-id="59624-113">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="59624-114">フィードバック</span><span class="sxs-lookup"><span data-stu-id="59624-114">Feedback</span></span>

<span data-ttu-id="59624-115">このチュートリアルに関するフィードバックは [、GitHub リポジトリで提供してください](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)。</span><span class="sxs-lookup"><span data-stu-id="59624-115">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>
