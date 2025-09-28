# Multimodal Medical Chatbot Interface

![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)![License](https://img.shields.io/badge/License-MIT-green.svg)

A user-friendly Gradio web interface for interacting with locally hosted, OpenAI-compatible medical Gemma large language models from Google. This application supports both text-to-text and image-to-text (multimodal) conversations, making it a powerful tool for medical AI research and experimentation.



## Features

-   **Multimodal Chat:** Seamlessly switch between text-only questions and image-based analysis.
-   **Dynamic System Prompts:** Automatically uses a specialized `IMAGE_PROMPT` for image analysis and a configurable `TEXT_PROMPT` for general medical questions.
-   **Model Selection:** Easily switch between different models loaded on your backend via a dropdown menu.
-   **Real-time Streaming:** Responses are streamed token-by-token for an interactive experience.
-   **Parameter Control:** Adjust key inference parameters like `Temperature` and `Max New Tokens` directly in the UI.
-   **Robust Session Management:**
    -   Chat history is maintained for conversational context.
    -   Image uploads are intelligently disabled mid-conversation to ensure a clean workflow.
    -   Includes "Stop" and "New Chat" controls.
-   **Download Conversation:** Save the model's last complete response as a Markdown file.
-   **Automatic Cleanup:** Temporary files created for downloads are automatically cleaned up when the application exits.

## System & Hardware Requirements

This application is a **frontend client**. It requires a separate, powerful backend server to host and run the language models. The setup described here is tailored for a high-performance local machine.

### Hardware

This setup is designed to run on a local machine with significant GPU resources:
-   **GPU:** 4x NVIDIA RTX 3090 (24 GB VRAM each)
-   **Total VRAM:** 96 GB

### Backend: vLLM Server

The Gradio interface connects to a local server powered by [vLLM](https://github.com/vllm-project/vllm), which provides an OpenAI-compatible API endpoint.

-   **Model:** The primary model for this setup is `google/medgemma-27b-it`.
-   **Model Source:** [huggingface.co/google/medgemma-27b-it](https://huggingface.co/google/medgemma-27b-it)

To serve this model across all four GPUs, you will run the following command in your terminal:

```bash
# Ensure you have uv or another package manager to run vLLM
uv run vllm serve google/medgemma-27b-it --tensor-parallel-size 4 --async-scheduling
```
-   `--tensor-parallel-size 4`: This critical flag splits the model's weights across the 4 GPUs, allowing you to run a model that wouldn't fit on a single card.
-   `--async-scheduling`: Enables asynchronous scheduling to improve throughput.

### Frontend: Python Dependencies

The `medgemma-gradio.py` script requires the following Python libraries:

-   `gradio`
-   `openai`
-   `pillow`

## Installation and Setup

Follow these steps to get the complete application running.

### 1. Set Up the Environment

First, clone this repository and create a Python virtual environment.

```bash
git clone <your-repository-url>
cd <your-repository-name>
python -m venv venv
source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
```

### 2. Install Dependencies

Install the required Python packages for both the vLLM server and the Gradio client.

```bash
# For the vLLM server
pip install vllm uv

# For the Gradio frontend
pip install gradio openai pillow
```

### 3. Launch the vLLM Backend Server

Open a new terminal window, activate your virtual environment, and start the vLLM server with the command specified above.

```bash
source venv/bin/activate
uv run vllm serve google/medgemma-27b-it --tensor-parallel-size 4 --async-scheduling
```

Leave this terminal running. It will handle all the model inference requests. By default, it will be available at `http://localhost:8000`.

### 4. Launch the Gradio Frontend

Open a second terminal window, navigate to the repository directory, and run the Python script.

```bash
source venv/bin/activate
python medgemma-gradio.py
```

## Running the Application

You can launch the application in several ways depending on your needs.

### To run locally on your machine:
Use the following line in the script:
```python
demo.launch()
```

### To serve to other devices on your local network (LAN):
Modify the last line to include your machine's local IP address.
```python
# Replace "192.168.0.xx" with your actual LAN IP address
demo.launch(server_name="192.168.0.xx", server_port=7860)
```

### To share publicly over the internet:
Set the `share` parameter to `True`. Gradio will generate a temporary public URL for you.
```python
demo.launch(share=True)
```
For more details, see the official Gradio guide on [Sharing Your App](https://www.gradio.app/guides/sharing-your-app).

## How to Use

1.  **Select a Model:** Choose your desired model from the "Select Model" dropdown.
2.  **Ask a Question:**
    -   **For a text-based question:** Simply type your message in the textbox and press "Send".
    -   **For an image analysis:** At the **start of a new chat**, click the "Upload Image" box, select your image, and then type an accompanying prompt (e.g., "What do you see in this chest x-ray?").
3.  **Adjust Parameters:** Use the sliders to control the `Temperature` and `Max New Tokens` for the model's response.
4.  **Manage the Conversation:**
    -   Click **"⏹️ Stop"** to interrupt a response while it is being generated.
    -   Click **"🗑️ New Chat"** to clear the conversation history and start fresh. This will also re-enable the image upload component.
    -   Click **"⬇️ Download Last Response"** to save the model's output.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
