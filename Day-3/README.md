# Agentic AI -- Day 3

## Project: Sweet Crumbs Home Bakery Customer Support Chatbot

This project builds a simple AI-powered customer support chatbot for
**Sweet Crumbs Home Bakery** using LangChain and the Groq API. The
chatbot answers customer questions about bakery products, prices,
eggless options, order notice requirements, and delivery-related
queries.

## Objectives

-   Connect a language model using the Groq API.
-   Create a customer-support assistant with a system prompt.
-   Use LangChain prompt templates and message history.
-   Experiment with model temperature and observe response consistency.
-   Evaluate chatbot responses using a CSV-based test set.
-   Write Python helper functions for price calculation and order
    availability.

## Technologies Used

-   Python
-   Google Colab
-   LangChain
-   LangChain-Groq
-   Groq API (`openai/gpt-oss-120b`)
-   CSV

## Business Information

  Product / Policy               Details
  ------------------ -------------------
  Plum cake                         ₹500
  Chocolate cake                    ₹650
  Cupcake                       ₹80 each
  Eggless cakes                Available
  Advance notice       At least 24 hours

The assistant is instructed to be polite and concise, avoid inventing
prices or policies, and refer uncertain questions to the owner.

## Main Implementation

### 1. Groq connection

The notebook retrieves `GROQ_API_KEY` from Google Colab Secrets and
initializes the Groq chat model through `ChatGroq`.

### 2. Prompt and conversation history

A `ChatPromptTemplate` combines: - A system message containing the
bakery's role, business facts, and response rules. - A
`MessagesPlaceholder` for conversation history. - The customer's current
question.

The `ask()` function sends the question to the LangChain chain, stores
the human and assistant messages in `history`, and returns the response.

### 3. Chatbot behaviour

The chatbot can answer questions such as: - Do you make eggless cakes? -
What is the price of plum cake? - How much does chocolate cake cost? -
Can I order a cake for today evening? - Can you guarantee delivery at 5
PM?

For information that is not confirmed, such as delivery coverage or
special discounts, the assistant should check with the owner rather than
make a promise.

### 4. CSV-based testing

The notebook creates a `tests.csv` file containing 10 customer questions
and expected keywords. The `run_tests()` function: 1. Reads the test
cases from the CSV file. 2. Sends each question to the model with an
empty conversation history. 3. Checks whether the expected keyword
appears in the response. 4. Prints PASS or FAIL for each test and
calculates the overall pass percentage.

**Observed results:** Baseline runs achieved 7/10 tests (70%). Further
prompt and temperature experiments also produced 70% in some runs, while
temperature 1.0 runs produced 60%. The prompt improvement experiment
remained at 70%.

These results show that changing temperature or rewording the prompt did
not automatically improve the test score. Some failures occurred because
the response used wording different from the exact expected keyword, or
did not follow the expected fallback wording.

### 5. Python helper functions

The notebook also implements two standalone functions.

**Price calculation**

`get_price(item, quantity)` looks up an item in the menu and returns its
total price. It raises an error if the item is not available or the
quantity is not positive.

Example results: - `get_price("plum cake")` → `500` -
`get_price("cupcake", 6)` → `480` - `get_price("chocolate cake", 2)` →
`1300`

**Order availability**

`check_order_availability(hours_until_order)` checks whether an order is
placed at least 24 hours in advance.

Example results: - `check_order_availability(48)` → `True` -
`check_order_availability(24)` → `True` - `check_order_availability(5)`
→ `False`

## How to Run

1.  Open the notebook in Google Colab.

2.  Install the required packages:

    ``` python
    !pip install -q langchain langchain-groq
    ```

3.  Add your Groq API key to Google Colab Secrets with the name
    `GROQ_API_KEY`.

4.  Run the notebook cells from top to bottom.

5.  Review the chatbot responses, CSV test results, and helper-function
    outputs.

> Keep your API key private. Do not paste it directly into the notebook
> or commit it to a public GitHub repository.

## Learning Outcomes

-   Learned how to connect a Groq-hosted language model using LangChain.
-   Practised writing system prompts with business-specific rules.
-   Used conversation history to maintain context between questions.
-   Evaluated LLM responses with simple automated keyword tests.
-   Observed how temperature changes can affect response consistency.
-   Implemented basic Python functions for menu pricing and order
    validation.

## Author

**Batta Sai Kumar**
