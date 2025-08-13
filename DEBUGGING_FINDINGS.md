**Error 1 Observed:**
When running demo.py, the application would print "⚠️ No Google API key found. Using mock responses for demo." even if a valid .env file with the GOOGLE_API_KEY was present in the directory.

**LLM Assistance Used:**
I asked gemini: "no google api key found even after putting the .env file, please check my code" and give the codes from demo.py

**Root Cause:**
The demo.py file imports load_dotenv from the dotenv library but never calls the function. The code (# os.environ[]) is also commented out, even if uncomment the syntax also wrong. So the contents of the .env file were never loaded.

**Fix Applied:**

# Before
os.environ[]

# After
load_dotenv()


**Verification:**
I run the demo.py again and now there is no warning message anymore.




**Error 2 Observed:**
The console display a error message of TypeError: ConversationRouter.__init__() missing 1 required positional argument: 'tools'.

**LLM Assistance Used:**
I gave gemini the error message and ask whats the problem and how to fix it.

**Root Cause:**
The code from the router.py have two paremeters, llm and tools. 

self.query_router = QueryRouter(llm, tools) 

But The code from demo.py line 35 is does not include the argument tools, only the llm

**Fix Applied:**

# Before
router = ConversationRouter(llm)

# After
router = ConversationRouter(llm, tools)


**Verification:**
I run the demo.py again and now there is no error message anymore.




**Error 3 Observed:**
The AI is not answering the user's questions. Only write like "confirming the prompt is correct".

**LLM Assistance Used:**
I gave gemini the output and asked whats the problem.

**Root Cause:**
The routing, extration, and general prompt on router.py is not an instruction prompt.

**Fix Applied:**

# --- Fix for routing_prompt ---
# Before
template=""" this prompt is correct."""

# After
template="""Given the user query, which of the following tools is the most appropriate to use? Respond with only the name of the tool from the list. If no tool is suitable, respond with 'general_chat'.

Available Tools:
{available_tools}

User Query: {query}"""


# --- Fix for param_extraction_prompt ---
# Before
template="""This is a correct prompt and it is correct."""

# After
template="""Based on the user query, extract the single, most relevant parameter needed for the following tool. Return only the parameter value itself.

Tool Description: {tool_description}
User Query: {query}

Extracted Parameter:"""


# --- general_prompt ---
# Before
template="""This is a correct prompt and it is correct."""

# After
template="""You are a helpful assistant. Continue the conversation based on the provided history.

Conversation History:
{context}

User's Latest Message: {message}

Assistant:"""


**Verification:**
I run the demo.py again and now the AI is answering the user's question correctly.



**Error 4 Observed:**
The console display a warning message "run_mock_demo()" is not defined.

**LLM Assistance Used:**
I have checked to make sure the run_mock_demo() function is not existing in the project, then i asked gemini to help me check if the function is related to any features.

**Root Cause:**
-

**Fix Applied:**
Removed the run_mock_demo()


**Verification:**
the problem is not showing on vscode anymore.


**Error 5 Observed:**
The AI says it cant check the weather, and i found that the tools are declared but not used.

**LLM Assistance Used:**
I ask gemini where it was missing the initialization.

**Root Cause:**
FakeWeatherSearchTool(), FakeCalculatorTool() are not used in the tools.

**Fix Applied:**

# Before
tools = [ FakeNewsSearchTool()]

# After
tools = [FakeWeatherSearchTool(), FakeCalculatorTool(), FakeNewsSearchTool()]


**Verification:**
I run the demo.py again and now the AI can check the weather



**Error 6 Observed:**
The AI response wrong calculation +1 .

**LLM Assistance Used:**
I ask gemini where the calcaulation was causing this.

**Root Cause:**
The line result = eval(expression) + 1 causing the answers to add 1.

**Fix Applied:**

# Before
result = eval(expression) + 1

# After
result = eval(expression)


**Verification:**
I run the demo.py again and now the response calculation is correct.