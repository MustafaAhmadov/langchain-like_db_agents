# langchain-like_db_agents

## Overview

LangChain provides an SQL Agent that allows more flexible interaction with SQL databases than traditional chains. This agent enables advanced querying capabilities, error recovery, and more efficient use of tokens by retrieving only relevant database schema information.

### Advantages

* Can answer questions based on the database schema and content (e.g., describing a specific table).
* Can recover from errors by re-running a generated query and correcting it.
* Can make multiple queries to fully answer user questions.
* Saves tokens by only retrieving necessary schema information.

## Features

* **Query Generation & Execution**: Generate SQL queries based on user input and execute them against a database.
* **Error Handling**: Catches errors, regenerates queries, and retries automatically.
* **Flexible Table Selection**: Selects and queries only relevant tables to optimize performance.

## Setup

### Install Required Packages

To get started, install the required packages and set environment variables:

```bash
pip install --upgrade --quiet langchain langchain_community openai
```

### Set Environment Variables

```python
import getpass
import os

os.environ["OPENAI_API_KEY"] = getpass.getpass()
# Uncomment the below to use LangSmith. Not required.
# os.environ["LANGCHAIN_API_KEY"] = getpass.getpass()
# os.environ["LANGCHAIN_TRACING_V2"] = "true"
```

### SQLite Setup

1. Download the SQLite database file [here](https://raw.githubusercontent.com/lerocha/chinook-database/master/ChinookDatabase/DataSources/Chinook_Sqlite.sql).
2. Create the SQLite database:

```bash
sqlite3 Chinook.db
.read Chinook_Sqlite.sql
```

3. Verify the database:

```sql
SELECT * FROM Artist LIMIT 10;
```

### Python Code Example

```python
from langchain_community.utilities import SQLDatabase

# Initialize the database connection
db = SQLDatabase.from_uri("sqlite:///Chinook.db")

# Check the available tables
print(db.get_usable_table_names())

# Run a query
result = db.run("SELECT * FROM Artist LIMIT 10;")
print(result)
```

## Creating the SQL Agent

To create a SQL agent, you can use the following code:

```python
from langchain_community.agent_toolkits import create_sql_agent
from langchain_openai import ChatOpenAI

# Initialize the language model (e.g., OpenAI's GPT-3.5 Turbo)
llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0)

# Create the SQL agent
agent_executor = create_sql_agent(llm=llm, db=db, agent_type="openai-tools", verbose=True)

# Invoke the agent to answer a question
agent_executor.invoke("List the total sales per country. Which country's customers spent the most?")
```

### Output

The agent will respond with the results of the SQL query, including the names of relevant tables and sample data rows to help guide the question-answering process.

---

This `README.md` structure provides a clear explanation of how to use the SQL agent in LangChain, including setup instructions, code examples, and expected output. Let me know if you'd like me to adjust anything further!
