<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4d01f-101">この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d01f-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="4d01f-102">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="4d01f-102">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="4d01f-103">まず **、./tutorial/graph_helper.py** にメソッドを追加して、2 つの日付の間に予定表のビューをフェッチします。</span><span class="sxs-lookup"><span data-stu-id="4d01f-103">Start by adding a method to **./tutorial/graph_helper.py** to fetch a view of the calendar between two dates.</span></span> <span data-ttu-id="4d01f-104">次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="4d01f-104">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="4d01f-105">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="4d01f-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="4d01f-106">呼び出される URL は `/v1.0/me/calendarview` です。</span><span class="sxs-lookup"><span data-stu-id="4d01f-106">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="4d01f-107">ヘッダーにより、結果の開始時刻と終了時刻がユーザーのタイム ゾーン `Prefer: outlook.timezone` に調整されます。</span><span class="sxs-lookup"><span data-stu-id="4d01f-107">The `Prefer: outlook.timezone` header causes the start and end times in the results to be adjusted to the user's time zone.</span></span>
        - <span data-ttu-id="4d01f-108">The `startDateTime` and parameters set the start and end of the `endDateTime` view.</span><span class="sxs-lookup"><span data-stu-id="4d01f-108">The `startDateTime` and `endDateTime` parameters set the start and end of the view.</span></span>
        - <span data-ttu-id="4d01f-109">この `$select` パラメーターは、各イベントで返されるフィールドを、ビューが実際に使用するフィールドに限定します。</span><span class="sxs-lookup"><span data-stu-id="4d01f-109">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
        - <span data-ttu-id="4d01f-110">パラメーター `$orderby` は、開始時刻で結果を並べ替える。</span><span class="sxs-lookup"><span data-stu-id="4d01f-110">The `$orderby` parameter sorts the results by start time.</span></span>
        - <span data-ttu-id="4d01f-111">この `$top` パラメーターは、結果を 50 イベントに制限します。</span><span class="sxs-lookup"><span data-stu-id="4d01f-111">The `$top` parameter limits the results to 50 events.</span></span>

1. <span data-ttu-id="4d01f-112">次のコードを **./tutorial/graph_helper.py** に追加して、Windows タイム ゾーン名に基づいて [IANA](https://www.iana.org/time-zones) タイム ゾーン識別子を参照します。</span><span class="sxs-lookup"><span data-stu-id="4d01f-112">Add the following code to **./tutorial/graph_helper.py** to lookup an [IANA time zone identifier](https://www.iana.org/time-zones) based on a Windows time zone name.</span></span> <span data-ttu-id="4d01f-113">Microsoft Graph はタイム ゾーンを Windows タイム ゾーン名として返し **、Python** の日時ライブラリには IANA タイム ゾーン識別子が必要なので、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="4d01f-113">This is necessary because Microsoft Graph can return time zones as Windows time zone names, and the Python **datetime** library requires IANA time zone identifiers.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. <span data-ttu-id="4d01f-114">**./tutorial/views.py に次のビューを追加します**。</span><span class="sxs-lookup"><span data-stu-id="4d01f-114">Add the following view to **./tutorial/views.py**.</span></span>

    ```python
    def calendar(request):
      context = initialize_context(request)
      user = context['user']

      # Load the user's time zone
      # Microsoft Graph can return the user's time zone as either
      # a Windows time zone name or an IANA time zone identifier
      # Python datetime requires IANA, so convert Windows to IANA
      time_zone = get_iana_from_windows(user['timeZone'])
      tz_info = tz.gettz(time_zone)

      # Get midnight today in user's time zone
      today = datetime.now(tz_info).replace(
        hour=0,
        minute=0,
        second=0,
        microsecond=0)

      # Based on today, get the start of the week (Sunday)
      if (today.weekday() != 6):
        start = today - timedelta(days=today.isoweekday())
      else:
        start = today

      end = start + timedelta(days=7)

      token = get_token(request)

      events = get_calendar_events(
        token,
        start.isoformat(timespec='seconds'),
        end.isoformat(timespec='seconds'),
        user['timeZone'])

      context['errors'] = [
        { 'message': 'Events', 'debug': format(events)}
      ]

      return render(request, 'tutorial/home.html', context)
    ```

1. <span data-ttu-id="4d01f-115">**./tutorial/urls.py を** 開き、既存のステートメントを次のステートメント `path` `calendar` に置き換える。</span><span class="sxs-lookup"><span data-stu-id="4d01f-115">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="4d01f-116">サインインし、ナビゲーション バー **の [予定表** ] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="4d01f-116">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="4d01f-117">すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4d01f-117">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="4d01f-118">結果の表示</span><span class="sxs-lookup"><span data-stu-id="4d01f-118">Display the results</span></span>

<span data-ttu-id="4d01f-119">テンプレートを追加して、結果をユーザーに分け親しまれる方法で表示できます。</span><span class="sxs-lookup"><span data-stu-id="4d01f-119">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="4d01f-120">**./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し、次 `calendar.html` のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="4d01f-120">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="4d01f-121">これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。</span><span class="sxs-lookup"><span data-stu-id="4d01f-121">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="4d01f-122">`calendar` **./tutorial/views.py のビューを次の** コードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4d01f-122">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="4d01f-123">ページを更新すると、アプリはイベントのテーブルをレンダリングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d01f-123">Refresh the page and the app should now render a table of events.</span></span>

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
