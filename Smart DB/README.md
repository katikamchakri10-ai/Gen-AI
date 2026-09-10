
# 🤖 SmartDB – AI-Powered SQL Database Chatbot

SmartDB is an **AI-powered natural-language database chatbot** that allows users to interact with a MySQL movie database using normal English questions instead of writing SQL queries manually.

The application uses **LangChain, OpenAI GPT-4o-mini, MySQL, PyMySQL, and SQLDatabase** to understand a user's question, generate a valid SQL query, execute it against the database, and convert the database result into a simple natural-language response.

---

## 📌 Project Overview

Normally, users need to know SQL to query a relational database.

For example, a user might need to write:

```sql
SELECT title, rating
FROM movies
ORDER BY rating DESC
LIMIT 5;
```

With SmartDB, the user can simply ask:

```text
Show me the top 5 highest rated movies
```

The AI automatically generates the required SQL query, executes it, and explains the result.

### Basic Workflow

```text
User Question
      ↓
OpenAI LLM
      ↓
Natural Language → SQL
      ↓
SQL Safety Check
      ↓
MySQL Database
      ↓
SQL Result
      ↓
OpenAI LLM
      ↓
Natural Language Answer
```

---

# ✨ Features

* 🤖 Natural-language database interaction
* 🧠 AI-powered SQL query generation
* 🗄️ MySQL database integration
* 🔗 LangChain SQLDatabase integration
* 🔍 Automatic database schema inspection
* 🛡️ Basic SQL safety validation
* 🧹 Automatic SQL cleanup
* 📊 SQL query execution
* 💬 Natural-language result generation
* 🎬 Movie and actor database support
* 🔄 Interactive chatbot workflow

---

# 🛠️ Technologies Used

| Technology         | Purpose                                |
| ------------------ | -------------------------------------- |
| Python             | Main programming language              |
| LangChain          | LLM and database integration           |
| OpenAI GPT-4o-mini | SQL generation and response generation |
| MySQL              | Relational database                    |
| PyMySQL            | Python MySQL database driver           |
| SQLDatabase        | Connect LangChain with MySQL           |
| Jupyter Notebook   | Development environment                |

---

# 🧠 AI Architecture

SmartDB uses the LLM in two major stages.

### Stage 1 – Question to SQL

The user's natural-language question is provided to the LLM along with the database schema.

```text
User Question
      ↓
Database Schema
      ↓
GPT-4o-mini
      ↓
SQL Query
```

Example:

```text
User:
Which actor has acted in the most movies?
```

The LLM generates:

```sql
SELECT
    a.name,
    COUNT(ma.movie_id) AS movie_count
FROM actors a
JOIN movie_actors ma
    ON a.actor_id = ma.actor_id
GROUP BY a.actor_id, a.name
ORDER BY movie_count DESC
LIMIT 1;
```

---

### Stage 2 – SQL Result to Answer

After the SQL query is executed, the result is passed to the LLM.

```text
SQL Result
    ↓
GPT-4o-mini
    ↓
Simple English Explanation
```

This makes the chatbot easier for non-technical users to interact with.

---

# 📊 Database Structure

The project works with a movie database containing the following main tables.

```text
movies
   │
   │ movie_id
   │
   ▼
movie_actors
   │
   │ actor_id
   ▼
actors
```

## 🎬 `movies`

Contains movie-related information.

Example fields used by the application include:

```text
movie_id
title
year
release_date
rating
revenue
```

---

## 👤 `actors`

Contains actor-related information.

Example fields include:

```text
actor_id
name
birth_year
gender
birthplace
```

---

## 🔗 `movie_actors`

This table connects movies and actors.

Example fields:

```text
movie_id
actor_id
character_name
```

The relationship can be represented as:

```text
Movies
  │
  │ movie_id
  ▼
Movie_Actors
  │
  │ actor_id
  ▼
Actors
```

This allows the chatbot to answer questions involving both movies and actors.

---

# 🔄 Complete Project Flow

## Step 1 – Connect to MySQL

The application connects to the MySQL database using SQLAlchemy through LangChain's `SQLDatabase`.

```python
db = SQLDatabase.from_uri(DATABASE_URI)
```

The application then checks the available database tables.

```python
db.get_usable_table_names()
```

---

## Step 2 – Retrieve Database Schema

When a question is asked, the application retrieves information about the database schema.

```python
schema = db.get_table_info()
```

The schema is provided to the LLM.

This helps the model generate SQL using the actual tables and columns available in the database.

---

# Step 3 – Generate SQL

The application sends a detailed prompt to GPT-4o-mini.

The prompt instructs the model to:

* Use only available tables
* Use only available columns
* Avoid inventing database fields
* Use JOIN when required
* Use `movie_actors` for actor/movie relationships
* Return only valid MySQL SQL
* Return one SQL query

Example:

```text
Question:

Show movies released after 2020
```

Possible generated SQL:

```sql
SELECT title, year
FROM movies
WHERE year > 2020;
```

---

# Step 4 – Clean Generated SQL

The project includes a function called `clean_sql()`.

````python
def clean_sql(sql):
    sql = sql.strip()

    if sql.startswith("```"):
        sql = sql.replace("```sql", "").replace("```", "").strip()

    return sql
````

This removes Markdown code fences if the LLM accidentally returns:

````text
```sql
SELECT ...
````

````

and converts it into clean SQL.

---

# Step 5 – SQL Safety Check

The project implements a basic SQL security layer using:

```python
def is_safe_sql(sql: str):
````

The application checks for potentially destructive SQL commands such as:

```text
INSERT
UPDATE
DELETE
DROP
ALTER
TRUNCATE
```

If one of these operations is detected, the application raises:

```text
Unsafe SQL detected.
```

This prevents the chatbot from intentionally executing common data-modification or destructive commands.

> Note: This is a basic protection mechanism and should not be considered a complete production-grade SQL security system.

---

# Step 6 – Execute SQL

After the SQL passes the safety check, it is executed against the MySQL database.

```python
result = db.run(sql_query)
```

The database returns the query result.

---

# Step 7 – Generate Natural-Language Answer

The SQL query and its result are then passed to GPT-4o-mini.

The model is instructed to:

* Answer the user's question directly
* Use only information contained in the SQL result
* Avoid inventing information
* Keep the explanation clear
* Avoid unnecessary SQL terminology

Example:

```text
User Question:
What is the highest earning movie?

SQL Result:
[('Avatar', 2923706026)]
```

The chatbot can return:

```text
Avatar is the highest earning movie in the database, with revenue of approximately $2.92 billion.
```

---

# 💬 Example Questions

The chatbot can handle questions such as:

### Movies

```text
Show all movies
```

```text
List movies released after 2020
```

```text
Show the top 5 highest rated movies
```

```text
Show movies with a rating above 8.5
```

```text
Show movies released in 2010
```

---

### Actors

```text
Show all actors
```

```text
Show actors born before 1970
```

```text
Which actor was born earliest?
```

```text
How many actors are in the database?
```

---

### Movie and Actor Relationships

```text
Show the actors in Inception
```

```text
Show all actors who acted in Titanic
```

```text
Show all movies Leonardo DiCaprio acted in
```

```text
How many movies has each actor acted in?
```

```text
Which actor has acted in the most movies?
```

```text
Show movies with their actors
```

---

### Ratings and Revenue

```text
What is the average rating of Leonardo DiCaprio movies?
```

```text
Which movies earned more than 500 million?
```

```text
Show the highest earning movie
```

---

# 🧩 Main Python Functions

## `clean_sql()`

Removes unnecessary formatting from the generated SQL query.

```python
def clean_sql(sql):
```

---

## `is_safe_sql()`

Performs a basic safety check on generated SQL.

```python
def is_safe_sql(sql: str):
```

---

## `ask_db()`

This is the main function of the application.

It performs the complete workflow:

```text
Get Database Schema
        ↓
Generate SQL
        ↓
Clean SQL
        ↓
Check SQL Safety
        ↓
Execute SQL
        ↓
Get Database Result
        ↓
Generate Explanation
        ↓
Return Answer
```

---

# 🏗️ System Architecture

```text
                  ┌───────────────────┐
                  │      USER         │
                  │ Natural Language  │
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │   GPT-4o-mini     │
                  │   SQL Generator   │
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │    clean_sql()    │
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │ is_safe_sql()     │
                  │ Security Check    │
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │      MySQL        │
                  │  Movie Database   │
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │    SQL Result     │
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │   GPT-4o-mini     │
                  │ Result Explainer  │
                  └─────────┬─────────┘
                            │
                            ▼
                  ┌───────────────────┐
                  │   Final Answer    │
                  └───────────────────┘
```

---

# 📁 Project Structure

Recommended GitHub repository structure:

```text
SmartDB/
│
├── SmartDB.ipynb
├── README.md
├── requirements.txt
├── .gitignore
└── .env.example
```

---

# 📦 Installation

Clone the repository:

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
```

Move into the project directory:

```bash
cd SmartDB
```

Install the required packages:

```bash
pip install pymysql
pip install langchain
pip install langchain-openai
pip install langchain-community
```

Or use the `requirements.txt` file:

```bash
pip install -r requirements.txt
```

---

# 📋 Requirements

Create a `requirements.txt` file:

```text
pymysql
langchain
langchain-openai
langchain-community
```

---

# 🔑 API Key Configuration

The project uses OpenAI's API.

**Never commit your OpenAI API key to GitHub.**

Use an environment variable instead.

### Windows

```bash
setx OPENAI_API_KEY "your_api_key_here"
```

Then restart Jupyter or your terminal.

Use:

```python
import os

api_key = os.getenv("OPENAI_API_KEY")
```

and initialize the model:

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.2
)
```

---

# 🗄️ MySQL Configuration

The project requires a MySQL database containing the movie-related tables.

The expected database structure includes:

```text
movie_database
│
├── movies
├── actors
└── movie_actors
```

Configure your database connection using environment variables instead of hard-coding credentials.

Example:

```python
import os

DATABASE_URI = (
    f"mysql+pymysql://{os.getenv('DB_USER')}:"
    f"{os.getenv('DB_PASSWORD')}@"
    f"{os.getenv('DB_HOST')}:"
    f"{os.getenv('DB_PORT')}/"
    f"{os.getenv('DB_NAME')}"
)
```

Then:

```python
from langchain_community.utilities import SQLDatabase

db = SQLDatabase.from_uri(DATABASE_URI)
```

---

# ▶️ How to Run

### 1. Start MySQL

Make sure your MySQL server is running.

### 2. Create the database

Create:

```text
movie_database
```

### 3. Create the required tables

The database should contain:

```text
movies
actors
movie_actors
```

### 4. Configure environment variables

Set your OpenAI API key and database credentials.

### 5. Open Jupyter Notebook

```bash
jupyter notebook
```

### 6. Open

```text
SmartDB.ipynb
```

### 7. Run the cells

The application will connect to MySQL and initialize the AI model.

### 8. Ask questions

Example:

```text
Which actor has acted in the most movies?
```

The AI generates SQL, executes it, and returns a natural-language answer.

---

# 🔐 Security Considerations

This project includes a basic SQL safety check.

The application blocks common destructive operations:

```text
INSERT
UPDATE
DELETE
DROP
ALTER
TRUNCATE
```

However, for production applications, additional security controls should be implemented.

Recommended improvements:

* Use a read-only database user
* Use parameterized queries where appropriate
* Restrict database permissions
* Validate generated SQL using a proper SQL parser
* Add query timeout limits
* Add query-result limits
* Log generated queries
* Add authentication
* Add rate limiting
* Never expose database credentials
* Never hard-code API keys

---

# ⚠️ Important Security Warning

Do **not** store credentials like this inside your GitHub repository:

```python
DATABASE_URI = "mysql+pymysql://username:password@host/database"
```

Instead use environment variables:

```python
os.getenv("DB_PASSWORD")
```

Also do not commit:

```text
.env
secret_key.py
API keys
database passwords
```

Add them to `.gitignore`.

---

# 📄 Recommended `.gitignore`

Create a `.gitignore` file:

```text
# Python
__pycache__/
*.py[cod]

# Jupyter
.ipynb_checkpoints/

# Environment variables
.env

# API keys / secrets
secret_key.py

# Virtual environments
venv/
.venv/
env/

# OS files
.DS_Store
Thumbs.db
```

---

# 🎯 Project Objectives

The main objectives of SmartDB are:

1. Allow users to query databases using natural language.
2. Automatically convert natural-language questions into SQL.
3. Reduce the requirement for users to understand SQL syntax.
4. Execute generated queries against a MySQL database.
5. Convert database results into understandable English.
6. Demonstrate the integration of Generative AI with relational databases.

---

# 💡 Why This Project Is Useful

Traditional database applications require users to understand:

```text
SQL
Tables
Columns
JOINs
WHERE conditions
GROUP BY
ORDER BY
Aggregations
```

SmartDB provides a simpler interface:

```text
Natural Language
       ↓
       AI
       ↓
      SQL
       ↓
    Database
       ↓
Natural Language
```

This makes database querying more accessible to non-technical users.

---

# 🚀 Future Enhancements

The current project can be extended into a production-level AI database assistant.

### 1. Web Interface

Build a frontend using:

```text
Streamlit
```

or:

```text
React
```

---

### 2. Conversation Memory

Allow users to ask follow-up questions.

Example:

```text
User:
Show the top 5 highest rated movies.

User:
Which one has the highest revenue?
```

The chatbot should understand that "which one" refers to the previously retrieved movies.

---

### 3. Multi-Database Support

Extend the application to support:

```text
MySQL
PostgreSQL
SQLite
SQL Server
```

---

### 4. Advanced SQL Validation

Replace the simple forbidden-word check with a proper SQL parser and validation layer.

---

### 5. Read-Only Database User

Create a MySQL user with only:

```text
SELECT
```

permissions.

This provides an additional security layer.

---

### 6. Query Visualization

Automatically generate charts for suitable questions.

Example:

```text
Show movie revenue by year
```

The system could generate a bar chart.

---

### 7. Query Explanation

Allow users to see:

```text
Natural Language Question
        ↓
Generated SQL
        ↓
Database Result
        ↓
AI Explanation
```

This would make the application useful for learning SQL as well.

---

# 📚 Concepts Learned

This project demonstrates practical knowledge of:

* Python
* Generative AI
* Large Language Models
* OpenAI API
* LangChain
* Natural Language Processing
* Text-to-SQL
* SQL
* MySQL
* Database connectivity
* PyMySQL
* Database schema inspection
* Prompt engineering
* SQL validation
* AI-powered database applications

---

# 🧑‍💻 Resume Project Description

### SmartDB – AI-Powered SQL Database Chatbot

> Built an AI-powered natural-language SQL chatbot using Python, LangChain, OpenAI GPT-4o-mini, PyMySQL, and MySQL. Implemented schema-aware text-to-SQL generation, SQL sanitization, database query execution, and LLM-based result summarization, enabling users to query movie and actor data using natural-language questions.

---

# 📌 Resume Bullet Points

You can use these bullets on your resume:

* Developed an **AI-powered Text-to-SQL chatbot** using Python, LangChain, OpenAI GPT-4o-mini, and MySQL.
* Implemented **schema-aware SQL generation** using database metadata to reduce invalid table and column references.
* Built a pipeline to **validate, execute, and summarize SQL queries** generated from natural-language questions.
* Integrated **MySQL with LangChain SQLDatabase and PyMySQL** for dynamic database interaction.
* Added a basic SQL security layer to prevent execution of destructive operations such as `DROP`, `DELETE`, `UPDATE`, and `TRUNCATE`.

---

# 🎤 Interview Explanation

If an interviewer asks:

### "Explain your SmartDB project."

You can say:

> SmartDB is an AI-powered Text-to-SQL application that allows users to query a MySQL movie database using natural language. I used LangChain's SQLDatabase to connect Python with MySQL and OpenAI GPT-4o-mini for language understanding. When a user asks a question, I first retrieve the database schema and provide it to the LLM. The LLM generates a MySQL query using only the available tables and columns. I then clean and validate the generated SQL before executing it against the database. Finally, I pass the SQL result back to the LLM, which converts the result into a simple English response for the user.

---

# 🔎 Example End-to-End Interaction

### User

```text
Which actor has acted in the most movies?
```

### AI-generated SQL

```sql
SELECT
    a.name,
    COUNT(ma.movie_id) AS movie_count
FROM actors a
JOIN movie_actors ma
    ON a.actor_id = ma.actor_id
GROUP BY a.actor_id, a.name
ORDER BY movie_count DESC
LIMIT 1;
```

### Database

```text
Returns the actor with the highest movie count.
```

### AI Response

```text
The actor with the most movies is [actor name], with [number] movies.
```

---

# 🏆 Project Highlights

```text
✅ Natural Language → SQL
✅ Schema-Aware SQL Generation
✅ MySQL Integration
✅ LangChain Integration
✅ OpenAI GPT-4o-mini
✅ SQL Safety Validation
✅ SQL Execution
✅ Result → Natural Language
✅ Interactive Database Chatbot
```

---

# 📈 Learning Outcome

Through this project, I learned how to combine **Generative AI with traditional relational databases**.

The project helped me understand how an LLM can:

```text
Understand User Intent
        ↓
Understand Database Schema
        ↓
Generate SQL
        ↓
Retrieve Database Information
        ↓
Interpret Results
        ↓
Generate Human-Friendly Answers
```

This architecture can be used as the foundation for building more advanced **AI Database Agents, Enterprise Data Assistants, and Text-to-SQL systems**.

---

# ⭐ Future Vision

SmartDB can be evolved into an enterprise AI data assistant where employees can ask questions such as:

```text
Show this month's sales.
```

```text
Which product generated the highest revenue?
```

```text
Compare this year's revenue with last year.
```

```text
Show the top 10 customers.
```

The AI would automatically understand the request, generate the required SQL, retrieve the data, analyze the result, and present the answer in an easy-to-understand format.

---

# 📜 License

This project is created for educational, learning, and portfolio purposes.

---

# 👨‍💻 Author

**Chakradhar Reddy**

Generative AI | RAG | AI Agents | Python | SQL | n8n

---

## ⭐ If you find this project useful

Consider giving this repository a ⭐ star and using it as a starting point for building your own AI-powered database applications.
