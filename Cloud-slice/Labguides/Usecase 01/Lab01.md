## Usecase 01-Build a Real-Time Parcel Delivery Monitoring Solution with Microsoft Fabric

**Scenario: The Fabrikam Frenzy – Real-Time Intelligence Under
Pressure**

Fabrikam, a global lifestyle brand known for its high-quality outerwear
and athleisure, operates a thriving retail and e-commerce business. As
the monsoon season intensifies, an unexpected wave of publicity hits:
several celebrity athletes post viral videos wearing Fabrikam’s latest
apparel. Within minutes, online clickstream traffic surges, add-to-cart
rates spike, and store associates struggle to understand what inventory
is actually available in real time.

At the same moment, a series of operational disruptions emerge across
Fabrikam’s supply chain:

- A tropical depression abruptly changes course, disrupting major
  transit routes and triggering carrier ETA delays.

- A key fabric supplier reports a loom vibration anomaly, with rising
  defect probabilities detected in quality telemetry.

- Several defective apparel units slip through and get shipped to
  customers, causing a rise in returns and service complaints.

- Headquarters receives reports from operations teams that visibility
  into real-time conditions is nearly nonexistent.

- Leadership grows increasingly frustrated that operational issues are
  being solved reactively instead of proactively.

With demand surging and disruptions unfolding minute-by-minute, Fabrikam
faces the risk of stockouts, delayed deliveries, customer
dissatisfaction, and reputational damage. The company urgently needs
**real-time intelligence** to sense demand as it happens, maintain
product availability, and fulfill customer promises despite rapidly
shifting conditions.

**Objective:**
•	Ingest and unify real-time data streams from manufacturing, logistics, e-commerce clickstream, weather, and product systems.
•	Detect operational disruptions such as shipment delays, manufacturing defects, and supply chain risks as they occur.
•	Provide real-time visibility through dashboards that enable proactive decision-making.
•	Monitor customer demand signals to prevent stockouts and improve fulfillment accuracy.
•	Automate alerts and actions using Microsoft Fabric Activator to reduce reaction time to critical events.


# Exercise 1: Environment Setup

## Task 1: Configure Azure Storage Account with Product data

1.  In the Azure portal, search box, type **Storage accounts** and then click on the **Storage accounts**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image4.png)

1.  On the **Storage account** page, click **+Create**.

> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tf1.png)

1.  On **Create a storage account** page, under the **Basics** tab, enter the following details to create a storage account and then click on **Next**

|  |   |
|----|----|
|Subscription|	Select your Azure OpenAI subscription|
|Resource group|	Select **FabricRG** |
|Storage account name	|**storage<inject key="DeploymentID" enableCopy="false" />**|
|Region	|**<inject key="Region" enableCopy="false" />**|
|Performance	|Standard: Recommended for most scenarios (general-purpose v2 account)|
|Redundancy	|Locally-redundant storage (LRS)|


> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tf2.png)

8.  Under the **Advanced** tab, select **"Enable hierarchical namespace"** and click on **Review+create.**

![A screenshot of a computer AI-generated content may be incorrect.](./media/tf3.png)

9.  On the **Review + Create** page, click **Create**.

> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tf4.png)

10. The Azure Storage account is now set up to host data for Azure Data Lake. Click **Go to resource**.

> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tf5.png)

11. On the left-side navigation pane of your Storage Account, select **Data storage** section and then select **Containers**.

> ![](./media/tf6.png)

12. Click **Add Container.**

> ![A screenshot of a computer AI-generated content may beincorrect.](./media/tf7.png)

7.  On the New container pane that appears on the right side, enter the container Name as **shipping-events** and click on **Create** button.

> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tf8.png)

13. Go back to your Storage Account. From the left navigation, select **Access keys** under **Security + networking** group,copy **Connection string** and **Storage account name**, paste them
    in a notepad, and then **Save** the notepad to use the information in the upcoming task.

> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tf9.png)

## Task 2: Create a single database - Azure SQL Database

1.  From the Azure portal home page, click on **Azure portal menu** represented by three horizontal bars on the left side of the Microsoft Azure command bar. Select  **Azure SQL database**

> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg1.png)

1.  Click on **+ Create** and select **SQL Database**

> ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg2.png)

1.  In this pane, under the **Basics** tab, enter the below details to create an Azure SQL Database and then click
    on **Next: Networking**

  | Setting                | Value / Action |
  |------------------------|----------------|
  | Subscription           | Leave the subscription group as default |
  | Resource group         | Select **FabricRG** |
  
  > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg3.png)
  
  | Setting                | Value / Action |
  |------------------------|----------------|
  | Server                  | Select **Create new** |
  | Server name             | **sqlserver<inject key="DeploymentID" enableCopy="false" />**|
  | Location                | **<inject key="Region" enableCopy="false" />** |
  | Authentication Method   | Use SQL authentication |
  | Server admin login      | sqladmin |
  | Password                | password321!|
  | Confirm password        | password321!|
  | Action                  | Click **OK** |
  
  > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg4.png)
  
  > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg5.png)

1.  On the **Networking** tab, select **Public endpoint**, set **Allow Azure services and resources** to **Yes**, enable **Add current client IP address**, and then click **Review + create**.
   
   > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg6.png)

1.  On the **Review + create** page, after reviewing, select **Create**

   > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg7.png)

1.  On **Microsoft.SQLDatabase** window, after the deployment is completed, click on the **Go to resource** button

1.  In SQL database page select **Query editor**.

   > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg8.png)

1.  In the **Query editor (preview)**, enter the SQL server **login** as **sqladmin** and **password** as **password321!**, then click **OK** to connect to the database.

   > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg9.png)

1. To create the **Product** table, paste the following code into the **Query editor** and run it to create the stored procedure.
    ```
    -- Step 1: Create the Products table
    CREATE TABLE Products (
        ProductId VARCHAR(20) PRIMARY KEY,
        ProductName VARCHAR(100),
        SKU VARCHAR(20),
        Brand VARCHAR(50),
        Category VARCHAR(50),
        UnitCost DECIMAL(10, 2)
    );
    ```

     ![A screenshot of a computer AI-generated content may bincorrect.](./media/tg10.png)

11. To enable database for **CDC,** click on **+New Query** and  paste the following code into the **Query editor** and run it to create the stored procedure.

    ```
    -- Enable Database for CDC
    EXEC sys.sp_cdc_enable_db;
    ```

    > ![A screenshot of a computer AI-generated content may be incorrect.](./media/tg11.png)

12. To enable CDC for a table using a gating role option**,** paste the
    following code into the **Query editor** and run it to create the
    stored procedure.
    
      ```
      -- Enable CDC for a table using a gating role option
      EXEC sys.sp_cdc_enable_table
          @source_schema = N'dbo',
          @source_name   = N'Products',
          @role_name     = NULL
      GO
      ```

   ![A screenshot of a computer AI-generated content may be incorrect.](./media/image28.png)

13. Go back to your SQL Database. Copy **Server name** and **SQL
    Database name**, paste them in a notepad, and then **Save** the
    notepad to use the information in the upcoming task.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image29.png)

## Task 3: Create a Fabric workspace

In this task, you create a Fabric workspace. The workspace contains all
the items needed for this lakehouse tutorial, which includes lakehouse,
dataflows, Data Factory pipelines, the notebooks, Power BI datasets, and
reports.

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL:**+++https://app.fabric.microsoft.com/+++** press
    the **Enter** button and sign in with your credentials

    |   |    |
    |-----|----|
    |Username|	+++@lab.CloudPortalCredential(User1).Username+++|
    |Password	|+++@lab.CloudPortalCredential(User1).Password+++|

2.  In the Workspaces pane, click on **+New workspace** tile

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image30.png)

3.  In the **Create a workspace** pane that appears on the right side,
    enter the following details, and click on the **Apply** button.

|  |    |
|-----|-----|
|Name	|**+++RealTimeWorkspace400@lab.LabInstance.Id+++** (can be a unique number)|
|Advanced	|Under License mode, select **Fabric capacity**|
|Default|	storage format Small dataset storage format|


![A screenshot of a computer AI-generated content may be
incorrect.](./media/image31.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image32.png)

   ![A screenshot of a computer AI-generated content may be
incorrect.](./media/image33.png)

## Task 4: Create a lakehouse

1.  In the Workspaces pane, select **+ New item**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image34.png)

2.  In the **Filter by item type** search box,
    enter +++**Lakehouse+++** and select the lakehouse item.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image35.png)

3.  Enter +++**L400_Lakehouse**+++as the lakehouse name and unselect the
    lakehouses schemas. Select **Create**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image36.png)

4.  When provisioning is complete, the lakehouse explorer page is shown.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image37.png)

## Task 5: Create an Eventhouse

1.  From the **Tutorial**  page, select **Digital twin builder** in the
    left-sided navigation menu to return to the workspace item list.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image38.png)

2.  In the Workspaces pane, select **+ New item**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image39.png)

3.  In the **Filter by item type** search box,
    enter +++**Eventhouse+++** and select the Eventhouse item.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image40.png)

4.  Enter **+++L400-Eventhouse+++** as the eventhouse name. A KQL
    database is created simultaneously with the same name and
    select **Create**. 

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image41.png)

5.  When provisioning is complete, the eventhouse **System
    overview** page is shown.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image42.png)

# Exercise 2: Ingest Manufacturing data into Eventhouse using Eventstream

## Task 1: Set Up an Eventstream and Create Custom Endpoints

In this task, you will create an Event Stream and add the Manufacturing
data as the source.

1.  From the **KQL databases** pane in the eventhouse, select
    the **RealTimeWorkspace** .

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image43.png)

2.  In the Workspaces pane, select **+ New item**. In the **Filter by
    item type** search box, enter +++**Eventstream+++** and select the
    **Eventstream** item

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image44.png)

3.  Enter **+++L400-Eventstream+++** as the eventstream name and
    select **Create**. 

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image45.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image46.png)

4.  On the Screen **Design a flow to ingest, transform, and route
    streaming events** click on **Use custom Endpoint**. This will
    create an event hub connected to the Eventstream.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image47.png)

5.  Insert +++**CustomEndpoint-L400**+++ as the source name and the
    click on **Add**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image48.png)

6.  Click on the **Publish** button.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image49.png)

7.  On the **Eventstream** pane, select the **keys** under
    the **Details**, select **SAS key Authentication ,** copy
    the **Event hub name**, **connection strings-primarykey** and paste
    them on a notepad, as you need them in the upcoming task

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image50.png)

8.  Now, click on **RealTimeWorkspaceXXX** on the left-sided navigation
    pane.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image51.png)

## Task 2: Import Manufacturing data Notebook

1.  On the **RealTimeWorkspace** page, from the menu bar, navigate and
    click on **-\>|Import** button, then select **Notebook** and
    select **From this computer** as shown in the below image.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image52.png)

2.  Select **Upload** from the **Import status** pane that appears on
    the right side of the screen.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image53.png)

3.  Navigate and select **Manufacturing Simulator** notebooks
    from **C:\LabFiles**and click on the **Open** button.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image54.png)

4.  You will see a notification stating **Imported successfully.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image55.png)

5.  Then, select the **Manufacturing Simulator**  notebook.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image56.png)

6.  In the **Explorer** pane, click **Add data items**, and then select
    **Existing data sources** to connect to an existing data source.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image57.png)

7.  In the **OneLake catalog**, select **L400_Lakehouse** and click
    **Connect** to link it as a data source.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image58.png)

8.  To start the notebook, run the 1^(st) cell.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image59.png)

9.  In the **2^(nd)** cell paste the **connection string of your custom
    app source and EventHubName** (the value that you have saved in your
    notepad in the (Exercise 2\>**Task 1\>Step 7)**, select
    the **Run** icon that appears on the left side of the cell

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image60.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image61.png)

10. Select the **third** cell to **generate the site location**, and
    then **run** the cell.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image62.png)

11. Select the 4^(th) cell to generate the assets, and then run the
    cell.

> ![A screen shot of a computer code AI-generated content may be
> incorrect.](./media/image63.png)

12. Select the 5^(th) cell to generate the Operators, and then run the
    cell.

![A screenshot of a computer program AI-generated content may be
incorrect.](./media/image64.png)

13. Select the 6^(th) cell to generate the products, and then run the
    cell.

![A screenshot of a computer program AI-generated content may be
incorrect.](./media/image64.png)

14. Select the 7^(th) cell to generate the event functions, and then run
    the cell.

![A screenshot of a computer program AI-generated content may be
incorrect.](./media/image65.png)

15. Select the 8^(th) cell and then run the cell.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image66.png)

16. Select the **9^(th)** cell to Streaming simulations, and then run
    the cell.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image66.png)

17. Select the 10^(th) cell and then run the cell.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image67.png)

**Note**: Skip running the 11th cell, which is the final cell in the
notebook.

18. Now, click on **L400_Eventstream** on the left navigation pane.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image68.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image69.png)

19. In the event stream authoring canvas, select the **Edit**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image70.png)

20. Click on the node **Transform events or add Destination** and
    select **SQL Code** from the menu.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image71.png)

21. Select **SqlCode** node and click on **Action**

> ![A screenshot of a chat AI-generated content may be
> incorrect.](./media/image72.png)

22. Click on **Edit query**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image73.png)

23. Use the following SQL transformation on the DefectProbability column
    to mark values greater than 0.1 as *Anomaly*. Copy the code and
    select **Test query**.

--Init Query
```
SELECT * ,CASE WHEN DefectProbability>0.1 THEN'1' ELSE '0' END AS Anamoly FROM [L400Eventstream-stream]
```
![A screenshot of a computer AI-generated content may be
incorrect.](./media/image74.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image75.png)

24. Click **Save** to apply the changes.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image76.png)

25. Click **Save** to apply the changes.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image77.png)

26. To add a destination, open the **Add destination** dropdown and
    select **Eventhouse** from the context menu.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image78.png)

27. Provide the following values in the pane **Eventhouse**. Click the
    button **Save** after you entered all the values.

| Field                          | Value |
|--------------------------------|-------|
| Event processing before ingestion | Ensure that this option is selected. |
| Workspace                      | Select **RealTimeWorkspaceXXX**. If you attend the Precon at dataMinds Connectrope, select the Workspace name that was provided to you. |
| Eventhouse                     | Select the Eventhouse **L400_Eventhouse** |
| KQL Database                   | Select the KQL Database **L400_Eventhouse** |
| Destination table              | Click **Create new**, enter **+++manufacturing+++** as the table name, and click **Done** |
| Input data format              | Ensure that the **JSON** option is selected |


> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image79.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image80.png)

15. Connect the output of the node **SqlCode** to the input of the
    node **Eventhouse**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image81.png)

28. Click on the button **Publish** that is located in the toolbar at
    the top of the screen.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image82.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image83.png)

29. Click on the icon **Manufacturing Simulator notebook** in the top
    toolbar

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image84.png)

30. Right-click **L400_Lakehouse** and click **Set as default
    lakehouse** from the menu

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image85.png)

31. Click the **Run all** button to execute all the cells

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image86.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image87.png)

## Task 3: creating delta tables in the lakehouse 

1.  Select on the icon **RealTimeWorkspaceXX** in the left toolbar and
    click on **L400_Lakehouse**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image88.png)

2.  To verify that the files have been successfully loaded, click the
    **Files** folder in the **Explorer** pane. You should see the files
    listed in the right-hand pane of the window.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image89.png)

3.  Next we have to create delta tables in our Lakehouse from the files
    we uploaded. To do this access the context menu by clicking on the
    three dots (**...**). Select **Load to tables** from the context
    menu.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image90.png)

4.  Retain all default values and click on the button **Load**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image91.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image92.png)

5.  These steps must be executed for the **assets.csv** file as well as
    for the **operators.csv, product.csv**, and **sites.csv** files.![A
    screenshot of a computer AI-generated content may be
    incorrect.](./media/image93.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image94.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image95.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image96.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image97.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image98.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image99.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image100.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image101.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image102.png)

6.  To verify the data in the Eventhouse, select the
    **L400_Eventstream** icon from the top toolbar.![A screenshot of a
    computer AI-generated content may be
    incorrect.](./media/image103.png)

7.  Select the **Eventhouse** and verify the data.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image104.png)

## Task 4: Accessing Eventhouse data from the lakehouse 

1.  Click on the icon **RealTimeWorkspaceXXX** in the left toolbar.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image105.png)

2.  Select the **L400_Eventhouse**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image106.png)

3.  From the **KQL databases** section, select **L400_Eventhouse**![A
    screenshot of a computer AI-generated content may be
    incorrect.](./media/image107.png)

4.  Click on the button **+New** in the menu bar at the top.
    Choose **OneLake shortcut** from the dropdown menu.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image108.png)

5.  Select **Microsoft OneLake.**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image109.png)

6.  Select the **L400_Lakehouse** in the Window **Select a data source
    type** and click on the button **Next**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image110.png)

7.  Expand the folder **Tables** under **L400_Lakehouse** in the
    window **New shortcut** and check all tables. Click on **Next**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image111.png)

8.  Click on the button **Create**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image112.png)

9.  Click the **Close** button

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image113.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image114.png)

# Exercise 3: Ingest Product Data from SQL Database into Eventhouse

## Task 1: Connect a SQL Database to an Eventstream

1.  Click on the icon **Real-Time** in the left toolbar.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image115.png)

2.  Select **Data sources** from the left navigation pane, choose the
    **Database CDC** tab, and then click **Connect** on **Azure SQL DB
    (CDC)**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image116.png)

3.  Click on **New connection**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image117.png)

4.  In Connection settings tab enter the below detail and click on
    Connect button

| Field    | Value |
|----------|-------|
| Server   | SQL server URL saved in **Exercise 1 → Task 2 → Step 13** |
| Database | Enter your SQL database |
| Username | sqladmin |
| Password | Password321! |


> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image118.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image119.png)

5.  Enter the **Eventstream** name as +++**sql_eventstream+++**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image120.png)

6.  In the **Connect data source** tab, open the **Tables** dropdown and
    select **Enter table name**.![A screenshot of a computer
    AI-generated content may be incorrect.](./media/image121.png)

7.  Enter the table name as +++**dbo.Products**+++

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image122.png)

8.  Click on **Connect** button

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image123.png)

9.  Click on **Finish** button

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image124.png)

10. Select **RealTimeWorkspaceXXX** in the left-sided navigation menu
    and click on **sql_eventstream**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image125.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image126.png)

11. In the event stream authoring canvas, select the **Edit**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image127.png)

12. Click on the node **Transform events or add Destination** and
    select **Eventhouse** from the menu.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image128.png)

13. Provide the following values in the pane **Eventhouse**. Click the
    button **Save** after you entered all the values.

| Field                          | Value |
|--------------------------------|-------|
| Event processing before ingestion | Ensure that this option is selected. |
| Workspace                      | Select **RealTimeWorkspaceXXX**. If you attend the Precon at dataMinds Connectrope, select the workspace name that was provided to you. |
| Eventhouse                     | Select the Eventhouse **L400_Eventhouse** |
| KQL Database                   | Select the KQL Database **L400_Eventhouse** |
| Destination table              | Click **Create new**, enter **+++products+++** as the table name, and click **Done** |
| Input data format              | Ensure that the **JSON** option is selected |


![A screenshot of a computer AI-generated content may be
incorrect.](./media/image129.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image130.png)

14. From the menu ribbon, select **Publish**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image131.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image132.png)

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image133.png)

15. Return to the **SQL Database Query Editor**, select **+ New query**,
    paste the provided product data SQL script into the query editor,
    and then execute the query to insert the data.
```
-- Step 2: Insert the product data
INSERT INTO dbo.Products (ProductId, ProductName, SKU, Brand, Category, UnitCost) VALUES
('PROD4000', 'Cyberpunk Hat', 'SKU4000', 'AirRun', 'Altars', 133.79),
('PROD4001', 'CloudShell Jacket', 'SKU4001', 'AirRun', 'Kids', 272.67),
('PROD4002', 'Oldschool Cardigan', 'SKU4002', 'UrbanStep', 'GenZ Pros', 295.88),
('PROD4003', 'TropicFeel Tshirt', 'SKU4003', 'UrbanStep', 'Colours', 138.43),
('PROD4004', 'ClassicWear Hoodie', 'SKU4004', 'ClassicWear', 'Kids', 63.33),
('PROD4005', 'TropicFeel Tshirt', 'SKU4005', 'AirRun', 'GenZ Pros', 182.16),
('PROD4006', 'UrbanStep Shoes', 'SKU4006', 'ZAVA', 'Colours', 36.00),
('PROD4007', 'UrbanStep Shoes', 'SKU4007', 'UrbanStep', 'Altars', 35.92),
('PROD4008', 'UrbanStep Shoes', 'SKU4008', 'ZAVA', 'Altars', 39.18),
('PROD4009', 'Cyberpunk Hat', 'SKU4009', 'AirRun', 'Kids', 53.56),
('PROD4010', 'UrbanStep Shoes', 'SKU4010', 'AirRun', 'GenZ Pros', 193.42),
('PROD4011', 'CloudShell Jacket', 'SKU4011', 'ClassicWear', 'Colours', 281.71),
('PROD4012', 'Oldschool Cardigan', 'SKU4012', 'StreetFlex', 'Altars', 94.36),
('PROD4013', 'Oldschool Cardigan', 'SKU4013', 'StreetFlex', 'Kids', 108.52),
('PROD4014', 'Cyberpunk Hat', 'SKU4014', 'ZAVA', 'Kids', 193.91),
('PROD4015', 'UrbanStep Shoes', 'SKU4015', 'ZAVA', 'GenZ Pros', 170.53),
('PROD4016', 'UrbanStep Shoes', 'SKU4016', 'StreetFlex', 'Altars', 281.30),
('PROD4017', 'Cyberpunk Hat', 'SKU4017', 'AirRun', 'Colours', 99.79),
('PROD4018', 'CloudShell Jacket', 'SKU4018', 'ClassicWear', 'Colours', 191.26),
('PROD4019', 'ClassicWear Hoodie', 'SKU4019', 'ClassicWear', 'GenZ Pros', 206.99);
```
![A screenshot of a computer AI-generated content may be
incorrect.](./media/image134.png)

1.  Return to the **sql_eventstream**, click **Refresh**, and verify
    that the data has been updated successfully.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image135.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image136.png)

2.  Click on the icon **L400_Eventhouse** in the top toolbar.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image137.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image138.png)

# Exercise 4: Ingest Shipping Data from Azure Storage Container into Eventhouse

## Task 1: Create a Workspace Identity 

1.  Click on the icon **RealTimeWorkspaceXXX** in the left toolbar.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image139.png)

2.  From the menu ribbon, select **Workspace settings**.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image140.png)

3.  In the **Workspace identity** settings pane and select **+ Workspace
    identity**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image141.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image142.png)

## Task 2: Assign Storage Blob Data Contributor Role to Fabric Workspace

1.  Open your browser, navigate to the address bar, and type or paste
    the following URL:+++<https://portal.azure.com/+++>, then press
    the **Enter** button.

2.  Select ResourceGroup1.

3.  In the **Resource Group Overview**, select the storage account named
    **l400storageXXX**.
    ![A screenshot of a computer AI-generated content
    may be incorrect.](./media/image143.png)

5.  From the left menu, click on the **Access control(IAM**). On the
    Access control(IAM) page, Click **+Add** and select **Add role
    assignments**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image144.png)

5.  Type the **+++Storage Blob Data Contributor+++** in the search box and
    select it. Click **Next**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image145.png)

6.  In the **Add role assignment** tab, select Assign access to User
    group or service principal. Under Members, click **+Select members**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image146.png)

7.  On the Select members tab , search your Fabric workspace and
    click **Select.**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image147.png)

8.  In the **Add role assignment** page, Click **Review + Assign**, you
    will get a notification once the role assignment is complete.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image148.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image149.png)

9.  You will see a notification – added as **Stronge Blob Data
    Contributor**  for Azure Pass-Sponsorship.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image150.png)

## Task 3: Import Shipping Data Notebook

1.  Return to the Fabric workspace .

2.  On the **RealTimeWorkspace** page, from the menu bar, navigate and
    click on **-\>|Import** button, then select **Notebook** and
    select **From this computer** as shown in the below image.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image151.png)

3.  Select **Upload** from the **Import status** pane that appears on
    the right side of the screen.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image152.png)

4.  Navigate and select **Shipping Simulator** notebooks
    from **C:\LabFiles**and click on the **Open** button.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image153.png)

5.  Then, select the **Shipping Simulator**  notebook.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image154.png)

6.  Select the cell ,paste the **BLOB_CONNECTION_STRING and
    CONTAINER_NAME** (the value that you have saved in your notepad in
    the Exercise 1\> **Task 1\>Step 13)**, select the **Run** icon that
    appears on the left side of the cell.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image155.png)

7.  The last code cell its still running continue the next steps.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image156.png)

8.  Now, click on **L400_Eventhouse** on the top navigation pane.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image157.png)

9.  Click on the button **Get data** in the menu bar at the top.
    Choose **Azure Storage** from the dropdown menu.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image158.png)

10. Select the **+ New table**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image159.png)

11. Enter the new table name as +++**shipping+++**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image160.png)

12. Configure the Azure Blob Storage source by selecting **Connect to a
    storage account**, choosing the appropriate **Subscription**, **Blob
    storage account**, and **Container (shipping-events)**, then create
    a **New connection** to complete the setup.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image161.png)

13. Click on **Save** button

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image162.png)

14. Select **Next**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image163.png)

15. Click on **Finish** button

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image164.png)

16. The **Get data** process will take approximately **10–13 minutes**
    to complete.![A screenshot of a computer AI-generated content may be
    incorrect.](./media/image165.png)

17. Click on **Close** button

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image166.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image167.png)

# Exercise 5: Share Weather Data with Fabrikam Distributors and Configure Hourly Alerts for the US Region

## Task 1: Create derived stream

1.  Click on the icon **Real-Time** in the left toolbar.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image168.png)

2.  In the **Data sources** pane, search for **+++Real-time weather+++**, and
    then select **Connect** to start ingesting live weather data into
    the Eventstream.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image169.png)

3.  Select **United States (US)** as the location for ingesting
    real-time weather data, verify the **Eventstream name** as
    **+++weather_eventstream+++**, and then click **Next** to continue.

> ![A screenshot of a computer screen AI-generated content may be
> incorrect.](./media/image170.png)

4.  Click on **Connect** button

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image171.png)

5.  Click on **Finish** button

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image172.png)

6.  Click on the node **Transform events or add Destination** and
    select **Activator** from the menu.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image173.png)

7.  Select the **RealTimeWorkspace400L** workspace, create a new
    **Activator** by entering +++**temperature+++** as the activator
    name, keep the input data format as **JSON**, and then click
    **Done** to save the configuration.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image174.png)

8.  Click on **Save**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image175.png)

9.  Click on the button **Publish** that is located in the toolbar at
    the top of the screen.

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image176.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image177.png)

10. Click on the icon **RealTimeWorkspaceXXX** in the left toolbar and
    select **temperature**

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image178.png)

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image179.png)

11. Under the **Events** tab, select **New rule** to create a new event
    rule.![A screenshot of a computer AI-generated content may be
    incorrect.](./media/image180.png)

12. Enter the valid email and click on Start button

![A screenshot of a computer AI-generated content may be
incorrect.](./media/image181.png)

Summary:
This use case focuses on helping Fabrikam, a global retail and e-commerce brand, overcome real-time operational challenges during periods of extreme demand and disruption. A sudden surge in customer traffic, combined with weather-related transit delays and manufacturing quality issues, exposes the lack of real-time visibility across Fabrikam’s supply chain and fulfillment operations.
Using Microsoft Fabric Real-Time Intelligence, the solution integrates high-velocity data from multiple sources—including manufacturing telemetry, shipment events, e-commerce clickstream, weather data, and product information—into a single operational view. Eventstream, Eventhouse, KQL analytics, real-time dashboards, and Activator alerts work together to detect risks early, visualize live conditions, and trigger automated responses.
The outcome is a proactive, real-time operational command center that enables Fabrikam to protect customer experience, reduce delays, prevent defective shipments, and maintain business continuity under rapidly changing conditions






