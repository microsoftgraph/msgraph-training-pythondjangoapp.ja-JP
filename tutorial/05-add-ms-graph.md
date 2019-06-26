<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c4a8c-101">この演習では、Microsoft Graph をアプリケーションに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="c4a8c-102">このアプリケーションでは、[要求-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/)ライブラリを使用して Microsoft Graph への呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-102">For this application, you will use the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="c4a8c-103">Outlook から予定表のイベントを取得する</span><span class="sxs-lookup"><span data-stu-id="c4a8c-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="c4a8c-104">最初に、予定表イベント`./tutorial/graph_helper.py`を取得するメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-104">Start by adding a method to `./tutorial/graph_helper.py` to fetch the calendar events.</span></span> <span data-ttu-id="c4a8c-105">次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-105">Add the following method.</span></span>

```python
def get_calendar_events(token):
  graph_client = OAuth2Session(token=token)

  # Configure query parameters to
  # modify the results
  query_params = {
    '$select': 'subject,organizer,start,end',
    '$orderby': 'createdDateTime DESC'
  }

  # Send GET to /me/events
  events = graph_client.get('{0}/me/events'.format(graph_url), params=query_params)
  # Return the JSON result
  return events.json()
```

<span data-ttu-id="c4a8c-106">このコードの内容を検討してください。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="c4a8c-107">呼び出し先の URL は`/v1.0/me/events`になります。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-107">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="c4a8c-108">パラメーター `$select`は、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="c4a8c-109">パラメーター `$orderby`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="c4a8c-110">ここで、予定表ビューを作成します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-110">Now create a calendar view.</span></span> <span data-ttu-id="c4a8c-111">で`./tutorial/views.py`、最初に`from tutorial.graph_helper import get_user`行を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-111">In `./tutorial/views.py`, first change the `from tutorial.graph_helper import get_user` line to the following.</span></span>

```python
from tutorial.graph_helper import get_user, get_calendar_events
```

<span data-ttu-id="c4a8c-112">次に、以下のビューを`./tutorial/views.py`に追加します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-112">Then, add the following view to `./tutorial/views.py`.</span></span>

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

<span data-ttu-id="c4a8c-113">を`./tutorial/urls.py`更新して、この新しいビューを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-113">Update `./tutorial/urls.py` to add this new view.</span></span>

```python
path('calendar', views.calendar, name='calendar'),
```

<span data-ttu-id="c4a8c-114">最後に、[ `./tutorial/templates/tutorial/layout.html`このビューにリンクする**予定表**] リンクを更新します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-114">Finally, update  the **Calendar** link in `./tutorial/templates/tutorial/layout.html` to link to this view.</span></span> <span data-ttu-id="c4a8c-115">行を`<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>`次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-115">Replace the `<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>` line with the following.</span></span>

```html
<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="{% url 'calendar' %}">Calendar</a>
```

<span data-ttu-id="c4a8c-116">これで、これをテストできます。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-116">Now you can test this.</span></span> <span data-ttu-id="c4a8c-117">サインインして、ナビゲーションバーの [**予定表**] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-117">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="c4a8c-118">すべてが動作する場合は、ユーザーの予定表にイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="c4a8c-119">結果を表示する</span><span class="sxs-lookup"><span data-stu-id="c4a8c-119">Display the results</span></span>

<span data-ttu-id="c4a8c-120">これで、テンプレートを追加して、よりわかりやすい方法で結果を表示することができます。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-120">Now you can add a template to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="c4a8c-121">という名前`./tutorial/templates/tutorial` `calendar.html`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-121">Create a new file in the `./tutorial/templates/tutorial` directory named `calendar.html` and add the following code.</span></span>

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    {% if events %}
      {% for event in events %}
        <tr>
          <td>{{ event.organizer.emailAddress.name }}</td>
          <td>{{ event.subject }}</td>
          <td>{{ event.start.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
          <td>{{ event.end.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
        </tr>
      {% endfor %}
    {% endif %}
  </tbody>
</table>
{% endblock %}
```

<span data-ttu-id="c4a8c-122">これにより、イベントのコレクションをループ処理して、テーブル行を1つずつ追加します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-122">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="c4a8c-123">次`import`のステートメントを`./tutorials/views.py`ファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-123">Add the following `import` statement to the top of the `./tutorials/views.py` file.</span></span>

```python
import dateutil.parser
```

<span data-ttu-id="c4a8c-124">の`calendar` `./tutorial/views.py`ビューを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-124">Replace the `calendar` view in `./tutorial/views.py` with the following code.</span></span>

```python
def calendar(request):
  context = initialize_context(request)

  token = get_token(request)

  events = get_calendar_events(token)

  if events:
    # Convert the ISO 8601 date times to a datetime object
    # This allows the Django template to format the value nicely
    for event in events['value']:
      event['start']['dateTime'] = dateutil.parser.parse(event['start']['dateTime'])
      event['end']['dateTime'] = dateutil.parser.parse(event['end']['dateTime'])

    context['events'] = events['value']

  return render(request, 'tutorial/calendar.html', context)
```

<span data-ttu-id="c4a8c-125">ページを更新すると、アプリがイベントの表を表示するようになります。</span><span class="sxs-lookup"><span data-stu-id="c4a8c-125">Refresh the page and the app should now render a table of events.</span></span>

![イベントの表のスクリーンショット](./images/add-msgraph-01.png)
