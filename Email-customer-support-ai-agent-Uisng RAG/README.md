# 📧 AI Email Support Agent using RAG

An AI-powered email support automation system that reads customer emails from **Gmail**, retrieves relevant information from a knowledge base using **Retrieval-Augmented Generation (RAG)**, generates an AI-based response, and automatically sends the reply back to the customer.

The project combines **Python, LangChain, OpenAI, FAISS, Gmail API, and RAG** to create an automated customer support workflow.

---

## 🚀 Project Overview

Customer support teams often receive a large number of repetitive questions through email. Manually reading every email, finding the relevant information, preparing a response, and replying can be time-consuming.

This project automates that process.

The system:

1. Connects to a Gmail account.
2. Retrieves unread emails from the inbox.
3. Extracts the sender, subject, and customer question.
4. Uses a knowledge-base PDF as the source of information.
5. Loads and splits the PDF into smaller chunks.
6. Converts the chunks into vector embeddings.
7. Stores the embeddings in a FAISS vector database.
8. Retrieves the most relevant information for the customer's question.
9. Sends the retrieved context and question to an OpenAI language model.
10. Generates an answer based only on the retrieved context.
11. Extracts the customer's email address.
12. Sends the generated response back through Gmail.

---

## 🧠 What is RAG?

**Retrieval-Augmented Generation (RAG)** is a technique that allows an AI model to retrieve relevant information from an external knowledge source before generating an answer.

Instead of relying only on the information stored in the language model, the system first searches the knowledge base and provides relevant information as context to the LLM.

### RAG Flow

```text
Knowledge Base PDF
       ↓
   PDF Loader
       ↓
     Chunking
       ↓
    Embeddings
       ↓
   FAISS Vector Store
       ↓
     Retriever
       ↓
Customer Question
       ↓
Relevant Context
       ↓
   Prompt Template
       ↓
    OpenAI LLM
       ↓
   AI Generated Answer
```

---

# 🔄 Complete Workflow

```text
                    ┌─────────────────────┐
                    │      Gmail Inbox    │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Find Unread Emails  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Extract Email Data  │
                    │ Sender / Subject /  │
                    │ Customer Question   │
                    └──────────┬──────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │ Customer Question  │
                     └─────────┬──────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │    Retriever       │
                     └─────────┬──────────┘
                               │
                               ▼
                  ┌──────────────────────────┐
                  │ FAISS Vector Database    │
                  └────────────┬─────────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │ Relevant Context   │
                     └─────────┬──────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │   OpenAI LLM       │
                     └─────────┬──────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │ Generated Answer   │
                     └─────────┬──────────┘
                               │
                               ▼
                     ┌────────────────────┐
                     │ Gmail Reply        │
                     └────────────────────┘
```

---

# ✨ Features

* 📩 Automatically reads unread Gmail messages
* 🔎 Extracts sender and subject information
* 📝 Extracts the customer's question from the email body
* 📚 Uses a PDF as the knowledge base
* ✂️ Splits documents into smaller chunks
* 🧮 Generates text embeddings
* 🗃️ Stores embeddings in FAISS
* 🔍 Performs similarity-based retrieval
* 🤖 Uses an OpenAI LLM to generate answers
* 🎯 Grounds answers using retrieved context
* 📧 Automatically sends replies through Gmail
* 🔐 Uses Gmail OAuth authentication
* 🛑 Instructs the LLM to respond that it does not know when the answer is unavailable in the provided context

---

# 🛠️ Technologies Used

| Technology                     | Purpose                                     |
| ------------------------------ | ------------------------------------------- |
| Python                         | Main programming language                   |
| LangChain                      | RAG application framework                   |
| PyPDF                          | Loading PDF documents                       |
| RecursiveCharacterTextSplitter | Splitting documents into chunks             |
| OpenAI Embeddings              | Converting text into vector representations |
| FAISS                          | Vector database / similarity search         |
| OpenAI Chat Model              | Generating AI responses                     |
| Gmail API                      | Reading and sending emails                  |
| Google OAuth                   | Gmail authentication                        |
| Jupyter Notebook               | Development environment                     |

---

# 📂 Project Structure

```text
AI-Email-Support-Agent/
│
├── Email Support.ipynb
├── README.md
├── secret_key.py
├── client_secret.json
├── token.json
└── knowledge-base/
    └── knowledge.pdf
```

> `client_secret.json`, `token.json`, and API keys should **not** be uploaded to GitHub.

---

# ⚙️ Installation

## 1. Clone the Repository

```bash
git clone https://github.com/your-username/AI-Email-Support-Agent.git
```

Move into the project directory:

```bash
cd AI-Email-Support-Agent
```

---

## 2. Install Required Libraries

Install the required Python packages:

```bash
pip install langchain
pip install pypdf
pip install langchain-text-splitters
pip install langchain-community
pip install langchain-openai
pip install faiss-cpu
pip install google-api-python-client
pip install google-auth-httplib2
pip install google-auth-oauthlib
```

You can also install them together:

```bash
pip install langchain pypdf langchain-text-splitters langchain-community langchain-openai faiss-cpu google-api-python-client google-auth-httplib2 google-auth-oauthlib
```

---

# 🔑 API Configuration

## OpenAI API Key

The project uses an OpenAI API key for generating embeddings and AI responses.

Create a file named:

```text
secret_key.py
```

Example:

```python
api_key = "YOUR_OPENAI_API_KEY"
```

**Never upload your real API key to GitHub.**

Add the following to `.gitignore`:

```text
secret_key.py
token.json
client_secret*.json
.env
```

---

# 📧 Gmail API Setup

The project uses the Gmail API to read unread emails and send AI-generated replies.

The required Gmail scopes are:

```text
https://www.googleapis.com/auth/gmail.modify
https://www.googleapis.com/auth/gmail.send
```

The application uses Google's OAuth authentication flow to authorize Gmail access.

On the first run, Google authentication is required. After successful authentication, credentials can be stored locally in `token.json`.

---

# 📚 Knowledge Base

The project uses a PDF document as the knowledge source.

The PDF is loaded using:

```python
from langchain_community.document_loaders import PyPDFLoader

loader = PyPDFLoader("knowledge.pdf")
data = loader.load()
```

The document is then split into smaller chunks using `RecursiveCharacterTextSplitter`.

The notebook uses:

```python
RecursiveCharacterTextSplitter(
    separators=["\n\n", "\n", " "],
    chunk_size=500,
    chunk_overlap=0
)
```

The provided notebook loaded a PDF and produced **3 pages/documents**, which were split into **14 chunks** using these settings.

---

# 🧮 Embeddings

After chunking, the text chunks are converted into vector representations using OpenAI embeddings.

The project uses:

```python
OpenAIEmbeddings(
    api_key=api_key,
    model="text-embedding-3-small"
)
```

Embeddings allow the system to represent text numerically so that semantically relevant information can be retrieved from the vector store.

---

# 🗃️ FAISS Vector Store

The generated embeddings are stored in a FAISS vector store.

```python
from langchain_community.vectorstores import FAISS

vectorstore = FAISS.from_documents(
    chunk,
    embeddings
)
```

FAISS is then converted into a retriever:

```python
retriever = vectorstore.as_retriever(
    search_kwargs={"k": 3}
)
```

The retriever searches for the top relevant document chunks for the customer's question.

---

# 🤖 AI Response Generation

The project uses an OpenAI chat model to generate the response.

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    api_key=api_key,
    temperature=0.7
)
```

A prompt template is used to make sure the AI answers using the retrieved context.

The prompt follows this approach:

```text
Answer the question using only the context below.

Context:
{context}

Question:
{question}

If the answer is not available in the context,
say "I don't know based on the provided articles."
```

This helps reduce unsupported answers by instructing the model to rely on the retrieved context.

---

# 📩 Gmail Email Processing

The Gmail API is used to search the inbox for unread emails.

The project uses:

```python
results = service.users().messages().list(
    userId="me",
    labelIds=["INBOX"],
    q="is:unread"
).execute()
```

The notebook successfully detected unread emails during execution.

The system then retrieves the email using its message ID and extracts:

* Sender
* Subject
* Email body
* Customer question

---

# 🔍 Email Body Extraction

The project includes a function to extract the email body from Gmail's message payload.

It handles:

* Simple email bodies
* Multipart emails
* `text/plain` email parts
* Base64 URL-safe encoded email content

The decoded content is then used as the customer's question.

---

# 🔎 Retrieval Process

Once the customer's question is extracted, the retriever searches the FAISS vector database.

```python
docs = retriever.invoke(question)

context = "\n\n".join(
    doc.page_content for doc in docs
)
```

The retrieved document contents are combined into the `context` variable and passed to the prompt.

---

# 🧠 Answer Generation

The question and retrieved context are passed to the prompt:

```python
message = prompt.invoke({
    "question": question,
    "context": context
})
```

The OpenAI model then generates the answer:

```python
answer = llm.invoke(message)
```

The generated response is available through:

```python
answer.content
```

---

# 📤 Automatic Email Reply

After generating the AI answer, the customer's email address is extracted from the sender information.

The project uses a regular expression to identify the email address.

```python
match = re.search(
    r'[\w\.-]+@[\w\.-]+\.\w+',
    sender
)
```

The generated response is then converted into a Gmail-compatible message and sent using the Gmail API.

```python
reply = MIMEText(answer.content)

reply["To"] = customer_email
reply["Subject"] = "Re: " + subject
```

The message is encoded and sent through:

```python
service.users().messages().send(
    userId="me",
    body=body
).execute()
```

The notebook successfully produced the output:

```text
Reply sent successfully!
```

---

# 🧪 Example

### Customer Email

```text
From: customer@example.com

Subject: Requesting to answer

Question:
Who is Luffy?
```

### System Process

```text
Customer Email
      ↓
Extract Question
      ↓
"Who is Luffy?"
      ↓
FAISS Similarity Search
      ↓
Retrieve Relevant Context
      ↓
Send Context + Question to LLM
      ↓
Generate Answer
      ↓
Send Gmail Reply
```

### AI Response

```text
Luffy is the captain of the Straw Hat Pirates.
```

## The notebook demonstrates this complete flow using a One Piece PDF as the knowledge source.

# 🎯 Key Concepts Demonstrated

This project demonstrates practical understanding of:

* Generative AI
* Large Language Models
* Retrieval-Augmented Generation
* Document loading
* Document chunking
* Text embeddings
* Vector databases
* Similarity search
* Information retrieval
* Prompt engineering
* Gmail API
* OAuth authentication
* Email processing
* Automated email responses
* LangChain

---

# 💡 Why RAG is Used

A language model by itself may not have access to the specific information required to answer a customer's question.

RAG solves this by providing the model with relevant information from an external knowledge base.

For example:

```text
Customer Question
       ↓
Search Knowledge Base
       ↓
Retrieve Relevant Information
       ↓
Give Information to LLM
       ↓
Generate Grounded Answer
```

This makes the system more suitable for knowledge-base-based customer support.

---

# 🔐 Security

Do not commit sensitive credentials to GitHub.

The following files should be excluded:

```text
secret_key.py
token.json
client_secret.json
.env
```

Recommended `.gitignore`:

```text
# API Keys
secret_key.py
.env

# Google OAuth
token.json
client_secret*.json

# Python
__pycache__/
*.pyc

# Jupyter
.ipynb_checkpoints/
```

If an API key or credential has accidentally been uploaded to GitHub, revoke or rotate it immediately.

---

# ⚠️ Important Notes

* The project requires an OpenAI API key.
* Gmail API access requires Google OAuth configuration.
* The knowledge-base PDF must be available locally.
* The Gmail account must authorize the required Gmail API scopes.
* The current notebook is implemented as a Jupyter Notebook.
* The example knowledge base used during development is a One Piece PDF.
* The knowledge base can be replaced with a customer-support document containing product information, FAQs, policies, documentation, or other relevant content.

---

# 🚀 Future Improvements

The current notebook provides the core RAG + Gmail automation workflow. Possible improvements include:

### 1. Multiple Knowledge Sources

Support:

* Multiple PDFs
* Websites
* FAQs
* Product documentation
* Company databases

### 2. Better Email Classification

Automatically classify emails into categories such as:

```text
Billing
Technical Support
Product Information
Order Status
Refund
General Query
```

### 3. Conversation Memory

Store previous customer conversations so that the AI can understand follow-up questions.

### 4. Human Handoff

If the AI cannot confidently answer a question, forward the email to a human support agent.

### 5. Email Priority

Automatically identify urgent customer emails and prioritize them.

### 6. Production Deployment

Convert the notebook into a backend service using technologies such as:

```text
FastAPI
REST API
Docker
Cloud Deployment
```

### 7. Monitoring

Add logging and monitoring to track:

* Number of emails processed
* Successful responses
* Failed responses
* Response time
* Frequently asked questions

---

# 📈 Learning Outcomes

Through this project, I gained practical experience in building a **Retrieval-Augmented Generation application** using Python and LangChain.

I learned how to:

* Load documents using PyPDF
* Split documents into meaningful chunks
* Generate embeddings
* Store embeddings in FAISS
* Retrieve relevant context
* Build prompts for an LLM
* Generate answers using OpenAI
* Connect Python applications with Gmail API
* Read and process emails
* Extract customer information
* Automatically send AI-generated email responses

---

# 👨‍💻 Project Type

**Generative AI / RAG / AI Automation Project**

### Primary Technologies

```text
Python
LangChain
OpenAI
FAISS
Gmail API
Google OAuth
PyPDF
Jupyter Notebook
```

---

# ⭐ Project Highlights

* Built an end-to-end **AI Email Support Agent**
* Implemented **Retrieval-Augmented Generation (RAG)**
* Integrated **FAISS vector search**
* Used **OpenAI embeddings and LLM**
* Integrated **Gmail API for automated email processing**
* Implemented **automatic AI-generated email replies**
* Used external knowledge instead of relying only on the LLM

---

# 📄 Repository Contents

```text
Email Support.ipynb
README.md
```

Additional credential/configuration files should remain local and should not be committed to the repository.

---

# 📜 License

This project is intended for educational and portfolio purposes.

You may modify and extend the project for learning and development.

---

# 🙌 Acknowledgements

This project was built using open-source libraries and APIs including:

* LangChain
* OpenAI
* FAISS
* Google Gmail API
* PyPDF

---

## ⭐ If you find this project useful

Feel free to explore the code, improve the workflow, and build your own AI-powered customer support automation system.

