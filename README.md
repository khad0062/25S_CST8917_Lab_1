# Create Function App and Storage Account in Azure

## Step 1: Sign in to Azure Portal

- Go to: [https://portal.azure.com](https://portal.azure.com)
- Log in with your Azure account.

---

## Step 2: Create a Storage Account

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

---

## Step 3: Create a Function App

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

---

## Step 4: Wait for Deployment

- After deployment completes, click **Go to Resource** to open your Function App.

## Step 5 (Optional): Create a Function Inside Your Function App

1. In the **Function App** page, go to **Functions** in the left-hand menu.
2. Click **+ Add**.
3. Choose a **Development environment** (e.g., VS Code or Azure portal).
4. Select a **template**, such as **HTTP trigger**.
5. Configure the **trigger settings** and click **Create**.

## # Create a Local Azure Functions Project in Python (Visual Studio Code)

Follow the steps below to create a local Azure Functions project using **Python (Programming Model V2)** in **Visual Studio Code**.

---

# Create a Local Azure Functions Project in Python (Visual Studio Code)

Follow the steps below to create a local Azure Functions project using **Python (Programming Model V2)** in **Visual Studio Code**.

---

## Step 1: Open Command Palette

- Press `F1` or `Ctrl+Shift+P` in Visual Studio Code.
- Run the command:  

---

## Step 2: Select Project Folder

- Choose the directory location for your project workspace.
- **Important:** Select a new or empty folder.  
---

## Step 3: Provide the Following Information When Prompted

| Prompt | Your Selection |
|--------|----------------|
| **Select a language** | `Python (Programming Model V2)` |
| **Select a Python interpreter to create a virtual environment** | Choose your preferred Python interpreter (or provide the path manually) |
| **Select a template for your project's first function** | `HTTP trigger` |
| **Name of the function you want to create** | `HttpExample` |
| **Authorization level** | `ANONYMOUS` *(lets anyone call your function endpoint)* |
| **Select how you would like to open your project** | `Open in current window` |

---

## Step 4: Project is Created

Visual Studio Code will:

- Scaffold a new Azure Functions project.
- Add your first function: `HttpExample`.
- Create a `function_app.py` file that includes your HTTP-triggered function.

> You can now view the project files in the **Explorer** pane of VS Code.

---

## Next Steps

You’re now ready to:
- Edit your function logic in `function_app.py`
- Add bindings (input/output)
- Run and debug your function locally
- Deploy the function to Azure



