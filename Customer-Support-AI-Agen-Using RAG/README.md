# 📚 PDF Question Answering using RAG, OpenAI & FAISS

A **Retrieval-Augmented Generation (RAG)** project that allows users to ask questions about the content of a PDF document.

The system extracts text from a PDF, divides the text into smaller chunks, converts the chunks into numerical vector representations using OpenAI embeddings, stores them in a FAISS vector index, retrieves the most relevant chunks for a user question, and finally uses an OpenAI language model to generate an answer based on the retrieved context.

---

## 🚀 Project Overview

Traditional Large Language Models may not have access to the information contained in a user's private PDF documents.

This project solves that problem using **Retrieval-Augmented Generation (RAG)**.

The workflow is:

```text
PDF Document
     ↓
Text Extraction
     ↓
Text Chunking
     ↓
OpenAI Embeddings
     ↓
FAISS Vector Index
     ↓
User Question
     ↓
Question Embedding
     ↓
Similarity Search
     ↓
Relevant Chunks
     ↓
GPT-4o-mini
     ↓
Generated Answer
```

---

## ✨ Features

* 📄 Read and extract text from PDF files
* ✂️ Divide large PDF text into smaller chunks
* 🧠 Generate embeddings using OpenAI
* 🔎 Perform similarity search using FAISS
* 📌 Retrieve the most relevant text chunks
* 🤖 Generate answers using GPT-4o-mini
* 💬 Ask questions interactively from the terminal/Jupyter Notebook
* 🐍 Implemented completely in Python

---

## 🛠️ Technologies Used

| Technology        | Purpose                              |
| ----------------- | ------------------------------------ |
| Python            | Main programming language            |
| PyPDF2            | Extract text from PDF                |
| NumPy             | Numerical array operations           |
| FAISS             | Vector storage and similarity search |
| OpenAI Embeddings | Convert text into vectors            |
| GPT-4o-mini       | Generate natural-language answers    |
| Jupyter Notebook  | Development and experimentation      |

---

## 📂 Project Structure

```text
Gen-RAG-Project/
│
├── Gen RAG Project.ipynb
├── Onepiece.pdf
├── README.md
└── requirements.txt
```

> `Onepiece.pdf` is the example document used in the notebook.

---

## 🔄 How the RAG Pipeline Works

### 1. PDF Loading

The project uses `PyPDF2` to read the PDF document.

```python
reader = PyPDF2.PdfReader(path)

for page in reader.pages:
    page_text = page.extract_text()
```

The extracted text from all available pages is combined into a single text string.

---

### 2. Text Chunking

Large documents cannot always be processed efficiently as one large piece of text.

The extracted text is divided into chunks of approximately **500 characters**.

```python
def make_chunks(text, chunk_size=500):
    chunks = []
    start = 0

    while start < len(text):
        end = start + chunk_size
        chunk = text[start:end]
        chunks.append(chunk)
        start = end

    return chunks
```

Chunking makes it possible to search for specific sections of the document instead of processing the entire document for every question.

---

### 3. Creating Embeddings

Each text chunk is converted into a numerical vector using the OpenAI embedding model:

```text
text-embedding-3-small
```

The embedding represents the semantic meaning of the text in vector form.

```python
response = client.embeddings.create(
    input=chunk,
    model="text-embedding-3-small"
)
```

The generated vectors are converted into NumPy arrays.

---

### 4. Creating the FAISS Vector Index

The generated embeddings are stored in a FAISS index.

The project uses:

```python
faiss.IndexFlatL2(dimensions)
```

FAISS allows the system to efficiently search for vectors that are similar to the vector representation of a user's question.

---

### 5. User Question

The user enters a question through the notebook:

```python
question = input("Ask your Question from PDF: ")
```

For example:

```text
Ask your Question from PDF: Who is the father of Luffy?
```

---

### 6. Question Embedding

The user's question is also converted into an embedding using the same OpenAI embedding model.

```python
q_embed = client.embeddings.create(
    input=question,
    model="text-embedding-3-small"
).data[0].embedding
```

This allows the question to be compared with the document chunks in the same vector space.

---

### 7. Similarity Search

FAISS searches the vector index and retrieves the **top 2 most relevant chunks**.

```python
distances, indices = index.search(q_embed, k=2)
```

The corresponding text chunks are then retrieved:

```python
best_chunks = [
    chunks[i]
    for i in indices[0]
]
```

---

### 8. Context Creation

The retrieved chunks are combined to create the context for the language model.

```python
context = "\n\n".join(chunks)
```

The final prompt contains:

```text
Context:
<retrieved document chunks>

Question:
<user question>

Answer the question using the context provided.
```

---

### 9. Answer Generation

The retrieved context and question are sent to the OpenAI chat model.

The project uses:

```text
gpt-4o-mini
```

The generated answer is then displayed to the user.

---

# 🧠 What is RAG?

**RAG stands for Retrieval-Augmented Generation.**

Instead of asking an LLM to answer a question only from its pre-trained knowledge, RAG first retrieves relevant information from an external knowledge source and provides that information to the LLM.

In this project:

```text
User Question
      ↓
Convert Question to Embedding
      ↓
Search FAISS
      ↓
Retrieve Relevant PDF Chunks
      ↓
Send Chunks + Question to LLM
      ↓
Generate Answer
```

This approach is useful for applications where the information comes from private or custom documents.

---

# 📊 Project Workflow in Detail

```text
                    ┌─────────────────┐
                    │   PDF Document  │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │  PyPDF2 Reader  │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ Extracted Text  │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │  Text Chunks    │
                    │  ~500 chars     │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ OpenAI Embedding│
                    │ text-embedding- │
                    │    3-small      │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │  FAISS Index    │
                    └────────┬────────┘
                             │
                             │
User Question ───────────────┘
       ↓
Question Embedding
       ↓
FAISS Similarity Search
       ↓
Top Relevant Chunks
       ↓
Context + Question
       ↓
GPT-4o-mini
       ↓
Final Answer
```

---

# 💻 Installation

Clone the repository:

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
```

Move into the project directory:

```bash
cd Gen-RAG-Project
```

Install the required packages:

```bash
pip install PyPDF2
pip install faiss-cpu
pip install numpy
pip install openai
```

Or install everything using:

```bash
pip install -r requirements.txt
```

---

# 📦 Requirements

Create a `requirements.txt` file containing:

```text
PyPDF2
faiss-cpu
numpy
openai
```

---

# 🔑 OpenAI API Key Setup

**Never hard-code your API key inside your Python code or commit it to GitHub.**

Instead, store it as an environment variable.

### Windows

```bash
setx OPENAI_API_KEY "your_api_key_here"
```

Restart your terminal/Jupyter environment after setting the variable.

Then use:

```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.getenv("OPENAI_API_KEY")
)
```

### Alternative: `.env`

Create:

```text
.env
```

Add:

```text
OPENAI_API_KEY=your_api_key_here
```

Add `.env` to `.gitignore`:

```text
.env
```

---

# ▶️ Running the Project

1. Open Jupyter Notebook.

2. Open:

```text
Gen RAG Project.ipynb
```

3. Make sure the PDF file is available:

```text
Onepiece.pdf
```

4. Install the required libraries.

5. Configure your OpenAI API key securely.

6. Run the notebook cells.

7. Enter a question when prompted:

```text
Ask your Question from PDF:
```

8. The system retrieves relevant information and generates an answer.

---

# 🧪 Example

### Input

```text
Ask your Question from PDF: Who is the father of Luffy?
```

### RAG Process

```text
Question
   ↓
Question Embedding
   ↓
FAISS Search
   ↓
Retrieve Relevant Chunks
   ↓
Send Context to GPT-4o-mini
```

### Output

```text
Answer from OpenAI:
The context provided does not mention Luffy's father.
However, in the broader One Piece story, Luffy's father is Monkey D. Dragon.
```

The notebook output also demonstrates that the system retrieves related text parts before generating the answer.

---

# 🧩 Main Functions

The project is divided into several Python functions.

### `read_pdf()`

Responsible for extracting text from the PDF.

```python
def read_pdf(path):
```

---

### `make_chunks()`

Divides the extracted text into smaller chunks.

```python
def make_chunks(text, chunk_size=500):
```

---

### `get_embeddings()`

Creates OpenAI embeddings for the text chunks.

```python
def get_embeddings(chunklist):
```

---

### `build_faiss_index()`

Creates the FAISS vector index.

```python
def build_faiss_index(embeddings):
```

---

### `search_chunks()`

Converts the user's question into an embedding and retrieves the most relevant chunks.

```python
def search_chunks(question, chunks, index):
```

---

### `ask_question()`

Creates the prompt and sends the retrieved context to the LLM.

```python
def ask_question(chunks, question):
```

---

### `main()`

Coordinates the complete RAG pipeline.

```text
Load PDF
   ↓
Create Chunks
   ↓
Create Embeddings
   ↓
Build FAISS Index
   ↓
Take User Question
   ↓
Retrieve Relevant Chunks
   ↓
Generate Answer
```

---

# 🎯 Use Cases

This type of RAG architecture can be extended to many real-world applications:

* 📚 Document Question Answering
* 🎓 Educational PDF Assistant
* 📄 Resume Question Answering
* 🏢 Company Policy Assistant
* 📖 Research Paper Assistant
* 🧾 Invoice Document Assistant
* 💬 Customer Support Knowledge Base
* ⚖️ Legal Document Search
* 🏥 Medical Document Search
* 📑 Technical Documentation Assistant

---

# ⚡ Advantages

### 1. Custom Knowledge

The system can answer questions using information from a custom PDF.

### 2. Semantic Search

Embeddings allow the system to search based on meaning rather than only exact keywords.

### 3. Efficient Retrieval

FAISS provides vector similarity search for retrieving relevant chunks.

### 4. LLM-Based Answers

The retrieved information is passed to an LLM to generate natural-language responses.

### 5. Extensible Architecture

The architecture can be extended to multiple PDFs, databases, websites, and other knowledge sources.

---

# ⚠️ Current Limitations

The current implementation is a basic RAG prototype.

Some possible improvements include:

* Better chunking strategies
* Chunk overlap
* Metadata handling
* More advanced retrieval
* Re-ranking retrieved documents
* Source citations
* Conversation memory
* Multiple PDF support
* Persistent vector databases
* Web-based user interface
* Streaming responses
* Improved prompt engineering
* Retrieval evaluation
* Hallucination detection

---

# 🚀 Future Improvements

The project can be upgraded into a production-style AI application.

### Version 2

```text
Multiple PDFs
     ↓
Document Processing
     ↓
Chunking + Overlap
     ↓
Embeddings
     ↓
FAISS / Vector Database
     ↓
Retriever
     ↓
LLM
     ↓
Answer + Sources
```

### Version 3

Add:

* Streamlit frontend
* Chat history
* Multiple document upload
* Source document references
* Conversation memory
* Better retrieval
* Document metadata
* Authentication
* API backend

---

# 📈 Learning Outcomes

Through this project, I learned how to build a basic RAG pipeline from scratch.

### Concepts Covered

* PDF text extraction
* Text preprocessing
* Text chunking
* Vector embeddings
* Vector databases/indexes
* Semantic similarity search
* FAISS
* Prompt construction
* Large Language Models
* Retrieval-Augmented Generation
* Python-based AI application development

---

# 👨‍💻 Project Skills

```text
Python
RAG
Generative AI
Large Language Models
OpenAI API
Embeddings
FAISS
Vector Search
Natural Language Processing
PDF Processing
Prompt Engineering
Jupyter Notebook
```

---

# 📌 Project Highlights for Resume

You can describe the project on your resume as:

> **Built a Retrieval-Augmented Generation (RAG) based PDF Question Answering system using Python, PyPDF2, OpenAI embeddings, FAISS, and GPT-4o-mini. Implemented PDF text extraction, chunking, vector embedding generation, similarity-based retrieval, and context-aware answer generation.**

---

# ⭐ Project Architecture

```text
                PDF
                 │
                 ▼
          ┌─────────────┐
          │   PyPDF2    │
          └──────┬──────┘
                 │
                 ▼
          ┌─────────────┐
          │   Chunking  │
          └──────┬──────┘
                 │
                 ▼
          ┌─────────────┐
          │  Embeddings │
          │ OpenAI API  │
          └──────┬──────┘
                 │
                 ▼
          ┌─────────────┐
          │    FAISS    │
          │ Vector Index│
          └──────┬──────┘
                 │
                 │
        User Question
                 │
                 ▼
          ┌─────────────┐
          │  Embedding  │
          │  Question   │
          └──────┬──────┘
                 │
                 ▼
          ┌─────────────┐
          │ Similarity  │
          │   Search    │
          └──────┬──────┘
                 │
                 ▼
          Relevant Context
                 │
                 ▼
          ┌─────────────┐
          │ GPT-4o-mini │
          └──────┬──────┘
                 │
                 ▼
             Final Answer
```

---

# 🔐 Security Note

API keys and other credentials should **never** be committed to GitHub.

Use:

```python
os.getenv("OPENAI_API_KEY")
```

instead of placing an API key directly inside the source code.

If an API key has already been exposed publicly, revoke it and create a new one before publishing the repository.

---

# 📜 License

This project is intended for educational and portfolio purposes.

You may modify and extend the project for learning and development.

---

# 🙌 Acknowledgement

This project was developed as a practical implementation of **Retrieval-Augmented Generation (RAG)** using Python, OpenAI embeddings, FAISS, and an OpenAI language model.

---

## ⭐ If you find this project useful

Feel free to ⭐ star the repository and use the project as a starting point for building more advanced RAG and Generative AI applications.

