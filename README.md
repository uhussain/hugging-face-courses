# Running Jupyter Notebooks Locally

This guide provides instructions for running Jupyter notebooks on your local machine with proper resource management and performance optimization.

## Recommended: VSCode with Resource-Limited Kernel

The recommended approach is to run notebooks directly in VSCode with a custom kernel that prevents notebooks from consuming too much CPU/GPU/memory, keeping your system responsive.

### Prerequisites
- VSCode with the Jupyter extension installed
- Python 3.x and ipykernel installed (`pip install ipykernel`)

### Step 1: Create the Resource-Limited Kernel

This custom kernel limits CPU threads, GPU memory usage, and system resources to prevent notebooks from slowing down your machine.

```bash
# Create the kernel directory
mkdir -p ~/Library/Jupyter/kernels/python3-limited

# Create kernel.json configuration
cat > ~/Library/Jupyter/kernels/python3-limited/kernel.json << 'EOF'
{
  "display_name": "Python 3 (Resource Limited)",
  "language": "python",
  "argv": [
    "/usr/bin/python3",
    "-m",
    "ipykernel_launcher",
    "-f",
    "{connection_file}"
  ],
  "metadata": {
    "debugger": true
  },
  "env": {
    "PYTHONUNBUFFERED": "1",
    "OMP_NUM_THREADS": "4",
    "MKL_NUM_THREADS": "4",
    "OPENBLAS_NUM_THREADS": "4",
    "VECLIB_MAXIMUM_THREADS": "4",
    "NUMEXPR_NUM_THREADS": "4",
    "PYTORCH_MPS_HIGH_WATERMARK_RATIO": "0.5",
    "PYTORCH_ENABLE_MPS_FALLBACK": "1",
    "TF_FORCE_GPU_ALLOW_GROWTH": "true",
    "TF_GPU_THREAD_MODE": "gpu_private"
  }
}
EOF
```

### Step 2: Verify Kernel Installation

```bash
jupyter kernelspec list
```

You should see `python3-limited` in the list of available kernels.

### Step 3: Use the Kernel in VSCode

1. Open any `.ipynb` notebook file in VSCode
2. Click the kernel selector in the top-right corner of the notebook
3. Select **"Python 3 (Resource Limited)"** from the list
4. If the kernel doesn't appear, reload VSCode:
   - Press `Cmd+Shift+P` (or `Ctrl+Shift+P` on Windows/Linux)
   - Type "Developer: Reload Window"
   - Press Enter

### Step 4: Working with Colab Notebooks

To use notebooks downloaded from Google Colab:

1. Download the `.ipynb` file from Colab (File → Download → Download .ipynb)
2. Open the file in VSCode
3. Select the "Python 3 (Resource Limited)" kernel
4. Run cells normally with `Shift+Enter`

### Resource Limits Applied

The kernel applies these limits automatically:

- **CPU**: Max 4 threads (prevents hogging all CPU cores)
- **GPU (Apple Silicon)**: Max 50% GPU memory usage
- **Libraries**: Thread limits for NumPy, PyTorch, TensorFlow, etc.

### Customizing Resource Limits

Edit `~/Library/Jupyter/kernels/python3-limited/kernel.json` to adjust limits:

- **CPU threads**: Change `OMP_NUM_THREADS` value (1-8 recommended)
- **GPU memory**: Change `PYTORCH_MPS_HIGH_WATERMARK_RATIO` (0.1-0.9)

After editing, restart the kernel in VSCode for changes to take effect.

---

## Alternative: Google Colab with Local Runtime

If you prefer to use Google Colab's interface while running code on your local machine, you can set up a local Jupyter server as a backend. This allows you to use your machine's local resources and file system while benefiting from Colab's user-friendly interface.

### Prerequisites
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
