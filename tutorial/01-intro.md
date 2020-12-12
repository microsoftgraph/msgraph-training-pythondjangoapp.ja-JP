<!-- markdownlint-disable MD002 MD041 -->

このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Python Django Web アプリを構築する方法について説明します。

> [!TIP]
> 完成したチュートリアルをダウンロードするだけの場合は、2 つの方法でダウンロードできます。
>
> - Python クイック [スタートをダウンロードして、](https://developer.microsoft.com/graph/quick-start?platform=option-Python) 作業コードを数分で取得します。
> - GitHub リポジトリを [ダウンロードまたは複製します](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、開発用コンピューターに [Python](https://www.python.org/) [(python](https://pypi.org/project/pip/)付き) がインストールされている必要があります。 Python がない場合は、ダウンロード オプションの前のリンクを参照してください。

また、メールボックスを持つ個人用の Microsoft アカウントが Outlook.com Microsoft の仕事用アカウントまたは学校アカウントである必要があります。 Microsoft アカウントをお持ちない場合は、無料アカウントを取得するためのオプションが 2 つ提供されています。

- 新しい [個人用 Microsoft アカウントにサインアップできます](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- Office [365 開発者プログラムにサインアップして、365](https://developer.microsoft.com/office/dev-program) サブスクリプションを無料Office取得できます。

> [!NOTE]
> このチュートリアルは、Python バージョン 3.9.0 および Django バージョン 3.1.4 で記述されています。 このガイドの手順は他のバージョンでも動作する可能性がありますが、テストは行ってはいではありません。

## <a name="feedback"></a>フィードバック

このチュートリアルに関するフィードバックは [、GitHub リポジトリで提供してください](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)。
