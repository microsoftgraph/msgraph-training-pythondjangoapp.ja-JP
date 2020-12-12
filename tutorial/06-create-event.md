<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="827fe-101">このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="827fe-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="827fe-102">**./tutorial/graph_helper.py** に次のメソッドを追加して、新しいイベントを作成します。</span><span class="sxs-lookup"><span data-stu-id="827fe-102">Add the following method to **./tutorial/graph_helper.py** to create a new event.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="CreateEventSnippet":::

## <a name="create-a-new-event-form"></a><span data-ttu-id="827fe-103">新しいイベント フォームを作成する</span><span class="sxs-lookup"><span data-stu-id="827fe-103">Create a new event form</span></span>

1. <span data-ttu-id="827fe-104">**./tutorial/templates/tutorial** ディレクトリに新しいファイルを作成し、次 `newevent.html` のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="827fe-104">Create a new file in the **./tutorial/templates/tutorial** directory named `newevent.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/newevent.html" id="NewEventSnippet":::

1. <span data-ttu-id="827fe-105">**./tutorial/views.py に次のビューを追加します**。</span><span class="sxs-lookup"><span data-stu-id="827fe-105">Add the following view to **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="NewEventViewSnippet":::

1. <span data-ttu-id="827fe-106">**./tutorial/urls.py を** 開き、ビューの `path` ステートメントを追加 `newevent` します。</span><span class="sxs-lookup"><span data-stu-id="827fe-106">Open **./tutorial/urls.py** and add a `path` statements for the `newevent` view.</span></span>

    ```python
    path('calendar/new', views.newevent, name='newevent'),
    ```

1. <span data-ttu-id="827fe-107">変更を保存し、アプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="827fe-107">Save your changes and refresh the app.</span></span> <span data-ttu-id="827fe-108">[予定表 **] ページ** で、[新しいイベント] **ボタンを選択** します。</span><span class="sxs-lookup"><span data-stu-id="827fe-108">On the **Calendar** page, select the **New event** button.</span></span> <span data-ttu-id="827fe-109">フォームに入力し、[作成] を選択 **して** イベントを作成します。</span><span class="sxs-lookup"><span data-stu-id="827fe-109">Fill in the form and select **Create** to create the event.</span></span>

    ![新しいイベント フォームのスクリーンショット](images/create-event-01.png)
