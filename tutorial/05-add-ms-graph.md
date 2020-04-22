<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d807e-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="d807e-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="d807e-102">このアプリケーションでは、[要求-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/)ライブラリを使用して Microsoft Graph への呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="d807e-102">For this application, you will use the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="d807e-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="d807e-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="d807e-104">最初に、 **/tutorial/graph_helper py**にメソッドを追加して、予定表イベントを取得します。</span><span class="sxs-lookup"><span data-stu-id="d807e-104">Start by adding a method to **./tutorial/graph_helper.py** to fetch the calendar events.</span></span> <span data-ttu-id="d807e-105">次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d807e-105">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="d807e-106">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="d807e-106">Consider what this code is doing.</span></span>

    - <span data-ttu-id="d807e-107">呼び出される URL は `/v1.0/me/events` です。</span><span class="sxs-lookup"><span data-stu-id="d807e-107">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="d807e-108">パラメーター `$select`は、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="d807e-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="d807e-109">パラメーター `$orderby`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="d807e-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="d807e-110">**./Tutorial/views.py**で、 `from tutorial.graph_helper import get_user`行を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="d807e-110">In **./tutorial/views.py**, change the `from tutorial.graph_helper import get_user` line to the following.</span></span>

    ```python
    from tutorial.graph_helper import get_user, get_calendar_events
    ```

1. <span data-ttu-id="d807e-111">次のビューを/tutorial/views.py に追加します **。**</span><span class="sxs-lookup"><span data-stu-id="d807e-111">Add the following view to **./tutorial/views.py**.</span></span>

    ```python
    def calendar(request):
      context = initialize_context(request)

      token = get_token(request)

      events = get_calendar_events(token)

      context['errors'] = [
        { 'message': 'Events', 'debug': format(events)}
      ]

      return render(request, 'tutorial/home.html', context)
    ```

1. <span data-ttu-id="d807e-112">**/Tutorial/urls.py**を開き、既存`path`のステートメント`calendar`を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d807e-112">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="d807e-113">サインインして、ナビゲーションバーの [**予定表**] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="d807e-113">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="d807e-114">すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d807e-114">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="d807e-115">結果の表示</span><span class="sxs-lookup"><span data-stu-id="d807e-115">Display the results</span></span>

<span data-ttu-id="d807e-116">これで、テンプレートを追加して、よりわかりやすい方法で結果を表示することができます。</span><span class="sxs-lookup"><span data-stu-id="d807e-116">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="d807e-117">という名前`calendar.html`の **/tutorial/templates/tutorial**ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="d807e-117">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="d807e-118">これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d807e-118">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="d807e-119">/Tutorials/views.py ファイルの`import`先頭に次のステートメントを **./tutorials/views.py**追加します。</span><span class="sxs-lookup"><span data-stu-id="d807e-119">Add the following `import` statement to the top of the **./tutorials/views.py** file.</span></span>

    ```python
    import dateutil.parser
    ```

1. <span data-ttu-id="d807e-120">/Tutorial/views.py の`calendar`ビューを **./tutorial/views.py**次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d807e-120">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="d807e-121">ページを更新すると、アプリがイベントの表を表示するようになります。</span><span class="sxs-lookup"><span data-stu-id="d807e-121">Refresh the page and the app should now render a table of events.</span></span>

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
