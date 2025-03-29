# Automated Cold Email Generation for Software Consulting

This project demonstrates an automated workflow for generating cold emails to potential clients based on their job postings. It leverages Langchain, Groq's LLM, web scraping, JSON parsing, Pandas, and ChromaDB for semantic search.

## Workflow

1.  **Web Scraping:** Scrapes job posting information from a specified webpage (`https://www.techtarget.com/WhatIs/feature/Tech-job-boards-to-know`).
2.  **Job Posting Extraction:** Uses Groq's `llama-3.1-70b-versatile` model to extract structured job posting data (role, experience, skills, description) from the scraped text and formats it as JSON.
3.  **Portfolio Data:** Loads portfolio information (tech stack and associated links) from a CSV file (`my_portfolio.csv`).
4.  **Semantic Search (ChromaDB):** Creates embeddings of the portfolio tech stacks and stores them in a ChromaDB vector store.
5.  **Relevant Portfolio Retrieval:** For each extracted job posting, it queries the ChromaDB vector store to find the most relevant portfolio entries based on the job's skills and role.
6.  **Cold Email Generation:** Uses Groq's LLM again, along with a predefined prompt, to generate a personalized cold email. The email introduces "XYZ Software Consulting," highlights its expertise in business process automation, and includes the most relevant portfolio links retrieved from ChromaDB to showcase the company's capabilities.

## Technologies Used

* **Langchain:** A framework for building applications powered by large language models (LLMs).
* **Langchain Groq:** Integration for using Groq's LLMs within Langchain.
* **Groq:** A platform providing fast inference for large language models.
* **Langchain Community (WebBaseLoader):** A Langchain module for loading web content.
* **Langchain Core (PromptTemplate, JsonOutputParser):** Langchain modules for creating prompts and parsing JSON output.
* **Pandas:** A Python library for data manipulation and analysis (used for loading the portfolio CSV).
* **ChromaDB:** An open-source embedding database for storing and querying vector embeddings (used for semantic search of the portfolio).
* **UUID:** A Python module for generating unique identifiers (used for ChromaDB document IDs).

## Setup

### Prerequisites

* Python 3.6 or higher.
* Pip package installer.
* A Groq API key (required for using Groq's LLM). You'll need to set this as an environment variable or directly in the `ChatGroq` initialization.
* A CSV file named `my_portfolio.csv` in the same directory as the Python script, with columns named "Techstack" and "Links" containing your portfolio information.

### Installation

1.  Clone this repository (if applicable) or create a new directory for the project.
2.  Navigate to the project directory in your terminal.
3.  Install the required Python libraries using pip:
    ```bash
    pip install langchain langchain-groq langchain-community chromadb pandas
    ```

### Configuration

1.  **Groq API Key:** Ensure your Groq API key is set as an environment variable (e.g., `GROQ_API_KEY=your_api_key`) or replace the empty string in the `llm = ChatGroq(groq_api_key='')` line with your actual key.
2.  **`my_portfolio.csv`:** Create a CSV file named `my_portfolio.csv` with at least two columns:
    * **Techstack:** A string describing the technologies used in a portfolio project (e.g., "React, Node.js, MongoDB").
    * **Links:** A URL to the portfolio project or relevant information.

    Example `my_portfolio.csv`:
    ```csv
    Techstack,Links
    React, Node.js, MongoDB,[https://example.com/react-portfolio](https://example.com/react-portfolio)
    Angular,.NET, SQL Server,[https://example.com/angular-portfolio](https://www.google.com/search?q=https://example.com/angular-portfolio)
    Python, Django, MySQL,[https://example.com/python-portfolio](https://example.com/python-portfolio)
    Machine Learning, Python, TensorFlow,[https://example.com/ml-python-portfolio](https://example.com/ml-python-portfolio)
    ```

## Running the Code

1.  Save the provided Python code as a `.py` file (e.g., `generate_emails.py`).
2.  Open your terminal and navigate to the project directory.
3.  Run the script using:
    ```bash
    python generate_emails.py
    ```

    The generated cold email will be printed to the console.

## Code Explanation

* **Import Libraries:** Imports necessary modules from Langchain, Groq, Pandas, ChromaDB, and UUID.
* **Initialize LLM:** Initializes the Groq `llama-3.1-70b-versatile` model with a temperature of 0 for deterministic output.
* **Load Web Data:** Uses `WebBaseLoader` to fetch the content of the specified webpage containing tech job boards.
* **Extract Job Postings:** Defines a Langchain prompt to instruct the LLM to extract job details (role, experience, skills, description) from the scraped webpage content and return them in JSON format.
* **Parse JSON Output:** Uses `JsonOutputParser` to convert the LLM's JSON response into a Python list of dictionaries.
* **Load Portfolio Data:** Reads the `my_portfolio.csv` file into a Pandas DataFrame.
* **Initialize ChromaDB:** Creates or gets an existing ChromaDB collection named "portfolio" for storing portfolio embeddings.
* **Populate ChromaDB:** If the "portfolio" collection is empty, it iterates through the `my_portfolio.csv` and adds the "Techstack" as the document content, "Links" as metadata, and a unique ID to the ChromaDB collection.
* **Retrieve Relevant Links:** For each extracted job posting, it queries the ChromaDB collection using the job's skills and role as query texts to find the most semantically similar portfolio entries. It retrieves the associated "Links" as relevant portfolio showcases.
* **Generate Cold Email:** Defines a Langchain prompt for generating a cold email. This prompt includes:
    * A persona (Chavinda Wijethilake, BDE at XYZ Software Consulting).
    * A description of the company's expertise.
    * The extracted job descriptions.
    * The most relevant portfolio links retrieved from ChromaDB.
    * Instructions to write a cold email tailored to the job postings, highlighting XYZ's capabilities and including the relevant portfolio links.
* **Invoke LLM for Email:** Uses the Langchain chain to invoke the Groq LLM with the email prompt and the job descriptions and relevant links as input.
* **Print Output:** Prints the generated cold email content to the console.

## Further Improvements

* **More Sophisticated Job Extraction:** Improve the job posting extraction prompt to handle more varied website structures and extract more specific information (e.g., required experience level).
* **Refined Semantic Search:** Experiment with different embedding models and ChromaDB query parameters for more accurate retrieval of relevant portfolio items.
* **Personalization:** Enhance the email prompt to further personalize the cold email based on the specific job roles and the content of the job board.
* **Email Sending Integration:** Integrate with an email sending service (e.g., SendGrid, Mailgun) to automatically send the generated emails.
* **Error Handling:** Add more robust error handling for web scraping, API calls, and data processing.
* **Configuration:** Externalize configuration parameters (e.g., API keys, URLs, file paths) for better maintainability.
* **Rate Limiting:** Implement rate limiting for API calls to avoid exceeding usage limits.
