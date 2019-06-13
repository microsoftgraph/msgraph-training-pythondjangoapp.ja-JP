<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="450bf-101">このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Python Django web アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="450bf-101">This tutorial teaches you how to build a Python Django web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="450bf-102">完成したチュートリアルをダウンロードするだけで済む場合は、2つの方法でダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="450bf-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="450bf-103">作業コードを分単位で取得するために、 [Python クイックスタート](https://developer.microsoft.com/graph/quick-start?platform=option-Python)をダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="450bf-103">Download the [Python quick start](https://developer.microsoft.com/graph/quick-start?platform=option-Python) to get working code in minutes.</span></span>
> - <span data-ttu-id="450bf-104">[GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)をダウンロードするか、クローンを作成します。</span><span class="sxs-lookup"><span data-stu-id="450bf-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="450bf-105">前提条件</span><span class="sxs-lookup"><span data-stu-id="450bf-105">Prerequisites</span></span>

<span data-ttu-id="450bf-106">このチュートリアルを開始する前に、開発コンピューターに[Python](https://www.python.org/) ( [pip](https://pypi.org/project/pip/)あり) がインストールされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="450bf-106">Before you start this tutorial, you should have [Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="450bf-107">Python を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="450bf-107">If you do not have Python, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="450bf-108">このチュートリアルは、Python バージョン3.7.0 および Django version 2.2.2 で記述されています。</span><span class="sxs-lookup"><span data-stu-id="450bf-108">This tutorial was written with Python version 3.7.0 and Django version 2.2.2.</span></span> <span data-ttu-id="450bf-109">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。</span><span class="sxs-lookup"><span data-stu-id="450bf-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="450bf-110">フィードバック</span><span class="sxs-lookup"><span data-stu-id="450bf-110">Feedback</span></span>

<span data-ttu-id="450bf-111">このチュートリアルに関するフィードバックは、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)に記入してください。</span><span class="sxs-lookup"><span data-stu-id="450bf-111">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>
