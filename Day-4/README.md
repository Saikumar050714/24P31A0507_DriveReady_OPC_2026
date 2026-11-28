# Agentic AI -- Bakery Order Assistant

## Project Overview

This project develops an AI-powered order assistant for **Sweet Crumbs
Home Bakery** using LangChain, the Groq API, and Google Sheets.

The assistant can answer questions about bakery prices and opening
hours, collect customer order details, calculate order totals, and save
order information to a Google Sheet using LangChain tools.

## Objectives

-   Connect a Groq-hosted language model using LangChain.
-   Authenticate with Google and create a Google Sheet using `gspread`.
-   Define tools that allow the AI agent to retrieve prices, check
    opening hours, and log orders.
-   Validate order details with a Pydantic model.
-   Maintain conversation history during a chat session.
-   Extract structured order information from natural-language messages.
-   Inspect tool calls and tool responses to understand the agent
    workflow.

## Technologies Used

-   Python
-   Google Colab
-   LangChain
-   LangChain-Groq
-   Groq API (`openai/gpt-oss-20b`)
-   Google Sheets
-   `gspread`
-   Pydantic

## Bakery Information

### Menu

  Item               Price
  ---------------- -------
  Plum cake           ₹500
  Chocolate cake      ₹650
  Cupcake              ₹80

### Opening Hours

  Day                Opening hours
  ------------------ ---------------
  Monday--Saturday   9 AM to 8 PM
  Sunday             10 AM to 6 PM

Additional business rules: - Orders require at least 24 hours' advance
notice. - Eggless versions of all cakes are available.

## Main Implementation

### 1. Install packages and configure the model

The notebook installs the required Python packages, retrieves
`GROQ_API_KEY` from Google Colab Secrets, and initializes the `ChatGroq`
model using `openai/gpt-oss-20b`.

### 2. Connect to Google Sheets

The notebook authenticates the Google account, authorizes access through
`gspread`, creates a spreadsheet named **Sweet Crumbs Orders**, and adds
the following column headings:

`name`, `item`, `qty`, `date`, `eggless`, `total`

The generated spreadsheet URL is printed when the sheet is ready.

### 3. Define and validate order details

A Pydantic `Order` model describes the required order fields: - `name`:
customer's first name - `item`: plum cake, chocolate cake, or cupcake -
`qty`: quantity, with a minimum value of 1 - `date`: pickup date as
provided by the customer - `eggless`: whether an eggless order was
requested

The `Literal` type restricts the item to the supported menu choices,
while Pydantic validates the quantity.

### 4. Create LangChain tools

The notebook defines three tools for the agent:

-   **`get_price(item, qty)`** -- looks up the menu price and returns
    the total for the requested quantity. It reports when an item is not
    on the menu.
-   **`is_open(day)`** -- returns the bakery's opening hours for the
    requested day.
-   **`log_order(name, item, qty, date, eggless)`** -- validates the
    order with the `Order` model, calculates the total, and appends the
    order details to the Google Sheet.

The order-logging tool is intended to be used only after the customer
has provided their name, item, quantity, and pickup date.

### 5. Build the AI agent

The notebook uses LangChain's `create_agent` with the Groq model and the
three tools.

The system prompt instructs the assistant to: - Act as a friendly Sweet
Crumbs Home Bakery order assistant. - Follow the 24-hour advance-notice
policy. - Use `get_price` for prices and `is_open` for opening hours. -
Collect all required details before saving an order. - Read the saved
order and total back to the customer. - Keep replies under 60 words. -
Respond in English or Telugu according to the customer's language.

The `chat()` function sends the user's message to the agent, stores the
returned messages in conversation history, and returns the assistant's
latest reply. If an exception occurs, it prints the error and returns a
short retry message.

### 6. Example chatbot interaction

The notebook tests a question about Sunday opening hours and the price
of two chocolate cakes. The assistant responds that the bakery is open
from 10 AM to 6 PM on Sunday and that two chocolate cakes cost ₹1,300.

A second example provides the customer's name, quantity, eggless
preference, and pickup date. The notebook displays a confirmation for
two eggless chocolate cakes for Ravi on 24 December, with a total of
₹1,300.

### 7. Check the 24-hour order policy

The `check_order_availability(hours_until_order)` tool checks whether an
order meets the minimum 24-hour advance-notice requirement.

Example results: - 48 hours → `Order can be accepted.` - 12 hours →
`Order requires at least 24 hours' notice.`

### 8. Extract structured order information

The notebook uses `llm.with_structured_output(Order)` to extract order
details from natural-language customer messages.

The examples demonstrate extracting: - Customer name - Menu item -
Quantity - Requested date - Eggless preference

Messages from Ravi, Sita, John, Ahmed, and Priya are used to demonstrate
structured extraction.

The notebook also tests invalid requests, such as ordering donuts (which
are not on the menu) and requesting zero cupcakes. These examples
produce tool-use errors because the model does not return a valid
structured `Order` for those requests.

### 9. Inspect agent tool calls

The notebook prints the agent's message trace for a question about
Sunday opening hours and cupcake prices.

The trace shows the assistant calling `is_open` and `get_price`,
receiving the tool results, and then generating a final response. It
also counts the AI messages and tool messages in the trace.

## How to Run

1.  Open the notebook in Google Colab.

2.  Install the required packages:

    ``` python
    !pip install -q langchain langchain-groq gspread
    ```

3.  Add your Groq API key to Google Colab Secrets using the name
    `GROQ_API_KEY`.

4.  Run the notebook cells in order.

5.  When Google authentication is requested, sign in and authorize
    access to Google Sheets.

6.  Check the printed spreadsheet URL to view the **Sweet Crumbs
    Orders** sheet.

7.  Test the assistant with customer questions and order details.

> **Security note:** Keep your Groq API key private. Use Google Colab
> Secrets rather than writing the key directly in the notebook. Review
> Google account permissions before granting spreadsheet access.

## Learning Outcomes

-   Learned how to create a tool-using AI agent with LangChain.
-   Practised connecting a Groq language model to custom Python tools.
-   Used Google Sheets as a simple order-record storage destination.
-   Applied Pydantic models to validate structured order data.
-   Extracted structured information from natural-language messages.
-   Observed how an agent calls tools and uses their results.
-   Practised maintaining conversation history in an agent-based
    chatbot.

## Author

**Batta Sai Kumar**
