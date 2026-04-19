# 🚀 AI DevOps Agent – Day 1 (Local AWS CLI Integration)

## 📌 Overview

This project is a **local AI-powered DevOps assistant** that interacts with your AWS account using **AWS CLI (not CloudShell)**.

It uses:

* AWS CLI (installed locally)
* Python virtual environment
* LangChain
* Google Gemini (LLM)
* Custom tools to execute AWS commands

---

## 🧠 What It Does

You can ask:

* "List S3 buckets"
* "Show EC2 instances"
* "Get IAM users"

And the AI agent will:

1. Understand your query
2. Run AWS CLI commands locally
3. Return clean output

---

## 🏗️ Architecture

User Input → LLM (Gemini) → LangChain Agent → AWS CLI → Output

---

## ⚙️ Prerequisites

* Ubuntu/Linux system
* Python 3.10+
* AWS CLI installed
* AWS credentials configured
* Google Gemini API key

---

## 🛠️ Setup Steps

### 1️⃣ Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Verify:

```bash
aws --version
```

---

### 2️⃣ Configure AWS Credentials

```bash
aws configure
```

Enter:

* Access Key
* Secret Key
* Region (e.g., ap-south-1)
* Output format (json)

Test:

```bash
aws s3 ls
```

---

### 3️⃣ Create Project Directory

```bash
mkdir ai-devops-agent
cd ai-devops-agent
```

---

### 4️⃣ Setup Python Virtual Environment

```bash
sudo apt install python3-venv -y
python3 -m venv venv
source venv/bin/activate
```

---

### 5️⃣ Install Dependencies

```bash
pip install langchain langchain-google-genai python-dotenv
```

---

### 6️⃣ Add Environment Variables

Create `.env` file:

```bash
nano .env
```

Add:

```
GOOGLE_API_KEY=your_gemini_api_key
```

---

## 📂 Project Structure

```
ai-devops-agent/
│── venv/
│── tools.py
│── agent.py
│── .env
│── README.md
```

---

## 🔧 tools.py

```python
import subprocess
from langchain.tools import tool


def run_aws(cmd):
    result = subprocess.run(cmd, capture_output=True, text=True)

    def clean(text):
        if not text:
            return ""
        return "\n".join([
            line for line in text.splitlines()
            if "epoll" not in line
        ]).strip()

    return clean(result.stdout) or clean(result.stderr)

@tool
def get_s3_buckets(_: str = "list") -> str:
    """List all S3 buckets"""
    return run_aws(["aws", "s3", "ls"])
```

---

## 🤖 agent.py

```python
from dotenv import load_dotenv
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain.agents import create_react_agent
from langchain.agents.agent import AgentExecutor
from langchain import hub
from tools import get_s3_buckets

load_dotenv()

tools = [get_s3_buckets]

llm = ChatGoogleGenerativeAI(
    model="gemini-2.5-flash",
    temperature=0
)

prompt = hub.pull("hwchase17/react")

agent = create_react_agent(llm, tools, prompt)

agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True
)

result = agent_executor.invoke({
    "input": "List my S3 buckets"
})

print(result["output"])
```

---

## ▶️ Run the Project

```bash
python3 agent.py
```

---

## ✅ Expected Output

```
2026-04-20  my-bucket-1
2026-04-21  logs-bucket
```

---

## 🔥 Key Features

* Local AWS CLI execution (secure)
* No dependency on AWS CloudShell
* AI-powered command execution
* Clean output filtering

---

## 🚀 Future Enhancements (Day 2+)

* Add EC2, VPC, IAM tools
* Build CLI chatbot interface
* Integrate with Jenkins pipeline
* Deploy as web app (FastAPI)

---

## ⚠️ Notes

* Ensure AWS CLI is configured properly
* Use IAM roles instead of root credentials (best practice)
* Keep `.env` file secure

---

## 👨‍💻 Author

Anurag Patil – DevOps Engineer
