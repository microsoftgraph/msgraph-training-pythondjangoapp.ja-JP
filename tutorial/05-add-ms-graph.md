<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. まず **、./tutorial/graph_helper.py** にメソッドを追加して、2 つの日付の間に予定表のビューをフェッチします。 次のメソッドを追加します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    このコードの実行内容を考えましょう。

    - 呼び出される URL は `/v1.0/me/calendarview` です。
        - ヘッダーにより、結果の開始時刻と終了時刻がユーザーのタイム ゾーン `Prefer: outlook.timezone` に調整されます。
        - The `startDateTime` and parameters set the start and end of the `endDateTime` view.
        - この `$select` パラメーターは、各イベントで返されるフィールドを、ビューが実際に使用するフィールドに限定します。
        - パラメーター `$orderby` は、開始時刻で結果を並べ替える。
        - この `$top` パラメーターは、結果を 50 イベントに制限します。

1. 次のコードを **./tutorial/graph_helper.py** に追加して、Windows タイム ゾーン名に基づいて [IANA](https://www.iana.org/time-zones) タイム ゾーン識別子を参照します。 Microsoft Graph はタイム ゾーンを Windows タイム ゾーン名として返し **、Python** の日時ライブラリには IANA タイム ゾーン識別子が必要なので、これが必要です。

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. **./tutorial/views.py に次のビューを追加します**。

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

1. **./tutorial/urls.py を** 開き、既存のステートメントを次のステートメント `path` `calendar` に置き換える。

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. サインインし、ナビゲーション バー **の [予定表** ] リンクをクリックします。 すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

テンプレートを追加して、結果をユーザーに分け親しまれる方法で表示できます。

1. **./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し、次 `calendar.html` のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。

1. `calendar` **./tutorial/views.py のビューを次の** コードに置き換えます。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. ページを更新すると、アプリはイベントのテーブルをレンダリングする必要があります。

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
