# Project Documentation: Building a Customized Chat Bot for Science-Related Questions

## Introduction

Welcome to the documentation of the customized chat bot developed for science-related questions, also known as the Science Bot. This document outlines the journey and key components of the project, from requirements to implementation. The purpose of this project is to handle scientific questions intelligently and provide accurate answers.

## How to Run

**Download and install all dependencies:**

- A `requirements.txt` file has been provided.
- Download the Llama quantized model.
- Run the server locally on port 5500 using the `start.sh` script, or run it within a Docker container using the provided Dockerfile.
- To run the UI interface built with Chainlit, go to your terminal and type:
  ```
  chainlit run test.py -w
  ```

## Directory Structure

- `main.py`: Main application file handling the Flask server, request processing, and response generation.
- `simple_text.py`: Defines the `SimpleText` class and its schema using Marshmallow.
- `ignite.py`: Launches the Flask server.
- `datastore/`: Contains state information `state.json` and individual query data `query_id.json` in a structured format.
- `vectorstore/`: Contains the generated embeddings of the texts in vector format.
- `preprocessing.py`: Handles document loading and text splitting, contributing to overall text preprocessing.
- `test.py`: Contains the logic behind the large language model where input queries are processed to obtain results.

## Implementation Steps

### 1. Understanding the Test

The initial step involved thoroughly understanding the provided test description, including the mission, ground rules, and deployment options. I reviewed the Openfabric documentation to understand the underlying data structures, classes, and functions in the project directory.

### 2. Framework Selection

The project utilizes the Openfabric PySDK for interacting with the Openfabric platform and handling execution context.

### 3. Text Preprocessing

In `preprocessing.py`, I leveraged Langchain to:

- Load PDF data stored in the `data/` folder using `PyPDFLoader` and `DirectoryLoader`.
- Split the text into chunks using `RecursiveCharacterTextSplitter`.
- Generate embeddings using a sentence-transformer model from `HuggingFaceEmbeddings`.
- Store embeddings using FAISS.

Code snippet –

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/74cbe6d0-15af-4718-a2a0-c99e4fc81d6f)

To generate these vector stores before executing the script, run `preprocessing.py`.

Output –

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/df8b240b-5761-4d1f-aaa8-1ffcf4f8567b)

These files must be generated before running the main application.

### 4. Coding the Execute Function

The core of the application is the `execute` function in `main.py`. This function processes incoming requests, handles each query, and generates appropriate responses.

- `handle_request()` was developed using Flask to handle incoming requests and pass them to `execute()`.

Code snippet –

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/240395eb-bfef-4875-8c8b-e2e45df74e88)

- Generated 32-character random alphanumeric session and query IDs.

Code snippet –

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/afe1d175-c87e-47a7-90c0-e6146323c38c)

- The `execute` function sets up a Ray class for each query, iterates through the queries, processes them, updates the datastore `query_id.json` and `state.json`, and returns output in a dictionary format via `SimpleText`.

Code snippet –

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/f809fcf0-6b02-48c6-8ccf-5c6d3f166daa)

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/8f9a3590-45ba-4de5-9ab9-13887ab72859)

### 5. Integration with Custom Model `test.py`

- Defines the custom prompt template.

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/9a7aac3b-d54b-4646-a003-633ab7005160)

- Implements the retrieval QA chain.

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/3fb28224-367f-4bde-96a5-2fd2b46ef183)

- Loads the quantized Llama 2, 7B model.

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/2c557752-3f01-4e56-a36d-4b660672f52c)

- Defines the question-answer model and combines all steps to generate output.

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/adf9d4db-4f36-4c0a-bf9a-cb5adfddf79e)

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/bb44c599-3659-4d15-a5aa-38aa638c07f7)

### 6. Error Handling

Handled within the `execute` function. Error messages are updated in the Ray class.

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/d7c5e3f5-f230-492d-baa9-f569190842e3)

### 7. State Management

The `state.json` file in `datastore/` tracks the status of each query, including queued, requested, and completed.

### 8. Output Storage

Each query output is stored as a separate `query_id.json` file in `datastore/queries/`.

### 9. Output

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/e53b909d-8e86-4956-b2f0-33fba37e553c)

### 10. UI Interface

The UI is developed using Chainlit:

```bash
chainlit run test.py -w
```

![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/609d417b-4687-410c-a4d6-6c5f3ae44925)
![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/e2ae329d-1f91-45c9-a084-1c109c963100)
![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/b48922f5-1721-4207-964f-408de95e625d)
![image](https://github.com/prajwalnayak17/Shiksha_GPT/assets/87718913/fd68b6b3-a036-4dcc-a24b-42c49ab64a3c)

## Usage

Run locally with `start.sh` or within a Docker container using the Dockerfile.

## Future Scope

- Improve response generation speed.
- Enhance answer accuracy and context handling.
- Optimize performance for multiple runs and large queries.

## Conclusion

The customized chat bot meets the test requirements, effectively integrating Openfabric PySDK, a custom AI model, error handling, and state/output management.

## Important Links

- [Openfabric PySDK Docs](https://docs.openfabric.ai/developer-tools/index/)
- [Langchain Docs](https://python.langchain.com/docs/get_started/introduction)
- [Chainlit Docs](https://github.com/Chainlit/chainlit)
- [Ctransformers Docs](https://github.com/marella/ctransformers)

