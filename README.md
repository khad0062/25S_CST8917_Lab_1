# CST8917 Lab 1: Building Azure Function Apps with Output Bindings

## Task 1: Connect Azure Functions to Azure Storage using Visual Studio Codee
## 1. Create Azure Resources
### Step 1: Sign in to Azure Portal
- Go to: [https://portal.azure.com](https://portal.azure.com)
- Log in with your Azure account.

### Step 2: Create a Storage Account
1. In the search bar, type **"Storage accounts"** and click on it.
2. Click **+ Create**.
3. **Basics tab**:
   - **Subscription**: Choose your subscription.
   - **Resource Group**: Create a new one or select an existing one.
   - **Storage account name**: Must be globally unique (e.g., `myfunctionappstorage123`)
   - **Region**: Choose your nearest region (e.g., *Canada Central*).
   - **Performance**: Standard
   - **Redundancy**: Locally-redundant storage (LRS) is fine for most cases.
4. Click **Review + Create**, then **Create**.

### Step 3: Create a Function App
1. In the search bar, type **"Function App"** and click on it.
2. Click **+ Create**.
3. **Basics tab**:
   - **Hosting Plan **: Choose Flex Consumption hosting plan
   - **Subscription**: Choose your subscription.
   - **Resource Group**: Use the same one as the storage account.
   - **Function App name**: Unique name (e.g., `myfunctionapp123`)
   - **Region**: Same as the storage account.
   - **Runtime Stack**: Choose Python or based on your need
    - Runtime Stack Version : 3.11
5. **Hosting tab**:
   - **Storage Account**: Select the one you created earlier.
6. **Monitoring tab**:
   - **Application Insights**: Enable (optional, for logging).
7. Click **Review + Create**, then **Create**.

### Step 4: Wait for Deployment
- After deployment completes, click **Go to Resource** to open your Function App.

### Step 5: Create a Function Inside Your Function App
1. In the **Function App** page, go to **Functions** in the left-hand menu.
2. Click **+ Add**.
3. Choose a **Development environment** (e.g., VS Code or Azure portal).
4. Select a **template**, such as **HTTP trigger**.
5. Configure the **trigger settings** and click **Create**.

## 2. Create a Local Azure Functions Project in Python (Visual Studio Code)
Follow the steps below to create a local Azure Functions project using **Python (Programming Model V2)** in **Visual Studio Code**.
### Step 1: Open Command Palette
- Press `F1` or `Ctrl+Shift+P` in Visual Studio Code.
- Run the command: 

### Step 2: Select Project Folder
- Choose the directory location for your project workspace.
- **Important:** Select a new or empty folder. 

### Step 3: Provide the Following Information When Prompted
| Prompt | Your Selection |
|--------|----------------|
| **Select a language** | `Python (Programming Model V2)` |
| **Select a Python interpreter to create a virtual environment** | Choose your preferred Python interpreter (or provide the path manually) |
| **Select a template for your project's first function** | `HTTP trigger` |
| **Name of the function you want to create** | `HttpExample` |
| **Authorization level** | `ANONYMOUS` *(lets anyone call your function endpoint)* |
| **Select how you would like to open your project** | `Open in current window` |

### Step 4: Project is Created
Visual Studio Code will:
- Scaffold a new Azure Functions project.
- Add your first function: `HttpExample`.
- Create a `function_app.py` file that includes your HTTP-triggered function.
> You can now view the project files in the **Explorer** pane of VS Code.

### Step 5: Download Remote App Settings
To connect your function to the Azure Storage account during local execution:
1. Press `F1` in **Visual Studio Code**
2. Run: `Azure Functions: Download Remote Settings...`
3. Select your function app from the Azure list
4. Select **"Yes to all"** to overwrite the existing settings
5. Open the `local.settings.json` file
6. Copy the value for `AzureWebJobsStorage`

### Step 6: Verify Extension Bundles
Ensure the `host.json` file includes the correct extension bundle configuration:
```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[3.*, 4.0.0)"
  }
}
```
This enables support for Storage Queue output bindings without installing them manually.

### Step 6: Add Queue Output Binding to the Function
Update your `function_app.py` file:
```python
import azure.functions as func
import logging

app = func.FunctionApp(http_auth_level=func.AuthLevel.ANONYMOUS)

@app.route(route="HttpExample")
@app.queue_output(arg_name="msg", queue_name="outqueue", connection="AzureWebJobsStorage")
def HttpExample(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
    else:
        return func.HttpResponse(
            "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.",
            status_code=200
        )
```
This allows your function to send the `name` value as a message to the `outqueue`.

### Step 7. Run the Function Locally
Open a terminal in your project directory and run the following:
```bash
# Create and activate virtual environment (if not done already)
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install required Python packages
pip install -r requirements.txt

# Start the Azure Function app locally
press F5 to start the function app project and Core Tools.

# With the Core Tools running, go to the Azure: Functions area. 
Under Functions, expand Local Project > Functions. Right-click (Ctrl-click) the HttpExample function and select Execute Function Now....
This sends a message ("Azure") to the queue `outqueue`.
```

### Step 8: View the Queue Message (Using Azure Storage Explorer)
- Download and install Azure Storage Explorer
- Launch it and click **Connect**
- Choose **Add an Azure account**, then sign in
- Locate your storage account, then expand **Queues**
- Open `outqueue`
- You will see the message written by your function (e.g., "Azure")

### Step 9: Deploy Updated Function to Azure
To publish your updated function code to Azure:
- Press `F1` in Visual Studio Code
- Run: `Azure Functions: Deploy to Function App...`
- Select the same Function App created earlier
- Click **Deploy** when prompted to overwrite files

### Step 10: Test Live Function in Azure
- Press `F1` and select `Azure Functions: Execute Function Now...`
- Choose the `HttpExample` function
- Enter this JSON as the request body:

```json
{ "name": "Azure" }
```
- Confirm the function executes successfully.
- Recheck the queue `outqueue` in Storage Explorer to confirm the message was added
You have successfully tested both the local and deployed versions of your function with a working queue output binding!
---
## Task 2: Connect Azure Functions to Azure SQL Database using Visual Studio Code

### Step 1: Create Azure SQL Database

Follow the Azure SQL quickstart to create a serverless Azure SQL Database.
#### Input Requirements:
- **Resource group**: Same as your function app
- **Database name**: `mySampleDatabase`
- **Server name**: Globally unique name
- **Authentication**: SQL Server authentication
- **Login**: `azureuser`
- **Password**: Secure password
- **Allow Azure services**: Yes

### Create `ToDo` Table
```sql
CREATE TABLE dbo.ToDo (
    [Id] UNIQUEIDENTIFIER PRIMARY KEY,
    [order] INT NULL,
    [title] NVARCHAR(200) NOT NULL,
    [url] NVARCHAR(200) NOT NULL,
    [completed] BIT NOT NULL
);
```
### Step 2: Update Function App Settings
1. Copy the **ADO.NET** connection string from Azure Portal.
2. Replace `<your_password>` with your actual SQL password.
3. In VS Code:
   - `Ctrl+Shift+P` → `Azure Functions: Add New Setting`
   - **Name**: `SqlConnectionString`
   - **Value**: (your updated connection string)
4. Then:
   - `Ctrl+Shift+P` → `Azure Functions: Download Remote Settings`
   - Confirm overwrite

### Step 3: Register Binding Extensions
Update your `host.json` to include:
```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[4.*, 5.0.0)"
  }
}
```
Enables SQL binding without manual package installation.

### Step 4: Add SQL Output Binding to Function
Update `function_app.py`:
```python
import azure.functions as func
import logging
from azure.functions.decorators.core import DataType
import uuid

app = func.FunctionApp()

@app.function_name(name="HttpTrigger1")
@app.route(route="hello", auth_level=func.AuthLevel.ANONYMOUS)
@app.generic_output_binding(
    arg_name="toDoItems",
    type="sql",
    CommandText="dbo.ToDo",
    ConnectionStringSetting="SqlConnectionString",
    data_type=DataType.STRING
)
def test_function(req: func.HttpRequest, toDoItems: func.Out[func.SqlRow]) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')
    name = req.get_json().get('name')
    if name:
        toDoItems.set(func.SqlRow({
            "Id": str(uuid.uuid4()),
            "title": name,
            "completed": False,
            "url": ""
        }))
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### Step 5: Run the Function Locally
Open a terminal in your project directory and run the following:
```bash
# Create and activate virtual environment (if not done already)
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install required Python packages
pip install -r requirements.txt

# Start the Azure Function app locally
press F5 to start the function app project and Core Tools.

# With the Core Tools running, go to the Azure: Functions area. 
Under Functions, expand Local Project > Functions. Right-click (Ctrl-click) the HttpExample function and select Execute Function Now....
This sends a message ("Azure") to the queue `outqueue`
```

###  Step 6: Verify SQL Insertion
In Azure SQL Portal > Query Editor:
```sql
SELECT TOP 1000 * FROM dbo.ToDo;
```
You should see the inserted data.
### Step 7: Redeploy Function to Azure
1. `F1` → `Azure Functions: Deploy to Function App...`
2. Select your function app
3. Confirm file overwrite
4. Use `Execute Function Now...` to test live in Azure

 Confirm data in Azure SQL Database.

---




