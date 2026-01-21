# Lab 3: Real-Time Product Clickstream Monitoring and Insights using Microsoft Fabric

## Task 1: Set Up an Eventstream and Create Custom Endpoints

1.  Now, click on **RealTimeWorkspaceXXX** on the left-sided navigation
    pane.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image1.png)

2.  In the Workspaces pane, select **+ New item**. In the **Filter by
    item type** search box, enter +++**Eventstream+++** and select the
    **Eventstream** item

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image2.png)

3.  Enter **+++clickstream_eventstream+++** as the eventstream name and
    select **Create**. 

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image3.png)

4.  On the Screen **Design a flow to ingest, transform, and route
    streaming events** click on **Use custom Endpoint**. This will
    create an event hub connected to the Eventstream.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image4.png)

5.  Insert +++**CustomEndpoint-clickstream**+++ as the source name and
    the click on **Add**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image5.png)

6.  Click on the **Publish** button.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image6.png)

7.  On the **Eventstream** pane, select the **keys** under
    the **Details**, select **SAS key Authentication ,** copy
    the **Event hub name**, **connection strings-primarykey** and paste
    them on a notepad, as you need them in the upcoming task

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image7.png)

8.  Now, click on **RealTimeWorkspaceXXX** on the left-sided navigation
    pane.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image8.png)

## Task 2: Import Clickstream Simulator data Notebook

1.  On the **RealTimeWorkspace** page, from the menu bar, navigate and
    click on **-\>|Import** button, then select **Notebook** and
    select **From this computer** as shown in the below image.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image9.png)

2.  Select **Upload** from the **Import status** pane that appears on
    the right side of the screen.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image10.png)

3.  Navigate and select **Clickstream Simulator** notebooks
    from **C:\LabFiles**and click on the **Open** button.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image11.png)

4.  Then, select the **Clickstream Simulator**  notebook.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)

5.  To start the notebook, run the 1^(st) cell.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image13.png)

6.  Run the 2^(nd) cell.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image14.png)

7.  In the **3^(rd)** cell paste the **connection string of your custom
    app source and EventHubName** (the value that you have saved in your
    notepad in the , select the **Run** icon that appears on the left
    side of the cell

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image15.png)

8.  Run the 4^(th) cell.

> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image16.png)

9.  Select the 5th cell and run the cell.

> ![A screenshot of a computer program AI-generated content may be
> incorrect.](./media/image17.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image18.png)

10. Now, click on **clickstream_eventstream** on the top navigation
    pane.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image19.png)

11. In the event stream authoring canvas, select the **Edit**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image20.png)

12. Click on the node **Transform events or add Destination** and
    select **Eventhouse** from the menu.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image21.png)

13. Provide the following values in the pane **Eventhouse**. Click the
    button **Save** after you entered all the values.

[TABLE]

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image22.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image23.png)

14. Click on the button **Publish** that is located in the toolbar at
    the top of the screen.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image24.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image25.png)

15. Now, click on **RealTimeWorkspaceXXX** on the left-sided navigation
    pane. Select **L400-Eventhouse**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image26.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image27.png)

## Task 3: Knowledge of Kusto Query Language (KQL) to analyze the data.

1.  Now, click on **RealTimeWorkspaceXXX** on the left-sided navigation
    pane and select **L400_KQL_Queryset**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image28.png)

2.  Create a new tab within the queryset by clicking on the ***+* icon**

3.  In the query editor, paste the provided code to top 3 products by
    demand, then click **Run** to execute the query. After execution,
    the results will be displayed.

//top 3 products by demand

//summarize is similar to group by in sql

clickstream

| where event_type in ("purchase_completed", "add_to_cart",
"product_click", "checkout_initiated")

| summarize TotalDemand = count() by product_id

| top 3 by TotalDemand desc

| project TotalDemand, product_id

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

4.  Create a new tab within the queryset by clicking on the ***+* icon**

5.  In the query editor, paste the provided code to see the cost of
    those top 3 products, then click **Run** to execute the query. After
    execution, the results will be displayed.

//let\`s see the cost of those top 3 products

clickstream

| where event_type in ("purchase_completed", "add_to_cart",
"product_click", "checkout_initiated")

| summarize TotalDemand = count() by product_id

| top 3 by TotalDemand desc

| join products_silver on $left.product_id == $right.ProductId

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image30.png)

6.  Create a new tab within the queryset by clicking on the ***+* icon**

7.  In the query editor, paste the provided code to create a copy of the
    table products_silver to test out the cost increase, then click
    **Run** to execute the query. After execution, the results will be
    displayed.

//let\`s create a copy of the table products_silver to test out the cost
increase

.create table product_copy (ProductId:string, ProductName:string,
SKU:string, Brand:string, Category:string, UnitCost:int)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image31.png)

8.  Create a new tab within the queryset by clicking on the ***+* icon**

9.  In the query editor, paste the provided code to recommended for one
    time load, now our table looks like the product table, then click
    **Run** to execute the query. After execution, the results will be
    displayed.

> //recommended for one time load, now our table looks like the product
> table
>
> .set-or-replace product_copy \<|
>
> products_silver

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image32.png)

10. Create a new tab within the queryset by clicking on the ***+* icon**

11. In the query editor, paste the provided code, then click **Run** to
    execute the query. After execution, the results will be displayed.

//let\`s also save the result of the TOP 3 products in another table,
because we\`ll need it in the update command, the let Delete or Append
only accepts the table name you are modifying

.create table Top3Products (ProductId:string, ProductName:string,
SKU:string, Brand:string, Category:string, UnitCost:int)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image33.png)

12. Create a new tab within the queryset by clicking on the ***+* icon**

13. In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

.set-or-replace Top3Products \<|

clickstream

| where event_type in ("purchase_completed", "add_to_cart",
"product_click", "checkout_initiated")

| summarize TotalDemand = count() by product_id

| top 3 by TotalDemand desc

| join products_silver on $left.product_id == $right.ProductId

| project ProductId, ProductName, SKU, Brand, Category, UnitCost;

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image34.png)

14. In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

//to update we\`ll use a .update command that uses append and delete

.update table product_copy delete top3Products append
Top3ProductsWithUpdatedCost \<|

let top3Products = product_copy

| where ProductId in (Top3Products | project ProductId );

let Top3ProductsWithUpdatedCost = product_copy

| where ProductId in (Top3Products | project ProductId )

| extend UnitCost = toint(UnitCost + UnitCost \* 0.15);

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image35.png)

15. Create a new tab within the queryset by clicking on the ***+* icon**

16. In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

//great, now run again the query that finds out the Top 3 products, they
aren\`t the same right?

// that is why we need to have a pipe

//in the pipe we\`ll create 1 KQL acitivity where we\`ll update our top
3 products daily

.set-or-replace Top3Products \<|

clickstream

| where event_type in ("purchase_completed", "add_to_cart",
"product_click", "checkout_initiated") and ingestion_time() \> ago(1d)

| summarize TotalDemand = count() by product_id

| top 3 by TotalDemand desc

| join products_silver on $left.product_id == $right.ProductId

| project ProductId, ProductName, SKU, Brand, Category, UnitCost;

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image36.png)

17. Create a new tab within the queryset by clicking on the ***+* icon**

18. In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

//the 2nd KQL activity will update the products table with the cost
increased by 15% for the most demanded products

.update table product_copy delete top3Products append
Top3ProductsWithUpdatedCost \<|

let top3Products = product_copy

| where ProductId in (Top3Products | project ProductId );

let Top3ProductsWithUpdatedCost = product_copy

| where ProductId in (Top3Products | project ProductId )

| extend UnitCost = toint(UnitCost + UnitCost \* 0.15);

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image37.png)

## Task 4: Create a Growth Opportunity Report

1.  Create a new tab within the queryset by clicking on the ***+* icon**

2.  In the query editor, paste the provided code to get demand product,
    then click **Run** to execute the query. After execution, the
    results will be displayed.

> //in demand product
>
> clickstream
>
> | where event_type in ("purchase_completed", "add_to_cart",
> "product_click", "checkout_initiated")
>
> | summarize TotalDemand = count() by product_id
>
> | top 1 by TotalDemand desc
>
> | join products_silver on $left.product_id == $right.ProductId
>
> | project ProductName

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image38.png)

3.  Create a new tab within the queryset by clicking on the ***+* icon**

4.  In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

//website with highest traffic

> //you mean referral platform
>
> clickstream
>
> | where event_type in ("purchase_completed", "add_to_cart",
> "product_click", "checkout_initiated") and
> isnotempty(referral_platform)
>
> | summarize TotalDemand = count() by referral_platform
>
> | top 1 by TotalDemand desc
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image39.png)

5.  Create a new tab within the queryset by clicking on the ***+* icon**

6.  In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

> // product clicks over time
>
> clickstream
>
> | where event_type == "product_click"
>
> | extend EventTime = todatetime(timestamp)
>
> | extend ClickTime = bin(EventTime, 1h)
>
> | summarize NumberOfClicks = count() by product_id, ClickTime
>
> | order by ClickTime asc

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image40.png)

7.  Create a new tab within the queryset by clicking on the ***+* icon**

8.  In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

> //product clicks over time
>
> clickstream
>
> | where event_type in ("product_click")
>
> | extend ClickTime = bin(todatetime(timestamp), 1h)
>
> | summarize NumberOfClicks = count() by product_id, ClickTime
>
> | order by ClickTime asc

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image41.png)

9.  Create a new tab within the queryset by clicking on the ***+* icon**

10. In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

> //website traffic distribution
>
> clickstream
>
> | where isnotempty(referral_platform)
>
> | summarize Websites = count() by referral_platform

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image42.png)

11. Create a new tab within the queryset by clicking on the ***+* icon**

12. In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

> //device traffic distribution
>
> clickstream
>
> | where isnotempty(referral_source_type)
>
> | summarize Traffic = count() by referral_source_type

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image43.png)

13. Create a new tab within the queryset by clicking on the ***+* icon**

14. In the query editor, copy and paste the following code. Click on
    the **Run** button to execute the query. After the query is
    executed, you will see the results.

> //forecast the temperature
>
> manufacturing
>
> | extend timestamp = todatetime(timestamp)
>
> | where DefectProbability == "Anomaly"
>
> // Creates a time series of average temperature values, grouped into
> 1-hour intervals over the past 1 day
>
> | make-series avg_temp=avg(Temperature) on timestamp from ago(3d) to
> now() step 6m
>
> // Applies anomaly detection to the temperature series, 7h
>
> | extend forecast = series_decompose_forecast(avg_temp,48)
>
> | render timechart

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image44.png)

## Task 5: Create Dataflow Gen2

1.  Now, click on **RealTimeWorkspaceXXX** on the left-sided navigation
    pane.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image45.png)

1.  In the Workspaces pane, select **+ New item** and select the
    **Dataflow Gen2**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image46.png)

2.  Enter **+++RTI_Dataflow+++** as the new Dataflow Gen2 name and
    select **Create**. 

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image47.png)

3.  From the **Home** tab, select **Get data** and then
    the **More...** option to upload the tables into Dataflow Gen2

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image48.png)

4.  Select clickstream table

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image49.png)

5.  On the Home window, select **Save & run** and click on **Save &
    run** button

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image50.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image51.png)

6.  Now, click on **RealTimeWorkspaceXXX** on the left-sided navigation
    pane.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image52.png)

7.  Select **OneLake catalog** and select the existing KQL
    Databse, **L400_Eventhouse**, on the data destination configuration
    page that appears.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image53.png)

2.  Select **RealTimeworkspace** in the left-sided navigation menu to
    return to the workspace item list.

3.  Select the **New item** option on the workspace page.
    Select **Pipeline** from the new item flyout menu.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image54.png)

4.  Provide a Pipeline Name as **+++datafactory_pipeline+++** and then
    select **Create**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image55.png)

8.  Select Dataflow

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image56.png)

9.  Select workspace and dataflow

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image57.png)

10. On the **Home** tab of the pipeline editor window, select
    the **Run** button to manually trigger the run of the pipeline.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image58.png)

11. On the **Save and run?** dialog box, select **Save and run** to
    execute these activities. This activity will take around 1-2 min.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image59.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image60.png)

12. On the **Home** tab of the pipeline editor window,
    select **Schedule**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image61.png)

13. Select **+ Add schedule** and configure the schedule as required
    then select **Save** and close the **Schedule** panel.

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image62.png)

14. Select the Daily as schedule and click on Save button

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image63.png)

![A screenshot of a schedule AI-generated content may be
incorrect.](./media/image64.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image65.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image66.png)

## Task 6: Create a “Growth Opportunity” Report

1.  Select PowerBI descktop

2.  In the **Sign in** window, enter the **Username** and click on
    the **Next** button.

3.  Then, enter the password and click on the **Sign in** button.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image67.png)

4.  In Power BI Desktop, select **OneLake catalog** to connect to data
    stored in Microsoft Fabric and start building your report.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image68.png)

5.  From the **OneLake catalog**, select **L400-Eventhouse** and click
    **Connect** to load the real-time data source into Power BI Desktop.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image69.png)

6.  In the **Sign in** window, enter the **Username** and click on
    the **Next** button.

7.  Then, enter the password and click on the **Sign in** button.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image70.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image71.png)

8.  Select all the tables and click **Load** to import the data into
    Power BI Desktop.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image72.png)

9.  In the **Connection settings** dialog, select **Import** as the
    connection mode and click **OK** to proceed.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image73.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image74.png)

10. From the **Insert** tab in Power BI Desktop, select **Text box** to
    add descriptive text or titles to your report canvas.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image75.png)

11. Type in **Growth Opportunity
    Report**. **Highlight** the **text** and increase size to **32**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image76.png)

12. Select the **Slicer** visual, then drag **ProductName** from the
    **products_silver** table into the **Field** well to enable
    product-based filtering in the report.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image77.png)

13. In the **Visualizations** pane, open **Format visual**, go to
    **Visual → Slicer settings**, and set the **Style** to **Dropdown**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image78.png)

14. From the **Insert** tab, select **Text box**, then enter and format
    the text to display the selected product name as **Classic Wear
    Hoodies** and highlight it as the **Top Demand Product** in the
    report.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image79.png)

15. Select the **Pie chart** visual, then drag **referral_platform** to
    the **Legend** and **event_id** to **Values (Count)** from the
    **clickstream** table to visualize event distribution by referral
    platform.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image80.png)

16. From the **Insert** tab, select **Text box**, then enter and format
    the text to display the selected product name as **Pinterest** and
    highlight it as the **Platform that generates most traffic** in the
    report.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image81.png)

17. Select the **Pie chart** visual, then drag **referral_source_type**
    to the **Legend** and **Count of** **event_id** to **Values
    (Count)** from the **clickstream** table to visualize event
    distribution by referral platform.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image82.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image83.png)

18. In the Power BI report, select **Save As** to create a copy or save
    the report with a new name

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image84.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image85.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image86.png)

## Task 7: Setup Data Agent with Real-Time Intelligence

1.  In the **Fabric** home page, select **+New item.** **In the Filter
    by item type search box, enter +++data agent+++ and select the Data
    agent**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image87.png)

2.  Enter **+++l400-agent+++** as the Data agent name and
    select **Create**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image88.png)

3.  In AI-agent page, select **Add a data source**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image89.png)

4.  In the **OneLake catalog** tab, select the **L400_Eventhouse** and
    select **Add**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image90.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image91.png)

5.  Select the tables for which you want the AI skill to have available
    access.

> This lab uses these tables:

- product_copy

- product_silver

- Top products

6.  Enter the following text and click on the **Submit icon** as shown
    in the below image.

> **+++What is the most popular product+++**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image92.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image93.png)

7.  Select **clickstream** and enter the following text and click on
    the **Submit icon** as shown in the below image.

**+++Which website redirected the highest traffic+++**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image94.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image95.png)
