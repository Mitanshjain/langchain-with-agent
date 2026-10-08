# 🤖 Gemini Agent Bot (LangChain + Streamlit)

A small beginner project that builds an **AI Agent** using **LangChain** and **Google Gemini**, with a simple web interface made in **Streamlit**.

You type a question → the agent decides whether it can answer by itself or needs a **tool** (a calculator or a word counter) → you get the answer on the page.

---

## 📚 Table of Contents

1. [What does this project do?](#-what-does-this-project-do)
2. [What is LangChain? (Beginner explanation)](#-what-is-langchain-beginner-explanation)
3. [Key LangChain words you will see in the code](#-key-langchain-words-you-will-see-in-the-code)
4. [Project structure](#-project-structure)
5. [Requirements explained](#-requirements-explained)
6. [Setup step by step](#-setup-step-by-step)
7. [How to run](#-how-to-run)
8. [How the code works (line by line idea)](#-how-the-code-works)
9. [Example questions to try](#-example-questions-to-try)
10. [What happens behind the scenes?](#-what-happens-behind-the-scenes)
11. [Common errors and fixes](#-common-errors-and-fixes)
12. [Security notes](#-security-notes)
13. [Ideas to improve the project](#-ideas-to-improve-the-project)

---

## 🎯 What does this project do?

This app is a chatbot with **extra abilities**:

| You ask | What the agent does |
|---|---|
| `What is python` | Answers from the AI's own knowledge (no tool needed) |
| `Explain machine learning` | Answers from the AI's own knowledge |
| `45 * 8 + 10` | Uses the **calculator** tool |
| `Count words in: Generative AI is transforming the world` | Uses the **word_counter** tool |

A normal chatbot can only *talk*. An **agent** can *talk and also take actions* by using tools.

---

## 🧠 What is LangChain? (Beginner explanation)

### The problem
Large Language Models (LLMs) like Gemini, ChatGPT or Claude are great at language, but on their own they:

- can make mistakes in exact maths,
- cannot run your Python code,
- cannot look at your files or databases,
- do not remember anything between calls.

### The solution
**LangChain is a Python library (a toolbox) that helps you connect an LLM to other things** — prompts, tools, memory, documents, databases, APIs — so you can build real applications, not just a chat box.

Think of it like this:

> 🧑‍🍳 The **LLM** is a very smart chef.
> 🧰 **LangChain** is the kitchen: it gives the chef knives (tools), a recipe card (prompt), and an assistant who coordinates everything (agent).

### Why use it?
- You write less code to talk to different LLMs (Gemini, OpenAI, Claude…). Switching model is often a one-line change.
- It gives you ready-made building blocks (prompts, tools, agents, memory, RAG, etc.).
- It is the most popular framework for learning how AI apps are built.

---

## 🧩 Key LangChain words you will see in the code

| Word | Simple meaning | Where in this project |
|---|---|---|
| **LLM / Chat model** | The AI brain (here: Gemini) | `ChatGoogleGenerativeAI(...)` |
| **Prompt** | The instructions + the user's question you send to the LLM | `ChatPromptTemplate.from_messages(...)` |
| **Tool** | A normal Python function the AI is allowed to call | `calculator`, `word_counter` in `tools.py` |
| **`@tool` decorator** | A label that turns a Python function into a LangChain tool | `@tool` above each function |
| **Agent** | The "decision maker": reads the question and decides *"Do I answer directly, or use a tool?"* | `create_tool_calling_agent(...)` |
| **AgentExecutor** | The "runner": actually runs the agent, calls the tool, sends the result back to the AI, and repeats until done | `AgentExecutor(...)` |
| **`{input}`** | Placeholder that is replaced by the user's question | In the prompt |
| **`{agent_scratchpad}`** | The agent's "notepad" where it stores tool calls and tool results while thinking | In the prompt |
| **`temperature`** | Creativity level. `0` = precise and consistent, `1` = more creative | `temperature=0` |
| **`verbose=True`** | Prints the agent's thinking steps in your terminal (great for learning!) | `AgentExecutor(verbose=True)` |

---

## 📁 Project structure

```
your-project/
│
├── app.py               # Main file: LLM + prompt + agent + Streamlit UI
├── tools.py             # Tools the agent can use (calculator, word counter)
├── requirements.txt     # List of Python packages to install
├── .env                 # Your secret API key (never share this!)
├── question.txt         # Sample questions to test the agent
└── README.md            # This file
```

---

## 📦 Requirements explained

This is your `requirements.txt`:

```
streamlit
python-dotenv
google-generativeai
langchain
langchain-core
langchain-community
langchain-google-genai
```

Here is what each package does:

| Package | What it does | Used in this project? |
|---|---|---|
| `streamlit` | Creates the web page (text box, button, title) using only Python | ✅ Yes – the UI |
| `python-dotenv` | Reads secrets from the `.env` file so you don't hard-code your API key | ✅ Yes – `load_dotenv()` |
| `google-generativeai` | Google's own Python SDK for Gemini | ⚠️ Optional – LangChain's Google package handles the connection already |
| `langchain` | The main LangChain library (includes the `@tool` decorator) | ✅ Yes |
| `langchain-core` | Core building blocks: prompts, messages, base classes | ✅ Yes – `ChatPromptTemplate` |
| `langchain-community` | Extra integrations contributed by the community | ⚠️ Not used right now, but harmless |
| `langchain-google-genai` | Connects LangChain to Gemini (`ChatGoogleGenerativeAI`) | ✅ Yes – the AI brain |

### ⚠️ Important: one package is missing!

Your `app.py` has this line:

```python
from langchain_classic.agents import AgentExecutor, create_tool_calling_agent
```

In newer LangChain versions (1.x), the older `AgentExecutor` style of agents was moved into a separate package called **`langchain-classic`**. So you must add it, otherwise you will get `ModuleNotFoundError: No module named 'langchain_classic'`.

### ✅ Recommended `requirements.txt`

```
streamlit
python-dotenv
langchain
langchain-core
langchain-classic
langchain-community
langchain-google-genai
```

(`google-generativeai` was removed because `langchain-google-genai` already installs what it needs. Keeping it will not break anything though.)

---

## 🛠️ Setup step by step

### Step 1 – Install Python
You need **Python 3.10 or newer**. Check with:

```bash
python --version
```

### Step 2 – Create a virtual environment (recommended)
A virtual environment keeps this project's packages separate from other projects.

```bash
# Create it
python -m venv venv

# Activate it
# Windows:
venv\Scripts\activate
# Mac / Linux:
source venv/bin/activate
```

You will see `(venv)` at the start of your terminal line when it is active.

### Step 3 – Install the packages

```bash
pip install -r requirements.txt
```

### Step 4 – Get a free Gemini API key
1. Go to **Google AI Studio**: https://aistudio.google.com/apikey
2. Sign in with your Google account.
3. Click **Create API key** and copy it.

### Step 5 – Put the key in the `.env` file
Open `.env` and paste your key (no spaces around `=`):

```
GOOGLE_API_KEY="paste_your_key_here"
```

---

## ▶️ How to run

In your terminal (with the virtual environment active), inside the project folder:

```bash
streamlit run app.py
```

Your browser will open automatically at `http://localhost:8501`.

1. Type a question in the box.
2. Click **Ask Agent**.
3. See the answer under **Response**.
4. Look at your **terminal** too — because of `verbose=True` you can see the agent thinking and calling tools.

To stop the app, press `Ctrl + C` in the terminal.

---

## 🔍 How the code works

### `tools.py` – the abilities

```python
from langchain.tools import tool

@tool
def calculator(expression: str) -> str:
    """Solve mathematical expressions"""
    try:
        return str(eval(expression))
    except:
        return "Invalid expression"
```

- `@tool` turns the function into something the AI can call.
- **The docstring** (`"""Solve mathematical expressions"""`) is very important! The AI reads it to understand *what the tool does and when to use it*. A bad description = a confused agent.
- `eval(expression)` calculates the maths, e.g. `"45 * 8 + 10"` → `370`.

```python
@tool
def word_counter(text: str) -> str:
    """Counts words in a sentence"""
    return f"Total words: {len(text.split())}"
```

- `text.split()` cuts the sentence into words; `len()` counts them.

### `app.py` – the main brain

**1. Load the API key**
```python
from dotenv import load_dotenv
load_dotenv()
GOOGLE_API_KEY = os.getenv("GOOGLE_API_KEY")
```
Reads `GOOGLE_API_KEY` from your `.env` file.

**2. Create the LLM (Gemini)**
```python
llm = ChatGoogleGenerativeAI(
    model="gemini-3.5-flash",
    google_api_key=GOOGLE_API_KEY,
    temperature=0
)
```
This is the AI brain. `temperature=0` makes answers consistent. If you get a "model not found" error, check the current list of model names in Google AI Studio and replace the `model=` value.

**3. List the tools**
```python
tools = [calculator, word_counter]
```

**4. Write the prompt**
```python
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful AI assistant.\n ... use tools only when necessary ..."),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}")
])
```
- `system` = the rules you give the AI (its personality and instructions).
- `human` = the user's question (`{input}` gets replaced).
- `placeholder {agent_scratchpad}` = space where LangChain stores the tool calls and results. **Do not remove it** — the agent needs it.

**5. Create the agent and the executor**
```python
agent = create_tool_calling_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)
```
- `create_tool_calling_agent` = combines LLM + tools + prompt into an agent that can request tool calls.
- `AgentExecutor` = the loop that runs it.

**6. A helper function**
```python
def my_output(query):
    result = agent_executor.invoke({"input": query})
    return result["output"]
```
Sends the question in, returns only the final text answer.

**7. The Streamlit UI**
```python
st.title("🤖 Gemini Agent Bot")
query = st.text_input("Ask something")
if st.button("Ask Agent"):
    response = my_output(query)
    st.subheader("Response")
    st.write(response)
```
A title, a text box, a button, and a place to show the answer.

---

## 💬 Example questions to try

These are from your `question.txt`:

| Question | Expected behaviour |
|---|---|
| `Count words in: Generative AI is transforming the world` | Uses `word_counter` → **Total words: 7** |
| `45 * 8 + 10` | Uses `calculator` → **370** |
| `Explain machine learning` | Answers directly, no tool |
| `What is python` | Answers directly, no tool |

> 💡 Tip: For the calculator, phrasing like `Calculate 45 * 8 + 10` often works even more reliably than just the bare expression.

---

## 🎬 What happens behind the scenes?

Example: you ask **`Calculate 45 * 8 + 10`**

```
You ──► Streamlit ──► AgentExecutor ──► Gemini
                                          │
                       "This needs maths. I'll call calculator('45 * 8 + 10')"
                                          │
                       AgentExecutor runs your Python function ──► 370
                                          │
                       Result goes back to Gemini
                                          │
                       Gemini: "The answer is 370."
                                          │
You ◄── Streamlit ◄───────────────────────┘
```

Example: you ask **`What is python`**

```
You ──► Gemini decides: "I know this, no tool needed" ──► Answer
```

That decision-making is exactly what makes it an **agent**.

---

## 🧯 Common errors and fixes

| Error | Cause | Fix |
|---|---|---|
| `ModuleNotFoundError: No module named 'langchain_classic'` | Package missing | `pip install langchain-classic` |
| `ModuleNotFoundError: No module named 'dotenv'` | Package not installed | `pip install python-dotenv` |
| `DefaultCredentialsError` / API key error / `403` | Key empty or wrong | Check `.env` has the correct `GOOGLE_API_KEY`; restart the app |
| `404 model not found` | Model name is wrong or not available to your key | Change `model=` in `app.py` to a model listed in Google AI Studio |
| `429 quota exceeded` | Free tier limit reached | Wait a bit, or check your usage/billing in AI Studio |
| `streamlit: command not found` | Virtual environment not active or Streamlit not installed | Activate `venv`, then `pip install streamlit` |
| `.env` is not being read | File is in the wrong folder or named wrongly | Keep `.env` in the **same folder** as `app.py`; the name must be exactly `.env` |
| Empty question returns an error | Button pressed with empty text box | Add `if query:` check (see ideas below) |

---

## 🔐 Security notes

1. **Never upload your `.env` file to GitHub.** Create a `.gitignore` file containing:
   ```
   .env
   venv/
   __pycache__/
   ```
   If you ever leaked your key, delete it in Google AI Studio and create a new one.

2. **`eval()` is dangerous.** In `tools.py`, `eval()` runs *any* Python code the AI (or a user) sends it. That is fine for learning on your own computer, but never use it in a public app. A safer option is a library like `numexpr` or `sympy`, or a small custom parser that only allows numbers and `+ - * /`.

---

## 🚀 Ideas to improve the project

Once you understand the basics, try these in order:

1. **Handle empty input**
   ```python
   if st.button("Ask Agent"):
       if query.strip():
           response = my_output(query)
           st.subheader("Response")
           st.write(response)
       else:
           st.warning("Please type a question first.")
   ```
2. **Show a spinner** while waiting: `with st.spinner("Thinking..."):`
3. **Add a new tool**, e.g. `get_current_time`, `reverse_text`, or a unit converter. Remember: write a clear docstring!
4. **Add chat memory** so the agent remembers earlier messages.
5. **Make a real chat UI** using `st.chat_input` and `st.chat_message`.
6. **Add web search or file reading tools.**
7. **Learn RAG** (Retrieval-Augmented Generation) — letting the AI answer from your own PDFs. This is the next big LangChain topic.

---

## 📖 Helpful learning links

- LangChain docs: https://python.langchain.com
- Streamlit docs: https://docs.streamlit.io
- Google AI Studio (API keys & models): https://aistudio.google.com

---

Happy learning! 🎉 If something breaks, read the error message from bottom to top — the last lines usually tell you exactly what is wrong.