<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込みます。 このアプリケーションでは、[要求-oauthlib](https://requests-oauthlib.readthedocs.io/en/latest/)ライブラリを使用して Microsoft Graph への呼び出しを行います。

## <a name="get-calendar-events-from-outlook"></a>Outlook から予定表のイベントを取得する

最初に、予定表イベント`./tutorial/graph_helper.py`を取得するメソッドを追加します。 次のメソッドを追加します。

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

このコードの内容を検討してください。

- 呼び出し先の URL は`/v1.0/me/events`になります。
- パラメーター `$select`は、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。
- パラメーター `$orderby`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。

ここで、予定表ビューを作成します。 最初に、 `from tutorial.graph_helper import get_user`行を次のように変更します。

```python
from tutorial.graph_helper import get_user, get_calendar_events
```

次に、以下のビューを`./tutorial/views.py`に追加します。

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

を`./tutorial/urls.py`更新して、この新しいビューを追加します。

```python
path('calendar', views.calendar, name='calendar'),
```

最後に、[ `./tutorial/templates/tutorial/layout.html`このビューにリンクする**予定表**] リンクを更新します。 行を`<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>`次のように置き換えます。

```html
<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="{% url 'calendar' %}">Calendar</a>
```

これで、これをテストできます。 サインインして、ナビゲーションバーの [**予定表**] リンクをクリックします。 すべてが動作する場合は、ユーザーの予定表にイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果を表示する

これで、テンプレートを追加して、よりわかりやすい方法で結果を表示することができます。 という名前`./tutorial/templates/tutorial` `calendar.html`のディレクトリに新しいファイルを作成し、次のコードを追加します。

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

これにより、イベントのコレクションをループ処理して、テーブル行を1つずつ追加します。 次`import`のステートメントを`./tutorials/views.py`ファイルの先頭に追加します。

```python
import dateutil.parser
```

の`calendar` `./tutorial/views.py`ビューを次のコードで置き換えます。

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

ページを更新すると、アプリがイベントの表を表示するようになります。

![イベントの表のスクリーンショット](./images/add-msgraph-01.png)