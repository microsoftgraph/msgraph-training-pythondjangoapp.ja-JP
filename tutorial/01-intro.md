<!-- markdownlint-disable MD002 MD041 -->

このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Python Django Web アプリを構築する方法について説明します。

> [!TIP]
> 完成したチュートリアルをダウンロードするだけの場合は、2 つの方法でダウンロードできます。
>
> - Python クイック [スタートをダウンロードして、](https://developer.microsoft.com/graph/quick-start?platform=option-Python) 作業コードを数分で取得します。
> - GitHub リポジトリを [ダウンロードまたは複製します](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、開発マシンに [Python](https://www.python.org/) ( [ピ](https://pypi.org/project/pip/)ップ付き) がインストールされている必要があります。 Python をお持ちではない場合は、ダウンロード オプションの前のリンクを参照してください。

また、ユーザーにメールボックスを持つ個人用 Microsoft アカウント Outlook.com、Microsoft の仕事用または学校用のアカウントを使用する必要があります。 Microsoft アカウントをお持ちでない場合は、無料アカウントを取得するためのオプションが 2 つご利用できます。

- 新しい [個人用 Microsoft アカウントにサインアップできます](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。
- [365 開発者プログラムにサインアップOffice 365](https://developer.microsoft.com/office/dev-program)サブスクリプションの無料Office取得できます。

> [!NOTE]
> このチュートリアルは、Python バージョン 3.9.2 と Django バージョン 3.2 で記述されています。 このガイドの手順は、他のバージョンでも動作しますが、テストされていない場合があります。

## <a name="feedback"></a>フィードバック

このチュートリアルに関するフィードバックは [、GitHub リポジトリで提供してください](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp)。
