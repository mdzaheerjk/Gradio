# 🚀 Gradio — Complete Notes: Beginner to Production
> **One document to rule them all.** Everything you need to build, customize, and deploy AI/ML apps with Gradio.

---

## 📑 Table of Contents

1. [What is Gradio?](#1-what-is-gradio)
2. [Installation & Setup](#2-installation--setup)
3. [Core Concepts](#3-core-concepts)
4. [Your First Gradio App](#4-your-first-gradio-app)
5. [Interface Class — Deep Dive](#5-interface-class--deep-dive)
6. [Components Reference](#6-components-reference)
7. [Blocks API — Full Control](#7-blocks-api--full-control)
8. [Layouts](#8-layouts)
9. [Events & Interactivity](#9-events--interactivity)
10. [State Management](#10-state-management)
11. [Streaming & Real-time Output](#11-streaming--real-time-output)
12. [File Handling](#12-file-handling)
13. [Chatbots & Conversational AI](#13-chatbots--conversational-ai)
14. [Theming & Styling](#14-theming--styling)
15. [Authentication & Security](#15-authentication--security)
16. [Tabbed Interfaces & Multi-page Apps](#16-tabbed-interfaces--multi-page-apps)
17. [Integrating ML Frameworks](#17-integrating-ml-frameworks)
18. [API & Programmatic Access](#18-api--programmatic-access)
19. [Queuing, Concurrency & Performance](#19-queuing-concurrency--performance)
20. [Deployment](#20-deployment)
21. [Production Best Practices](#21-production-best-practices)
22. [Common Patterns & Recipes](#22-common-patterns--recipes)
23. [Debugging & Troubleshooting](#23-debugging--troubleshooting)
24. [Complete Project Examples](#24-complete-project-examples)

---

## 1. What is Gradio?

**Gradio** is an open-source Python library that lets you build **interactive web UIs for machine learning models** in minutes — no web development experience required.

### Why Gradio?
| Feature | Benefit |
|---|---|
| Pure Python | No HTML/CSS/JS needed |
| Instant sharing | `.launch(share=True)` gives a public URL |
| HuggingFace native | One-click deploy to Spaces |
| Built-in API | Every app is also a REST API automatically |
| 50+ components | Text, Image, Audio, Video, Dataframe, etc. |
| Real-time streaming | For LLMs and live model outputs |

### Gradio vs Alternatives
| | Gradio | Streamlit | Flask/FastAPI |
|---|---|---|---|
| Learning curve | Very Low | Low | High |
| ML-focused | ✅ | Partial | ❌ |
| Automatic API | ✅ | ❌ | Manual |
| Real-time streaming | ✅ | Limited | Manual |
| HF Spaces | ✅ Native | ✅ | ✅ |

---

## 2. Installation & Setup

### Basic Installation
```bash
pip install gradio
```

### Install with Extras
```bash
# With all optional dependencies
pip install "gradio[all]"

# Specific extras
pip install "gradio[oauth]"    # For OAuth login
pip install "gradio[image]"    # Extra image processing
```

### Version Check
```python
import gradio as gr
print(gr.__version__)
```

### Recommended Dev Environment
```bash
# Create virtual environment
python -m venv gradio-env
source gradio-env/bin/activate   # Linux/Mac
gradio-env\Scripts\activate      # Windows

pip install gradio torch transformers pillow numpy pandas
```

### Jupyter Notebook Setup
```python
# Gradio works natively in Jupyter
import gradio as gr

# In notebooks, use inline=True for embedded display
demo = gr.Interface(fn=lambda x: x, inputs="text", outputs="text")
demo.launch(inline=True)  # Renders inside notebook
```

---

## 3. Core Concepts

### The Mental Model

```
User Input → Gradio Component → Your Python Function → Gradio Component → User Output
```

Everything in Gradio follows this flow:
1. **Input Components** collect data from users
2. **Your function** processes the data (your ML model, API call, logic)
3. **Output Components** display the results

### Two Main APIs

```
Gradio
├── gr.Interface   ← Simple, structured (input → function → output)
└── gr.Blocks      ← Flexible, full control (custom layouts, events)
```

- Use **`Interface`** for straightforward demos
- Use **`Blocks`** for complex apps, multi-step workflows, chat apps

---

## 4. Your First Gradio App

### Hello World
```python
import gradio as gr

def greet(name):
    return f"Hello, {name}!"

demo = gr.Interface(fn=greet, inputs="text", outputs="text")
demo.launch()
```

### What Happens on `.launch()`
- A local server starts at `http://127.0.0.1:7860`
- Browser opens automatically (you can disable with `inbrowser=False`)
- The function `greet` is now both a UI and a REST API

### Key Launch Parameters
```python
demo.launch(
    server_name="0.0.0.0",   # Accessible on network (default: 127.0.0.1)
    server_port=7860,         # Port number
    share=True,               # Creates public gradio.live URL
    inbrowser=True,           # Auto-open browser
    debug=True,               # Show error tracebacks in UI
    show_error=True,          # Show errors in terminal
    quiet=False,              # Suppress console output
    auth=("user", "pass"),    # Basic auth
    ssl_keyfile="key.pem",    # HTTPS
    ssl_certfile="cert.pem",
)
```

---

## 5. Interface Class — Deep Dive

`gr.Interface` is the quickest way to wrap a function.

### Full Signature
```python
gr.Interface(
    fn,                     # Your function
    inputs,                 # Input component(s) — string shortcut or Component
    outputs,                # Output component(s)
    title=None,             # Page title
    description=None,       # Markdown description shown above
    article=None,           # Markdown shown below (citations, links)
    examples=None,          # List of example inputs
    cache_examples=False,   # Pre-compute example outputs
    live=False,             # Auto-run on input change (no Submit button)
    theme=None,             # gr.Theme object
    css=None,               # Custom CSS string
    allow_flagging="never", # "never", "auto", "manual"
    flagging_dir="flagged", # Where flagged data is saved
    flagging_options=None,  # Labels for flag button
)
```

### Multiple Inputs and Outputs
```python
import gradio as gr

def process(text, number, flag):
    result = text.upper() if flag else text.lower()
    return result, number * 2

demo = gr.Interface(
    fn=process,
    inputs=[
        gr.Textbox(label="Text"),
        gr.Slider(0, 100, label="Number"),
        gr.Checkbox(label="Uppercase?")
    ],
    outputs=[
        gr.Textbox(label="Processed Text"),
        gr.Number(label="Doubled Number")
    ]
)
demo.launch()
```

### Examples Feature
```python
demo = gr.Interface(
    fn=greet,
    inputs="text",
    outputs="text",
    examples=[
        ["Alice"],
        ["Bob"],
        ["World"],
    ],
    cache_examples=True  # Pre-compute so clicking is instant
)
```

### Live Mode (No Submit Button)
```python
demo = gr.Interface(
    fn=lambda x: x[::-1],  # Reverse string
    inputs="text",
    outputs="text",
    live=True  # Runs automatically as user types
)
```

### Flagging (Data Collection)
```python
demo = gr.Interface(
    fn=model_predict,
    inputs="image",
    outputs="label",
    allow_flagging="manual",
    flagging_options=["Wrong", "Offensive", "Other"],
    flagging_dir="./feedback_data"
)
```

---

## 6. Components Reference

### 📝 Text Components

#### Textbox
```python
gr.Textbox(
    value="",             # Default value
    label="Input",        # Label shown above
    placeholder="Type...", # Placeholder text
    lines=1,              # Number of visible lines (multiline if >1)
    max_lines=20,         # Max lines for auto-expand
    type="text",          # "text" or "password"
    interactive=True,     # Editable?
    visible=True,         # Shown?
    info="Helper text",   # Small text below component
    show_copy_button=True # Copy button on output
)
```

#### Markdown
```python
gr.Markdown("## Hello\n\nThis is **bold** text")
```

#### Code
```python
gr.Code(
    value="print('hello')",
    language="python",    # syntax highlighting language
    interactive=True,
    lines=10
)
```

### 🔢 Numeric Components

#### Number
```python
gr.Number(value=0, label="Enter number", minimum=0, maximum=100, step=1)
```

#### Slider
```python
gr.Slider(
    minimum=0,
    maximum=100,
    value=50,       # Default value
    step=1,         # Step size
    label="Volume"
)
```

### ✅ Selection Components

#### Checkbox
```python
gr.Checkbox(value=False, label="Enable feature")
```

#### CheckboxGroup
```python
gr.CheckboxGroup(
    choices=["Option A", "Option B", "Option C"],
    value=["Option A"],   # Default selected
    label="Select options"
)
```

#### Radio
```python
gr.Radio(
    choices=["Small", "Medium", "Large"],
    value="Medium",
    label="Size"
)
```

#### Dropdown
```python
gr.Dropdown(
    choices=["GPT-4", "Claude", "Gemini"],
    value="GPT-4",
    multiselect=False,    # Allow multiple selections
    label="Model"
)
```

### 🖼️ Media Components

#### Image
```python
gr.Image(
    value=None,            # Default image (path, URL, or numpy array)
    type="numpy",          # "numpy", "pil", "filepath"
    label="Upload Image",
    shape=(224, 224),      # Resize to this shape
    image_mode="RGB",      # "RGB", "L" (grayscale), "RGBA"
    sources=["upload", "webcam", "clipboard"],
    tool="editor",         # "editor", "select", "sketch", "color-sketch"
    streaming=False,       # For webcam streaming
)
```

#### Audio
```python
gr.Audio(
    value=None,
    type="numpy",          # "numpy" (sr, data), "filepath"
    label="Audio Input",
    sources=["upload", "microphone"],
    format="wav"           # Output format
)
```

#### Video
```python
gr.Video(
    value=None,
    label="Video",
    sources=["upload", "webcam"],
    format="mp4"
)
```

### 📊 Data Components

#### DataFrame
```python
import pandas as pd

gr.Dataframe(
    value=pd.DataFrame({"A": [1, 2], "B": [3, 4]}),
    headers=["Col A", "Col B"],
    row_count=5,
    col_count=(2, "fixed"),
    datatype=["number", "str"],
    interactive=True,       # Editable table
    label="Data"
)
```

#### JSON
```python
gr.JSON(value={"key": "value"}, label="Output JSON")
```

#### Label (Classification Output)
```python
gr.Label(
    value={"cat": 0.9, "dog": 0.08, "bird": 0.02},
    num_top_classes=3,
    label="Predictions"
)
```

### 📁 File Components

#### File
```python
gr.File(
    label="Upload File",
    file_count="single",      # "single", "multiple"
    file_types=[".pdf", ".txt", ".csv"],  # Restrict file types
    type="filepath"           # "filepath" or "binary"
)
```

#### UploadButton
```python
gr.UploadButton(
    label="Click to Upload",
    file_types=["image"],
    file_count="multiple"
)
```

### 🎨 Display Components

#### Plot
```python
import matplotlib.pyplot as plt

def make_plot():
    fig, ax = plt.subplots()
    ax.plot([1, 2, 3], [4, 5, 6])
    return fig

gr.Plot(label="Chart")
```

#### Gallery
```python
gr.Gallery(
    value=["img1.jpg", "img2.jpg"],
    label="Generated Images",
    columns=3,
    rows=2,
    height=400,
    object_fit="contain"    # CSS object-fit
)
```

#### HighlightedText
```python
gr.HighlightedText(
    value=[("Hello", "positive"), (" world", None), ("!", "negative")],
    color_map={"positive": "green", "negative": "red"},
    label="Sentiment"
)
```

#### AnnotatedImage
```python
gr.AnnotatedImage(
    value=(image, [(bbox, "cat"), (bbox2, "dog")]),
    label="Detected Objects"
)
```

### 🔘 Control Components

#### Button
```python
gr.Button(
    value="Run Model",
    variant="primary",    # "primary", "secondary", "stop"
    size="lg",           # "sm", "lg"
    icon="🚀"
)
```

#### ClearButton
```python
gr.ClearButton(components=[input_box, output_box])
```

---

## 7. Blocks API — Full Control

`gr.Blocks` gives you complete control over layout, events, and interactivity.

### Basic Blocks Structure
```python
import gradio as gr

with gr.Blocks() as demo:
    gr.Markdown("# My App")
    
    name_input = gr.Textbox(label="Your Name")
    greet_btn = gr.Button("Greet")
    output = gr.Textbox(label="Result")
    
    greet_btn.click(
        fn=lambda name: f"Hello, {name}!",
        inputs=name_input,
        outputs=output
    )

demo.launch()
```

### Blocks vs Interface
| Feature | Interface | Blocks |
|---|---|---|
| Layout control | ❌ Fixed | ✅ Full |
| Multiple events | ❌ | ✅ |
| Conditional visibility | ❌ | ✅ |
| Custom buttons | ❌ | ✅ |
| Tabs, Accordions | ❌ | ✅ |
| State between steps | Limited | ✅ Full |

---

## 8. Layouts

### Row and Column
```python
with gr.Blocks() as demo:
    with gr.Row():
        input1 = gr.Textbox(label="Input 1")
        input2 = gr.Textbox(label="Input 2")
    
    with gr.Column(scale=2):   # scale controls relative width
        output = gr.Textbox(label="Output")
    
    with gr.Row():
        with gr.Column(scale=1):
            gr.Markdown("Left panel")
        with gr.Column(scale=3):
            gr.Markdown("Right panel (wider)")
```

### Tabs
```python
with gr.Blocks() as demo:
    with gr.Tabs():
        with gr.Tab("Text"):
            text_in = gr.Textbox()
            text_out = gr.Textbox()
        
        with gr.Tab("Image"):
            img_in = gr.Image()
            img_out = gr.Image()
        
        with gr.Tab("About"):
            gr.Markdown("## About this app")
```

### Accordion (Collapsible)
```python
with gr.Blocks() as demo:
    with gr.Accordion("Advanced Settings", open=False):
        temperature = gr.Slider(0, 1, value=0.7, label="Temperature")
        max_tokens = gr.Slider(100, 4000, value=1000, label="Max Tokens")
```

### Group (Visually grouped)
```python
with gr.Group():
    name = gr.Textbox(label="Name")
    email = gr.Textbox(label="Email")
```

---

## 9. Events & Interactivity

### Event Types
```python
# Click
btn.click(fn, inputs, outputs)

# Change (triggers when value changes)
slider.change(fn, inputs, outputs)
textbox.change(fn, inputs, outputs)

# Submit (Enter key in textbox)
textbox.submit(fn, inputs, outputs)

# Select (for Gallery, Dataframe)
gallery.select(fn, inputs, outputs)

# Upload
file_input.upload(fn, inputs, outputs)

# Edit (for editable Dataframe)
df.edit(fn, inputs, outputs)
```

### Event Parameters
```python
btn.click(
    fn=my_function,
    inputs=[input1, input2],
    outputs=[output1, output2],
    api_name="predict",          # Name in the API
    queue=True,                  # Use queue (for streaming/slow fns)
    show_progress="full",        # "full", "minimal", "hidden"
    scroll_to_output=True,       # Auto-scroll to output
    trigger_mode="once",         # "once" or "multiple" (debounce)
)
```

### Chaining Events
```python
with gr.Blocks() as demo:
    input_box = gr.Textbox()
    step1_out = gr.Textbox()
    step2_out = gr.Textbox()
    
    btn = gr.Button("Process")
    
    btn.click(step1_fn, inputs=input_box, outputs=step1_out)\
       .then(step2_fn, inputs=step1_out, outputs=step2_out)
```

### `.then()` vs `.success()`
```python
# .then() always runs (even if previous step failed)
btn.click(fn1, ...).then(fn2, ...)

# .success() only runs if previous step succeeded (no exception)
btn.click(fn1, ...).success(fn2, ...)
```

### Showing/Hiding Components
```python
with gr.Blocks() as demo:
    checkbox = gr.Checkbox(label="Show advanced")
    advanced = gr.Column(visible=False)
    
    with advanced:
        gr.Slider(label="Advanced setting")
    
    checkbox.change(
        fn=lambda x: gr.update(visible=x),
        inputs=checkbox,
        outputs=advanced
    )
```

### `gr.update()` — Dynamic Updates
```python
# Update any component property
gr.update(value="new text", visible=True, interactive=False)

# Update choices in dropdown
gr.update(choices=["A", "B", "C"], value="A")

# Update label
gr.update(label="New Label")
```

---

## 10. State Management

### `gr.State` — Per-Session State
State persists across interactions **for a single user session**.

```python
import gradio as gr

def add_to_history(message, history):
    history = history or []
    history.append(message)
    return history, history

with gr.Blocks() as demo:
    history_state = gr.State([])  # Default value
    
    text_in = gr.Textbox(label="Add item")
    items_out = gr.JSON(label="History")
    btn = gr.Button("Add")
    
    btn.click(
        fn=add_to_history,
        inputs=[text_in, history_state],
        outputs=[history_state, items_out]
    )
```

### Important: State is Per-User
- Each browser session gets its own copy of `gr.State`
- State is **NOT** shared between users
- State is **lost** when the page refreshes

### Global State (Shared Between Users)
```python
# Use a regular Python variable (be careful with concurrency!)
shared_counter = {"count": 0}

def increment():
    shared_counter["count"] += 1
    return shared_counter["count"]
```

---

## 11. Streaming & Real-time Output

### Generator-based Streaming
```python
import gradio as gr
import time

def stream_text(prompt):
    words = f"Response to: {prompt}".split()
    result = ""
    for word in words:
        result += word + " "
        time.sleep(0.1)
        yield result  # yield instead of return!

with gr.Blocks() as demo:
    prompt = gr.Textbox(label="Prompt")
    output = gr.Textbox(label="Output")
    btn = gr.Button("Generate")
    
    btn.click(fn=stream_text, inputs=prompt, outputs=output)

demo.queue()  # Queue is required for streaming
demo.launch()
```

### Streaming with LLMs (e.g., OpenAI)
```python
from openai import OpenAI
import gradio as gr

client = OpenAI()

def stream_llm(prompt, history):
    messages = [{"role": "user", "content": prompt}]
    stream = client.chat.completions.create(
        model="gpt-4",
        messages=messages,
        stream=True
    )
    
    response = ""
    for chunk in stream:
        delta = chunk.choices[0].delta.content or ""
        response += delta
        yield response

with gr.Blocks() as demo:
    output = gr.Textbox(label="LLM Output")
    prompt = gr.Textbox(label="Prompt")
    btn = gr.Button("Generate")
    btn.click(stream_llm, inputs=[prompt, gr.State([])], outputs=output)

demo.queue()
demo.launch()
```

### Image Streaming (Webcam)
```python
def process_webcam_frame(frame):
    # frame is numpy array (H, W, 3)
    # Apply any real-time processing
    return frame

demo = gr.Interface(
    fn=process_webcam_frame,
    inputs=gr.Image(sources=["webcam"], streaming=True),
    outputs="image",
    live=True
)
```

---

## 12. File Handling

### Accepting Files
```python
def process_file(file):
    # file is a filepath string (use open() on it)
    with open(file, "r") as f:
        content = f.read()
    return f"File has {len(content)} characters"

demo = gr.Interface(
    fn=process_file,
    inputs=gr.File(file_types=[".txt", ".csv"]),
    outputs="text"
)
```

### Returning Files for Download
```python
import gradio as gr
import os

def create_file(text):
    path = "/tmp/output.txt"
    with open(path, "w") as f:
        f.write(text)
    return path  # Return filepath

demo = gr.Interface(
    fn=create_file,
    inputs="text",
    outputs=gr.File(label="Download")
)
```

### Image Processing Pipeline
```python
from PIL import Image
import numpy as np
import gradio as gr

def process_image(img):
    # img is numpy array when type="numpy"
    # Convert to grayscale
    gray = np.mean(img, axis=2).astype(np.uint8)
    return gray

demo = gr.Interface(
    fn=process_image,
    inputs=gr.Image(type="numpy"),
    outputs=gr.Image(type="numpy")
)
```

---

## 13. Chatbots & Conversational AI

### Basic Chatbot
```python
import gradio as gr

def chat(message, history):
    # history is list of [user_msg, bot_msg] pairs
    response = f"You said: {message}"
    return response

demo = gr.ChatInterface(
    fn=chat,
    title="My Chatbot",
    description="A simple echo chatbot"
)
demo.launch()
```

### `gr.ChatInterface` — Easiest Chatbot
```python
gr.ChatInterface(
    fn=chat,                          # fn(message, history) -> str
    title="Chatbot",
    description="Description",
    examples=["Hello!", "Help me write code"],
    theme=gr.themes.Soft(),
    chatbot=gr.Chatbot(height=400),   # Customize the chatbot component
    textbox=gr.Textbox(placeholder="Ask me anything"),
    submit_btn="Send",
    retry_btn="🔄 Retry",
    undo_btn="↩ Undo",
    clear_btn="🗑️ Clear",
)
```

### Streaming Chatbot
```python
import gradio as gr

def stream_chat(message, history):
    # Simulate streaming response
    response = f"Echo: {message}"
    for i in range(len(response)):
        yield response[:i+1]  # Yield partial responses

demo = gr.ChatInterface(fn=stream_chat)
demo.queue()
demo.launch()
```

### Custom Chatbot with Blocks (Full Control)
```python
import gradio as gr

def respond(message, chat_history):
    bot_response = f"Bot: {message[::-1]}"
    chat_history.append((message, bot_response))
    return "", chat_history  # Clear input, update chat

with gr.Blocks() as demo:
    chatbot = gr.Chatbot(
        label="Conversation",
        height=400,
        bubble_full_width=False
    )
    msg = gr.Textbox(
        label="Message",
        placeholder="Type here...",
        show_label=False
    )
    with gr.Row():
        submit = gr.Button("Send", variant="primary")
        clear = gr.ClearButton([msg, chatbot])
    
    msg.submit(respond, [msg, chatbot], [msg, chatbot])
    submit.click(respond, [msg, chatbot], [msg, chatbot])

demo.launch()
```

### Multimodal Chatbot
```python
def multimodal_chat(message, history):
    # message is dict: {"text": "...", "files": [...]}
    text = message["text"]
    files = message.get("files", [])
    
    if files:
        return f"Got {len(files)} file(s) + text: {text}"
    return f"Text only: {text}"

demo = gr.ChatInterface(
    fn=multimodal_chat,
    multimodal=True   # Enables file upload in chat
)
```

### Chatbot with History + System Prompt
```python
import gradio as gr
from openai import OpenAI

client = OpenAI()
SYSTEM_PROMPT = "You are a helpful coding assistant."

def chat_with_history(message, history):
    messages = [{"role": "system", "content": SYSTEM_PROMPT}]
    
    # Add conversation history
    for human, assistant in history:
        messages.append({"role": "user", "content": human})
        messages.append({"role": "assistant", "content": assistant})
    
    messages.append({"role": "user", "content": message})
    
    # Stream the response
    response = client.chat.completions.create(
        model="gpt-4",
        messages=messages,
        stream=True
    )
    
    partial = ""
    for chunk in response:
        delta = chunk.choices[0].delta.content or ""
        partial += delta
        yield partial

demo = gr.ChatInterface(fn=chat_with_history)
demo.queue()
demo.launch()
```

---

## 14. Theming & Styling

### Built-in Themes
```python
import gradio as gr

# Available built-in themes
demo = gr.Blocks(theme=gr.themes.Default())
demo = gr.Blocks(theme=gr.themes.Soft())
demo = gr.Blocks(theme=gr.themes.Monochrome())
demo = gr.Blocks(theme=gr.themes.Glass())
demo = gr.Blocks(theme=gr.themes.Origin())
demo = gr.Blocks(theme=gr.themes.Citrus())
demo = gr.Blocks(theme=gr.themes.Ocean())
```

### Custom Theme
```python
my_theme = gr.themes.Soft(
    primary_hue="blue",          # Primary color
    secondary_hue="sky",         # Secondary color
    neutral_hue="slate",         # Neutral color
    font=gr.themes.GoogleFont("Poppins"),  # Font
    font_mono=gr.themes.GoogleFont("Fira Code"),
    radius_size=gr.themes.sizes.radius_lg,   # Border radius
    spacing_size=gr.themes.sizes.spacing_md, # Spacing
    text_size=gr.themes.sizes.text_lg        # Base text size
)

demo = gr.Blocks(theme=my_theme)
```

### Custom CSS
```python
custom_css = """
/* Target all textboxes */
.gradio-textbox textarea {
    font-size: 18px;
    border-radius: 12px;
}

/* Target by elem_id */
#my-button {
    background: linear-gradient(90deg, #667eea 0%, #764ba2 100%);
    border: none;
    color: white;
}

/* Target by elem_classes */
.highlight-box {
    border: 2px solid gold;
    padding: 10px;
}
"""

with gr.Blocks(css=custom_css) as demo:
    btn = gr.Button("Click", elem_id="my-button")
    box = gr.Textbox(elem_classes=["highlight-box"])
```

### Dark Mode
```python
with gr.Blocks(theme=gr.themes.Soft()) as demo:
    pass
# Users can toggle dark mode via the moon icon in the UI
# Or force it:
demo.launch(js="() => { document.body.classList.add('dark'); }")
```

### HuggingFace Themes Gallery
Visit [huggingface.co/spaces/gradio/theme-gallery](https://huggingface.co/spaces/gradio/theme-gallery)
```python
# Load a community theme
theme = gr.Theme.from_hub("nateraw/gradio-theme-slate")
```

---

## 15. Authentication & Security

### Basic Auth (Username/Password)
```python
# Single user
demo.launch(auth=("admin", "secret123"))

# Multiple users
demo.launch(auth=[
    ("alice", "pass1"),
    ("bob", "pass2"),
])

# Function-based auth (check database, etc.)
def check_auth(username, password):
    return username == "admin" and password == "secret"

demo.launch(auth=check_auth)
```

### OAuth (HuggingFace Login)
```python
import gradio as gr
from huggingface_hub import whoami

def greet(request: gr.Request):
    user_info = whoami(request.username)
    return f"Welcome, {user_info['name']}!"

with gr.Blocks() as demo:
    btn = gr.Button("Get my info")
    output = gr.Textbox()
    btn.click(greet, outputs=output)

demo.launch()
```

### Accessing Request Info
```python
def my_fn(text, request: gr.Request):
    # Access HTTP request info
    print(request.username)      # Logged-in user (if auth enabled)
    print(request.headers)       # HTTP headers
    print(request.client.host)   # Client IP
    return text

# The gr.Request parameter must be the LAST parameter
```

### Environment Variables for Secrets
```python
import os
import gradio as gr

API_KEY = os.environ.get("OPENAI_API_KEY")
# Never hardcode secrets!
```

---

## 16. Tabbed Interfaces & Multi-page Apps

### Multiple Interfaces as Tabs
```python
import gradio as gr

# Define separate interfaces
text_demo = gr.Interface(fn=lambda x: x.upper(), inputs="text", outputs="text")
img_demo = gr.Interface(fn=lambda x: x, inputs="image", outputs="image")

# Combine into tabbed app
demo = gr.TabbedInterface(
    interface_list=[text_demo, img_demo],
    tab_names=["Text Processor", "Image Viewer"],
    title="Multi-Tool App"
)
demo.launch()
```

### Blocks with Tabs (Advanced)
```python
with gr.Blocks() as demo:
    gr.Markdown("# Multi-Tool App")
    
    with gr.Tabs() as tabs:
        with gr.Tab("🔤 Text", id=0):
            with gr.Row():
                txt_in = gr.Textbox(label="Input")
                txt_out = gr.Textbox(label="Output")
            gr.Button("Process").click(
                lambda x: x.upper(), txt_in, txt_out
            )
        
        with gr.Tab("🖼️ Image", id=1):
            img_in = gr.Image(label="Upload")
            img_out = gr.Image(label="Result")
            gr.Button("Flip").click(
                lambda x: x[::-1], img_in, img_out
            )
        
        with gr.Tab("📊 Data", id=2):
            df_out = gr.Dataframe()
            gr.Button("Load Data").click(
                lambda: [[1, 2], [3, 4]], outputs=df_out
            )
    
    # Switch tabs programmatically
    tabs.select(fn=lambda evt: print(f"Tab {evt}"), inputs=None)
```

---

## 17. Integrating ML Frameworks

### 🤗 HuggingFace Transformers
```python
import gradio as gr
from transformers import pipeline

# Load model (first run downloads it)
classifier = pipeline("sentiment-analysis")

def classify(text):
    result = classifier(text)[0]
    return {result["label"]: result["score"]}

demo = gr.Interface(
    fn=classify,
    inputs=gr.Textbox(label="Text"),
    outputs=gr.Label(label="Sentiment")
)
demo.launch()
```

### Image Classification
```python
from transformers import pipeline
import gradio as gr

pipe = pipeline("image-classification", model="google/vit-base-patch16-224")

def classify_image(img):
    results = pipe(img)
    return {r["label"]: r["score"] for r in results}

demo = gr.Interface(
    fn=classify_image,
    inputs=gr.Image(type="pil"),
    outputs=gr.Label(num_top_classes=5)
)
```

### Text Generation (GPT-style)
```python
from transformers import AutoTokenizer, AutoModelForCausalLM
import gradio as gr
import torch

model_name = "gpt2"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)

def generate(prompt, max_new_tokens=100, temperature=0.7):
    inputs = tokenizer(prompt, return_tensors="pt")
    with torch.no_grad():
        outputs = model.generate(
            **inputs,
            max_new_tokens=max_new_tokens,
            temperature=temperature,
            do_sample=True
        )
    return tokenizer.decode(outputs[0], skip_special_tokens=True)

demo = gr.Interface(
    fn=generate,
    inputs=[
        gr.Textbox(label="Prompt", lines=3),
        gr.Slider(50, 500, value=100, step=10, label="Max Tokens"),
        gr.Slider(0.1, 2.0, value=0.7, step=0.1, label="Temperature")
    ],
    outputs=gr.Textbox(label="Generated Text", lines=5)
)
```

### PyTorch Custom Model
```python
import torch
import gradio as gr

# Load your trained model
model = torch.load("my_model.pt")
model.eval()

def predict(input_data):
    tensor = torch.tensor(input_data).float()
    with torch.no_grad():
        output = model(tensor.unsqueeze(0))
    return output.item()
```

### Scikit-Learn
```python
import pickle
import gradio as gr
import numpy as np

with open("model.pkl", "rb") as f:
    model = pickle.load(f)

def predict(feature1, feature2, feature3):
    X = np.array([[feature1, feature2, feature3]])
    prediction = model.predict(X)[0]
    probabilities = model.predict_proba(X)[0]
    return str(prediction), {str(i): p for i, p in enumerate(probabilities)}

demo = gr.Interface(
    fn=predict,
    inputs=[
        gr.Number(label="Feature 1"),
        gr.Number(label="Feature 2"),
        gr.Number(label="Feature 3")
    ],
    outputs=[
        gr.Textbox(label="Prediction"),
        gr.Label(label="Probabilities")
    ]
)
```

### Stable Diffusion (Image Generation)
```python
import gradio as gr
from diffusers import StableDiffusionPipeline
import torch

pipe = StableDiffusionPipeline.from_pretrained(
    "runwayml/stable-diffusion-v1-5",
    torch_dtype=torch.float16
).to("cuda")

def generate_image(prompt, negative_prompt, steps, guidance_scale):
    image = pipe(
        prompt,
        negative_prompt=negative_prompt,
        num_inference_steps=steps,
        guidance_scale=guidance_scale
    ).images[0]
    return image

demo = gr.Interface(
    fn=generate_image,
    inputs=[
        gr.Textbox(label="Prompt"),
        gr.Textbox(label="Negative Prompt"),
        gr.Slider(10, 50, value=30, step=1, label="Steps"),
        gr.Slider(1, 20, value=7.5, step=0.5, label="Guidance Scale"),
    ],
    outputs=gr.Image(label="Generated Image")
)

demo.queue()
demo.launch()
```

### Whisper (Speech-to-Text)
```python
import gradio as gr
import whisper

model = whisper.load_model("base")

def transcribe(audio):
    # audio is (sample_rate, numpy_array)
    result = model.transcribe(audio)
    return result["text"]

demo = gr.Interface(
    fn=transcribe,
    inputs=gr.Audio(sources=["microphone", "upload"], type="filepath"),
    outputs=gr.Textbox(label="Transcript"),
    title="Whisper Transcription"
)
```

---

## 18. API & Programmatic Access

### Every Gradio App is an API!
When you run `demo.launch()`, Gradio automatically exposes a REST API.

### API Documentation
Visit: `http://localhost:7860/?view=api`

### Python Client
```python
from gradio_client import Client

# Connect to any Gradio app
client = Client("http://localhost:7860")
# OR connect to HuggingFace Space
client = Client("username/space-name")

# Call the predict endpoint
result = client.predict("Hello", api_name="/predict")
print(result)
```

### JavaScript Fetch
```javascript
const response = await fetch("http://localhost:7860/run/predict", {
    method: "POST",
    headers: {"Content-Type": "application/json"},
    body: JSON.stringify({
        data: ["Hello World"]
    })
});

const data = await response.json();
console.log(data.data);
```

### Naming API Endpoints
```python
btn.click(
    fn=my_function,
    inputs=input_box,
    outputs=output_box,
    api_name="my_endpoint"  # Accessible at /run/my_endpoint
)
```

### Batch Processing via API
```python
def predict_batch(items: list):
    return [process(item) for item in items]

from gradio_client import Client
client = Client("http://localhost:7860")

# Submit multiple jobs
jobs = [client.submit(item) for item in my_items]
results = [job.result() for job in jobs]
```

---

## 19. Queuing, Concurrency & Performance

### Why Queue?
Without queue, only one user can use the app at a time. Queue handles concurrent users.

```python
demo.queue(
    max_size=20,              # Max requests in queue
    default_concurrency_limit=5  # Parallel workers
)
demo.launch()
```

### Per-Function Concurrency
```python
@demo.queue(concurrency_limit=3)  # Max 3 parallel calls
def slow_function(x):
    time.sleep(5)
    return x
```

### Progress Indicators
```python
import gradio as gr
import time

def slow_fn(n, progress=gr.Progress()):
    progress(0, desc="Starting...")
    for i in range(n):
        time.sleep(0.1)
        progress((i + 1) / n, desc=f"Step {i+1}/{n}")
    return "Done!"

demo = gr.Interface(
    fn=slow_fn,
    inputs=gr.Slider(1, 20, step=1),
    outputs="text"
)
demo.queue()
demo.launch()
```

### Caching for Performance
```python
# Cache example outputs (precompute)
demo = gr.Interface(
    fn=slow_model,
    inputs="text",
    outputs="text",
    examples=[["cat"], ["dog"], ["bird"]],
    cache_examples=True  # Pre-run all examples
)
```

### Load Balancing (Multiple Workers)
```python
# Run multiple Gradio workers
import subprocess
for i in range(4):
    subprocess.Popen(["python", "app.py", f"--port={7860+i}"])

# Use nginx or a load balancer in front
```

### Model Loading Best Practices
```python
# ✅ Load model ONCE at module level
model = load_model("my_model.h5")  # Outside function!

def predict(x):
    return model.predict(x)

# ❌ Never load model inside the function
def predict_bad(x):
    model = load_model("my_model.h5")  # Loads every request!
    return model.predict(x)
```

---

## 20. Deployment

### Option 1: HuggingFace Spaces (Easiest)

**Step 1:** Create account at huggingface.co

**Step 2:** Create a new Space → Choose Gradio SDK

**Step 3:** Create these files:

```
my-space/
├── app.py          # Your Gradio app (demo.launch())
├── requirements.txt
└── README.md       # Optional: Space metadata
```

**app.py:**
```python
import gradio as gr

def greet(name):
    return f"Hello, {name}!"

demo = gr.Interface(fn=greet, inputs="text", outputs="text")
demo.launch()
```

**requirements.txt:**
```
gradio
torch
transformers
```

**Step 4:** Push to HuggingFace
```bash
git init
git remote add origin https://huggingface.co/spaces/USERNAME/SPACE_NAME
git add .
git commit -m "Initial commit"
git push origin main
```

**Space Hardware:**
- Free: CPU Basic (2 vCPU, 16 GB RAM)
- Paid: GPU (T4, A10G, A100)

### Option 2: Docker

**Dockerfile:**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 7860

CMD ["python", "app.py"]
```

**app.py:**
```python
import gradio as gr

def predict(text):
    return text.upper()

demo = gr.Interface(fn=predict, inputs="text", outputs="text")
demo.launch(server_name="0.0.0.0", server_port=7860)
```

```bash
docker build -t my-gradio-app .
docker run -p 7860:7860 my-gradio-app
```

### Option 3: Cloud VM (AWS/GCP/Azure)

```bash
# On your VM
pip install gradio your-model-deps

# Run with screen or tmux for persistence
screen -S gradio
python app.py

# Or use systemd service
```

**systemd service (`/etc/systemd/system/gradio.service`):**
```ini
[Unit]
Description=Gradio App
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/myapp
ExecStart=/home/ubuntu/myapp/venv/bin/python app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable gradio
sudo systemctl start gradio
```

### Option 4: FastAPI + Gradio (Hybrid)

```python
from fastapi import FastAPI
import gradio as gr
import uvicorn

app = FastAPI()

@app.get("/health")
def health():
    return {"status": "ok"}

def gradio_fn(text):
    return text.upper()

gradio_app = gr.Interface(fn=gradio_fn, inputs="text", outputs="text")

# Mount Gradio at /gradio
app = gr.mount_gradio_app(app, gradio_app, path="/gradio")

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

### Option 5: Nginx Reverse Proxy

```nginx
server {
    listen 80;
    server_name myapp.com;
    
    location / {
        proxy_pass http://127.0.0.1:7860;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";  # For WebSocket
    }
}
```

### Environment Variables for Production
```python
import os

demo.launch(
    server_name=os.getenv("HOST", "0.0.0.0"),
    server_port=int(os.getenv("PORT", 7860)),
    share=os.getenv("SHARE", "false").lower() == "true"
)
```

---

## 21. Production Best Practices

### 1. Error Handling
```python
import gradio as gr

def safe_predict(text):
    try:
        result = model.predict(text)
        return result
    except Exception as e:
        raise gr.Error(f"Prediction failed: {str(e)}")

# gr.Error shows a nice error toast to the user
# gr.Warning shows a warning
# gr.Info shows an info message
```

### 2. Input Validation
```python
def validate_and_predict(text, max_len=500):
    if not text.strip():
        raise gr.Error("Please enter some text")
    if len(text) > max_len:
        raise gr.Error(f"Text too long. Max {max_len} characters")
    return model.predict(text)
```

### 3. Rate Limiting
```python
from collections import defaultdict
from datetime import datetime, timedelta

request_counts = defaultdict(list)
RATE_LIMIT = 10  # requests per minute

def rate_limited_fn(text, request: gr.Request):
    ip = request.client.host
    now = datetime.now()
    
    # Clean old requests
    request_counts[ip] = [t for t in request_counts[ip] 
                          if now - t < timedelta(minutes=1)]
    
    if len(request_counts[ip]) >= RATE_LIMIT:
        raise gr.Error("Rate limit exceeded. Try again in a minute.")
    
    request_counts[ip].append(now)
    return model.predict(text)
```

### 4. Logging
```python
import logging
import gradio as gr

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s",
    filename="app.log"
)
logger = logging.getLogger(__name__)

def predict_with_logging(text, request: gr.Request):
    logger.info(f"Prediction request from {request.client.host}: {text[:50]}")
    try:
        result = model.predict(text)
        logger.info(f"Prediction success")
        return result
    except Exception as e:
        logger.error(f"Prediction failed: {e}")
        raise gr.Error("Internal error")
```

### 5. Model Warm-up
```python
import gradio as gr

# Warm up model on startup
def warmup():
    logger.info("Warming up model...")
    model.predict("warmup text")
    logger.info("Model ready!")

with gr.Blocks() as demo:
    pass

demo.load(warmup)  # Run on app start
```

### 6. Health Checks
```python
from fastapi import FastAPI
import gradio as gr

fastapi_app = FastAPI()

@fastapi_app.get("/health")
def health():
    return {"status": "healthy", "model": "loaded"}

gradio_demo = gr.Interface(...)
app = gr.mount_gradio_app(fastapi_app, gradio_demo, path="/")
```

### 7. Secrets Management
```python
# .env file
OPENAI_API_KEY=sk-...
HF_TOKEN=hf_...

# Load in Python
from dotenv import load_dotenv
import os

load_dotenv()
api_key = os.getenv("OPENAI_API_KEY")
```

### 8. Versioning & Rollback
```bash
# Tag your releases
git tag v1.0.0
git push origin v1.0.0

# HuggingFace Spaces auto-deploys on push
# Use branches for staging
git checkout -b staging
```

### 9. Monitoring
```python
# Track metrics
from prometheus_client import Counter, Histogram, start_http_server

REQUEST_COUNT = Counter("gradio_requests_total", "Total requests")
REQUEST_DURATION = Histogram("gradio_request_duration_seconds", "Request duration")

def monitored_predict(text):
    REQUEST_COUNT.inc()
    with REQUEST_DURATION.time():
        return model.predict(text)

start_http_server(9090)  # Prometheus metrics at :9090
```

---

## 22. Common Patterns & Recipes

### Pattern 1: Model Comparison
```python
import gradio as gr

def compare_models(text):
    result_a = model_a.predict(text)
    result_b = model_b.predict(text)
    return result_a, result_b

demo = gr.Interface(
    fn=compare_models,
    inputs=gr.Textbox(label="Input"),
    outputs=[
        gr.Textbox(label="Model A"),
        gr.Textbox(label="Model B")
    ]
)
```

### Pattern 2: Pipeline (Multi-step)
```python
with gr.Blocks() as demo:
    raw_input = gr.Textbox(label="Raw Text")
    cleaned = gr.Textbox(label="Cleaned")
    translated = gr.Textbox(label="Translated")
    summarized = gr.Textbox(label="Summary")
    
    gr.Button("Step 1: Clean").click(clean_fn, raw_input, cleaned)
    gr.Button("Step 2: Translate").click(translate_fn, cleaned, translated)
    gr.Button("Step 3: Summarize").click(summarize_fn, translated, summarized)
    
    # Or run all at once:
    gr.Button("Run All").click(clean_fn, raw_input, cleaned)\
                        .then(translate_fn, cleaned, translated)\
                        .then(summarize_fn, translated, summarized)
```

### Pattern 3: Dynamic UI Based on Input
```python
with gr.Blocks() as demo:
    task = gr.Dropdown(["Translation", "Summarization", "QA"], label="Task")
    
    translation_box = gr.Textbox(label="Target Language", visible=False)
    length_slider = gr.Slider(label="Summary Length", visible=False)
    question_box = gr.Textbox(label="Question", visible=False)
    
    def update_ui(task):
        return (
            gr.update(visible=task == "Translation"),
            gr.update(visible=task == "Summarization"),
            gr.update(visible=task == "QA"),
        )
    
    task.change(update_ui, task, [translation_box, length_slider, question_box])
```

### Pattern 4: Before/After Image Comparison
```python
with gr.Blocks() as demo:
    with gr.Row():
        before = gr.Image(label="Original")
        after = gr.Image(label="Processed")
    
    process_btn = gr.Button("Apply Filter")
    process_btn.click(apply_filter, before, after)
```

### Pattern 5: Batch File Processing
```python
import gradio as gr

def process_batch(files):
    results = []
    for file in files:
        result = process_single_file(file)
        results.append(result)
    return "\n".join(results)

demo = gr.Interface(
    fn=process_batch,
    inputs=gr.File(file_count="multiple"),
    outputs=gr.Textbox(label="Results", lines=10)
)
```

### Pattern 6: Progress with Cancellation
```python
import gradio as gr
import time

def long_task(n, progress=gr.Progress(track_tqdm=True)):
    for i in progress.tqdm(range(n), desc="Processing"):
        time.sleep(0.1)
        # Check for cancellation handled automatically
    return "Complete!"

with gr.Blocks() as demo:
    n = gr.Slider(1, 100, value=50)
    output = gr.Textbox()
    btn = gr.Button("Start")
    stop_btn = gr.Button("Stop", variant="stop")
    
    run_event = btn.click(long_task, n, output)
    stop_btn.click(fn=None, cancels=[run_event])
```

### Pattern 7: Async Functions
```python
import asyncio
import gradio as gr
import httpx

async def fetch_data(url):
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.text

demo = gr.Interface(
    fn=fetch_data,
    inputs=gr.Textbox(label="URL"),
    outputs=gr.Code(label="Response")
)
```

---

## 23. Debugging & Troubleshooting

### Enable Debug Mode
```python
demo.launch(debug=True, show_error=True)
```

### Common Errors & Fixes

| Error | Cause | Fix |
|---|---|---|
| `Address already in use` | Port 7860 busy | Use `server_port=7861` |
| `CUDA out of memory` | GPU OOM | Reduce batch size, use `torch.cuda.empty_cache()` |
| Components don't update | Wrong return type | Return exact type matching component |
| Streaming not working | Missing queue | Add `demo.queue()` before launch |
| CORS error | Cross-origin request | Add `allowed_paths` or use CORS headers |
| Auth not working | Wrong format | Use tuple `("user", "pass")` not dict |
| File not found | Wrong path | Check working directory |

### Type Mismatch Debugging
```python
def debug_fn(x):
    print(f"Input type: {type(x)}, value: {x}")  # Print to terminal
    result = process(x)
    print(f"Output type: {type(result)}, value: {result}")
    return result
```

### Gradio Share Troubleshooting
```python
# If share=True doesn't work behind corporate firewall:
demo.launch(
    share=True,
    show_tips=True  # Shows the share URL prominently
)
```

### Memory Leaks
```python
import torch
import gc

def predict(image):
    result = model(image)
    
    # Clean up GPU memory
    torch.cuda.empty_cache()
    gc.collect()
    
    return result
```

### Testing Gradio Apps
```python
from gradio_client import Client

# Test your app programmatically
client = Client("http://localhost:7860")

# Test basic functionality
result = client.predict("test input", api_name="/predict")
assert result == "expected output"

# Test error handling
try:
    result = client.predict("", api_name="/predict")
except Exception as e:
    assert "Please enter" in str(e)
```

---

## 24. Complete Project Examples

### Project 1: Document Q&A App
```python
import gradio as gr
from transformers import pipeline

qa_pipeline = pipeline("question-answering")

def answer_question(document, question):
    if not document or not question:
        raise gr.Error("Please provide both a document and a question")
    
    result = qa_pipeline(question=question, context=document)
    confidence = f"{result['score']:.1%}"
    return result["answer"], confidence

with gr.Blocks(title="Document Q&A", theme=gr.themes.Soft()) as demo:
    gr.Markdown("# 📄 Document Q&A System")
    gr.Markdown("Paste a document and ask questions about it.")
    
    with gr.Row():
        with gr.Column(scale=2):
            document = gr.Textbox(
                label="Document",
                placeholder="Paste your document here...",
                lines=15
            )
        with gr.Column(scale=1):
            question = gr.Textbox(label="Question", lines=3)
            answer = gr.Textbox(label="Answer", interactive=False)
            confidence = gr.Textbox(label="Confidence", interactive=False)
            btn = gr.Button("Get Answer", variant="primary")
    
    btn.click(answer_question, [document, question], [answer, confidence])
    question.submit(answer_question, [document, question], [answer, confidence])

demo.queue()
demo.launch()
```

### Project 2: Image Classification with Explanation
```python
import gradio as gr
from transformers import pipeline
import numpy as np

classifier = pipeline("image-classification", model="google/vit-base-patch16-224")

def classify_with_explanation(image):
    if image is None:
        raise gr.Error("Please upload an image")
    
    results = classifier(image, top_k=5)
    
    # Format for Label component
    predictions = {r["label"]: r["score"] for r in results}
    
    # Generate text explanation
    top = results[0]
    explanation = f"""
    **Top prediction:** {top['label']} ({top['score']:.1%} confidence)
    
    The model analyzed your image and found patterns most consistent with **{top['label']}**.
    
    **All predictions:**
    """
    for r in results:
        bar = "█" * int(r["score"] * 20)
        explanation += f"\n- {r['label']}: {r['score']:.1%} {bar}"
    
    return predictions, explanation

with gr.Blocks(title="Image Classifier", theme=gr.themes.Ocean()) as demo:
    gr.Markdown("# 🔍 Image Classifier\nPowered by Vision Transformer (ViT)")
    
    with gr.Row():
        img_input = gr.Image(label="Upload Image", type="pil")
        
        with gr.Column():
            label_output = gr.Label(label="Predictions", num_top_classes=5)
            explanation_output = gr.Markdown()
    
    examples = gr.Examples(
        examples=["cat.jpg", "dog.jpg"],
        inputs=img_input
    )
    
    img_input.change(
        classify_with_explanation,
        inputs=img_input,
        outputs=[label_output, explanation_output]
    )

demo.queue()
demo.launch()
```

### Project 3: Full Chatbot with Memory & Persona
```python
import gradio as gr
from openai import OpenAI
import os

client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

PERSONAS = {
    "Assistant": "You are a helpful AI assistant.",
    "Teacher": "You are a patient teacher who explains things simply.",
    "Coder": "You are an expert programmer. Provide code examples.",
    "Poet": "You are a creative poet. Respond in verse when appropriate."
}

def chat(message, history, persona, temperature):
    system_prompt = PERSONAS[persona]
    
    messages = [{"role": "system", "content": system_prompt}]
    for human, assistant in history:
        messages.append({"role": "user", "content": human})
        messages.append({"role": "assistant", "content": assistant})
    messages.append({"role": "user", "content": message})
    
    stream = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=messages,
        temperature=temperature,
        stream=True
    )
    
    partial = ""
    for chunk in stream:
        delta = chunk.choices[0].delta.content or ""
        partial += delta
        yield partial

with gr.Blocks(title="Smart Chatbot", theme=gr.themes.Soft()) as demo:
    gr.Markdown("# 🤖 Smart Chatbot")
    
    with gr.Row():
        persona = gr.Dropdown(
            choices=list(PERSONAS.keys()),
            value="Assistant",
            label="Persona",
            scale=2
        )
        temperature = gr.Slider(0, 2, value=0.7, step=0.1, label="Creativity", scale=3)
    
    chatbot = gr.Chatbot(height=450, bubble_full_width=False)
    
    with gr.Row():
        msg = gr.Textbox(
            placeholder="Type your message...",
            show_label=False,
            scale=4
        )
        send = gr.Button("Send", variant="primary", scale=1)
    
    with gr.Row():
        clear = gr.ClearButton([msg, chatbot], value="Clear Chat")
        gr.Button("Export").click(
            lambda h: "\n".join([f"User: {u}\nBot: {b}" for u, b in h]),
            chatbot, gr.Textbox(visible=False)
        )
    
    def respond(message, history, persona, temp):
        history.append((message, ""))
        for chunk in chat(message, history[:-1], persona, temp):
            history[-1] = (message, chunk)
            yield "", history
    
    msg.submit(respond, [msg, chatbot, persona, temperature], [msg, chatbot])
    send.click(respond, [msg, chatbot, persona, temperature], [msg, chatbot])

demo.queue()
demo.launch()
```

### Project 4: Production ML Dashboard
```python
import gradio as gr
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from datetime import datetime
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Simulated model (replace with real model)
class MockModel:
    def predict(self, features):
        return np.random.rand()
    def predict_batch(self, df):
        return np.random.rand(len(df))

model = MockModel()
prediction_log = []

def single_predict(feature1, feature2, feature3, request: gr.Request):
    try:
        features = [feature1, feature2, feature3]
        pred = model.predict(features)
        
        # Log prediction
        entry = {
            "timestamp": datetime.now().isoformat(),
            "features": features,
            "prediction": pred,
            "user": request.username or "anonymous"
        }
        prediction_log.append(entry)
        logger.info(f"Prediction: {entry}")
        
        confidence = "High" if pred > 0.8 else "Medium" if pred > 0.5 else "Low"
        return f"{pred:.4f}", confidence
    except Exception as e:
        logger.error(f"Prediction error: {e}")
        raise gr.Error(f"Prediction failed: {str(e)}")

def batch_predict(file):
    try:
        df = pd.read_csv(file)
        predictions = model.predict_batch(df)
        df["prediction"] = predictions
        output_path = "/tmp/predictions.csv"
        df.to_csv(output_path, index=False)
        return df.head(10), output_path
    except Exception as e:
        raise gr.Error(f"Batch processing failed: {str(e)}")

def get_stats():
    if not prediction_log:
        return "No predictions yet", None
    
    preds = [e["prediction"] for e in prediction_log]
    
    fig, axes = plt.subplots(1, 2, figsize=(10, 4))
    
    axes[0].hist(preds, bins=20, edgecolor="black", color="steelblue")
    axes[0].set_title("Prediction Distribution")
    axes[0].set_xlabel("Prediction Score")
    
    times = [e["timestamp"][:16] for e in prediction_log[-20:]]
    axes[1].plot(range(len(preds[-20:])), preds[-20:], marker="o")
    axes[1].set_title("Recent Predictions")
    axes[1].set_xlabel("Request #")
    axes[1].set_ylabel("Score")
    
    plt.tight_layout()
    
    stats_text = f"""
    **Total Predictions:** {len(preds)}  
    **Average Score:** {np.mean(preds):.4f}  
    **Max Score:** {max(preds):.4f}  
    **Min Score:** {min(preds):.4f}  
    """
    return stats_text, fig

with gr.Blocks(title="ML Dashboard", theme=gr.themes.Monochrome()) as demo:
    gr.Markdown("# 📊 ML Model Dashboard")
    
    with gr.Tabs():
        with gr.Tab("🎯 Single Prediction"):
            with gr.Row():
                f1 = gr.Number(label="Feature 1", value=0.5)
                f2 = gr.Number(label="Feature 2", value=0.5)
                f3 = gr.Number(label="Feature 3", value=0.5)
            
            predict_btn = gr.Button("Predict", variant="primary")
            
            with gr.Row():
                pred_out = gr.Textbox(label="Prediction Score")
                conf_out = gr.Textbox(label="Confidence")
            
            predict_btn.click(single_predict, [f1, f2, f3], [pred_out, conf_out])
        
        with gr.Tab("📁 Batch Processing"):
            file_in = gr.File(label="Upload CSV", file_types=[".csv"])
            batch_btn = gr.Button("Process Batch")
            df_out = gr.Dataframe(label="Preview (first 10 rows)")
            file_out = gr.File(label="Download Results")
            
            batch_btn.click(batch_predict, file_in, [df_out, file_out])
        
        with gr.Tab("📈 Analytics"):
            refresh_btn = gr.Button("Refresh Stats")
            stats_md = gr.Markdown()
            stats_plot = gr.Plot()
            
            refresh_btn.click(get_stats, outputs=[stats_md, stats_plot])
            demo.load(get_stats, outputs=[stats_md, stats_plot])

demo.queue(max_size=10)
demo.launch(
    server_name="0.0.0.0",
    server_port=int(os.getenv("PORT", 7860)),
    auth=("admin", os.getenv("ADMIN_PASSWORD", "changeme")),
    show_error=True
)
```

---

## 🎓 Quick Reference Cheatsheet

```python
# ============ IMPORTS ============
import gradio as gr

# ============ QUICK INTERFACE ============
gr.Interface(fn, inputs, outputs).launch()

# ============ COMPONENT SHORTCUTS ============
# String shortcuts: "text", "image", "audio", "video", "file", "number"

# ============ COMMON COMPONENTS ============
gr.Textbox(label="", placeholder="", lines=1, value="")
gr.Image(type="numpy")          # "numpy", "pil", "filepath"
gr.Audio(type="numpy")          # (sample_rate, array)
gr.Slider(min, max, value, step)
gr.Dropdown(choices=[], value="")
gr.Radio(choices=[], value="")
gr.Checkbox(value=False)
gr.Dataframe(value=df)
gr.File(file_types=[".csv"])
gr.Button("Click", variant="primary")
gr.Markdown("# Header")
gr.Label(value={})              # Classification output
gr.Gallery(value=[])            # Image grid
gr.Plot(value=fig)              # Matplotlib/Plotly

# ============ BLOCKS ============
with gr.Blocks() as demo:
    with gr.Row():
        pass
    with gr.Column(scale=1):
        pass
    with gr.Tab("Name"):
        pass
    with gr.Accordion("Title", open=False):
        pass

# ============ EVENTS ============
btn.click(fn, inputs=[...], outputs=[...])
box.change(fn, inputs, outputs)
box.submit(fn, inputs, outputs)
event1 = btn.click(fn1, ...)
event1.then(fn2, ...)

# ============ STATE ============
state = gr.State(default_value)

# ============ UPDATES ============
gr.update(value=x, visible=True, interactive=False)

# ============ ERRORS ============
raise gr.Error("message")
raise gr.Warning("message")
raise gr.Info("message")

# ============ LAUNCH ============
demo.queue()
demo.launch(
    server_name="0.0.0.0",
    server_port=7860,
    share=False,
    auth=("user", "pass"),
    debug=True
)
```

---

## 📚 Resources

| Resource | Link |
|---|---|
| Official Docs | https://www.gradio.app/docs |
| GitHub | https://github.com/gradio-app/gradio |
| HF Spaces | https://huggingface.co/spaces |
| Theme Gallery | https://huggingface.co/spaces/gradio/theme-gallery |
| Gradio Client | https://www.gradio.app/docs/python-client |
| Discord | https://discord.gg/feTf9x3ZSB |
| Cookbook / Guides | https://www.gradio.app/guides |

---

*Last updated: 2025 | Gradio 4.x+ | Python 3.8+*
