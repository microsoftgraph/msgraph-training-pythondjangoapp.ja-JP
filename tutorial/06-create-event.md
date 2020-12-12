<!-- markdownlint-disable MD002 MD041 -->

このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。

1. **./tutorial/graph_helper.py** に次のメソッドを追加して、新しいイベントを作成します。

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="CreateEventSnippet":::

## <a name="create-a-new-event-form"></a>新しいイベント フォームを作成する

1. **./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し、次 `newevent.html` のコードを追加します。

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/newevent.html" id="NewEventSnippet":::

1. **./tutorial/views.py に次のビューを追加します**。

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="NewEventViewSnippet":::

1. **./tutorial/urls.py を** 開き、ビューの `path` ステートメントを追加 `newevent` します。

    ```python
    path('calendar/new', views.newevent, name='newevent'),
    ```

1. 変更を保存し、アプリを更新します。 [予定表 **] ページ** で、[新しいイベント] **ボタンを選択** します。 フォームに入力し、[作成] を選択 **して** イベントを作成します。

    ![新しいイベント フォームのスクリーンショット](images/create-event-01.png)
