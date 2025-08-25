# Local Runtime Setup for Google Colab
This guide provides instructions for setting up a local Jupyter server to use as a backend for your Google Colab notebooks. Running a local runtime allows you to use your machine's local resources and file system while still benefiting from Colab's user-friendly interface.

## Prerequisites
Python 3.x: Ensure you have Python 3.6 or newer installed on your system.

### Step 1: Create and Activate a Virtual Environment
It's highly recommended to use a virtual environment to manage your project's dependencies and avoid conflicts with your system's global Python packages. This guide uses venv, which is a standard part of Python.

From your terminal, run the following commands to create and activate a new virtual environment named transformers.

```
# Create the virtual environment
python3 -m venv transformers
```

```
# Activate the virtual environment
# On macOS and Linux:
source transformers/bin/activate
# On Windows (Command Prompt):
transformers\Scripts\activate
# On Windows (PowerShell):
transformers\Scripts\Activate.ps1
```

Once activated, your terminal prompt will show (transformers) at the beginning, indicating you are inside the environment.

### Step 2: Install Jupyter
With the virtual environment active, install the jupyter package. This will install the Jupyter Notebook server and its required dependencies directly into your isolated environment.

`pip install jupyter`

### Step 3: Start the Jupyter Server for Colab
Now you can start the Jupyter server, configured to accept connections from the Google Colab frontend. This command opens a port on your local machine and allows Colab to connect to it.

```
jupyter notebook \
    --NotebookApp.allow_origin='https://colab.research.google.com' \
    --port=8888 \
    --NotebookApp.port_retries=0 \
    --NotebookApp.allow_credentials=True
```

After running this command, your terminal will print out a URL. It will look something like this:

`http://localhost:8888/?token=your-long-authentication-token-here`

Copy this entire URL.

### Step 4: Connect from Google Colab
Open your Google Colab notebook in your browser.

In the top-right corner, click on the "Connect" button.

From the dropdown menu, select "Connect to local runtime...".

Paste the URL you copied from your terminal into the dialog box that appears.

Click "Connect".

Your notebook is now connected to your local machine! You can verify the connection by checking if the "Connect" button has changed to a green checkmark with a local tag. All code cells you run will now execute on your computer.

