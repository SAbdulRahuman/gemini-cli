# Complete gemini-cli rewrite from scratch feature by feature

## Chapter 1: Introduction to gemini-cli

Welcome to "Complete gemini-cli rewrite from scratch feature by feature"! In this book, we will embark on a journey to build a powerful command-line interface (CLI) tool called `gemini-cli`. This tool brings the capabilities of Google's Gemini models directly to your terminal, providing a seamless and efficient way to interact with AI.

### What is gemini-cli?

Gemini-cli is an open-source AI agent that allows you to leverage the power of Gemini from your command line. It's designed for developers, system administrators, and anyone who prefers working in a terminal environment. With `gemini-cli`, you can perform a wide range of tasks, from generating code and answering questions to automating complex workflows.

### Key Features

The `gemini-cli` tool is packed with features that make it a versatile and powerful assistant for your daily tasks. Here are some of its key capabilities:

*   **Powerful AI Models:** Access to Google's state-of-the-art Gemini models, including Gemini 2.5 Pro with its massive 1 million token context window.
*   **Built-in Tools:** The `gemini-cli` comes with a set of built-in tools that allow it to interact with your local environment. These tools include:
    *   **Google Search:** Ground your queries with real-time information from the web.
    *   **File System Operations:** Read, write, and list files on your local machine.
    *   **Shell Commands:** Execute shell commands and scripts.
    *   **Web Fetching:** Fetch content from web pages.
*   **Extensibility:** The `gemini-cli` is designed to be extensible. You can add your own custom tools and integrations using the Model Context Protocol (MCP).
*   **Terminal-First Design:** The entire user experience is optimized for the command line, with features like keyboard shortcuts, themes, and a rich interactive interface.
*   **Open Source:** The `gemini-cli` is open source and licensed under the Apache 2.0 license, which means you can freely use, modify, and distribute it.

### Architecture Overview

The `gemini-cli` is built with a modular architecture that separates the user-facing components from the core logic. This design makes the codebase easier to understand, maintain, and extend. The main components are:

*   **CLI Package (`packages/cli`):** This is the frontend of the application. It's responsible for rendering the user interface, handling user input, and managing the overall user experience in the terminal.
*   **Core Package (`packages/core`):** This is the backend of the application. It communicates with the Gemini API, orchestrates the execution of tools, and manages the state of the conversation.
*   **Tools (`packages/core/src/tools`):** These are individual modules that provide the `gemini-cli` with its special capabilities, such as interacting with the file system or executing shell commands.

In the upcoming chapters, we will dive deep into each of these components and build them from scratch. By the end of this book, you will have a fully functional `gemini-cli` tool and a thorough understanding of its inner workings.

Let's get started!

## Chapter 2: Setting Up the Monorepo

Now that we have a high-level understanding of `gemini-cli`, it's time to start building it. The first step is to set up our project structure. We will be using a monorepo architecture, which is a single repository that contains multiple packages.

### Why a Monorepo?

A monorepo is a great choice for a project like `gemini-cli` for several reasons:

*   **Code Sharing:** It's easy to share code between the `cli` and `core` packages without having to publish them to a package registry.
*   **Simplified Dependency Management:** All dependencies are managed in a single `package.json` file at the root of the project, which helps to avoid version conflicts.
*   **Atomic Commits:** Changes to multiple packages can be made in a single commit, which makes it easier to track the history of the project.
*   **Streamlined Development:** You can run tests and build all packages with a single command.

We will use npm workspaces to manage our monorepo. Workspaces are a feature of npm that makes it easy to work with multiple packages in a single repository.

### Project Structure

Let's create the basic directory structure for our project.

```bash
mkdir gemini-cli
cd gemini-cli
mkdir packages
touch package.json
```

After running these commands, your project directory should look like this:

```
gemini-cli/
├── packages/
└── package.json
```

The `packages` directory is where we will create our `cli` and `core` packages in the upcoming chapters.

### The Root `package.json`

Now, let's add the following content to the `package.json` file at the root of our project:

```json
{
  "name": "gemini-cli-book",
  "version": "1.0.0",
  "private": true,
  "workspaces": [
    "packages/*"
  ],
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

Let's break down the important parts of this file:

*   `"private": true`: This is important because it prevents the root project from being accidentally published to npm.
*   `"workspaces": ["packages/*"]`: This tells npm that our workspaces are located in the `packages` directory. Any subdirectory in `packages` that contains a `package.json` file will be treated as a workspace.

With our monorepo set up, we are now ready to start adding our packages. In the next chapter, we will add the essential dependencies and scripts that we will need for our project.

## Chapter 3: Essential Dependencies and Scripts

Now that we have our monorepo structure in place, it's time to add the essential development dependencies that we will need to build and test our project. We will also set up some basic scripts in our root `package.json` to streamline our development workflow.

### Core Development Dependencies

We will be using the following core dependencies in our project:

*   **TypeScript:** A strongly typed superset of JavaScript that will help us write more robust and maintainable code.
*   **esbuild:** A super-fast JavaScript bundler and minifier that we will use to build our project.
*   **Vitest:** A blazing-fast unit test framework that is compatible with Vite projects, but works great for any project.
*   **tsx:** A lightweight and fast command-line tool that allows you to run TypeScript files directly without pre-compiling them.

Let's install these dependencies as development dependencies in our root project:

```bash
npm install -D typescript esbuild vitest tsx @types/node
```

After running this command, your `package.json` file will be updated with a `devDependencies` section.

### Basic Build and Test Scripts

Now, let's add some basic scripts to our root `package.json` to make it easy to build and test our project.

```json
{
  "name": "gemini-cli-book",
  "version": "1.0.0",
  "private": true,
  "workspaces": [
    "packages/*"
  ],
  "scripts": {
    "build": "npm run build --workspaces --if-present",
    "test": "vitest run",
    "test:watch": "vitest"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@types/node": "^20.12.12",
    "esbuild": "^0.20.2",
    "tsx": "^4.9.3",
    "typescript": "^5.4.5",
    "vitest": "^1.6.0"
  }
}
```

Here's what our new scripts do:

*   `"build": "npm run build --workspaces --if-present"`: This script will run the `build` script in each of our workspaces (if it exists). This allows us to build all of our packages with a single command.
*   `"test": "vitest run"`: This script will run all of our tests once.
*   `"test:watch": "vitest"`: This script will run our tests in watch mode, which will automatically re-run the tests whenever a file changes.

### TypeScript Configuration

Finally, let's create a `tsconfig.json` file at the root of our project to configure the TypeScript compiler.

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "dist"
  },
  "include": ["packages/**/*.ts"]
}
```

Let's break down some of the key properties in this file:

*   `"target": "ES2022"`: This tells TypeScript to compile our code to the ES2022 version of JavaScript.
*   `"module": "ESNext"`: This tells TypeScript to use the latest ECMAScript module syntax.
*   `"moduleResolution": "node"`: This tells TypeScript to use the Node.js module resolution algorithm.
*   `"strict": true`: This enables all strict type-checking options.
*   `"outDir": "dist"`: This specifies the output directory for the compiled JavaScript files.
*   `"include": ["packages/**/*.ts"]`: This tells TypeScript to only include files with the `.ts` extension in the `packages` directory.

With our essential dependencies and scripts in place, we are now ready to create our first package. In the next chapter, we will take a deeper look at the core architectural principles of `gemini-cli`.

## Chapter 4: Core Architectural Principles

Before we start writing code for our packages, it's important to have a solid understanding of the architectural principles that guide the development of `gemini-cli`. This chapter will take a deeper dive into the modular architecture, the roles of the `cli` and `core` packages, and the interaction flow between them.

### The Core Components

As we mentioned in Chapter 1, `gemini-cli` is composed of three main components:

1.  **The CLI Package (`packages/cli`):** This is the user-facing part of our application. It's responsible for everything the user sees and interacts with in the terminal. This includes:
    *   Reading user input from the command line.
    *   Parsing and handling slash commands (e.g., `/help`, `/chat`).
    *   Rendering the chat interface, including messages from the user and Gemini.
    *   Managing the user's command history.
    *   Applying themes and custom styles to the UI.

2.  **The Core Package (`packages/core`):** This is the engine of our application. It acts as a bridge between the CLI and the Gemini API, and it's responsible for the core logic of our tool. This includes:
    *   Communicating with the Google Gemini API.
    *   Constructing prompts to send to the model, including context from the conversation history.
    *   Managing the execution of tools requested by the model.
    *   Handling the state of the conversation.

3.  **The Tools (`packages/core/src/tools`):** These are individual modules that give Gemini the ability to interact with the world outside of the chat. Each tool provides a specific capability, such as:
    *   Reading or writing files on the local file system.
    *   Executing shell commands.
    *   Fetching content from a URL.
    *   Searching the web.

### The Interaction Flow

Understanding how these components interact with each other is key to understanding the architecture of `gemini-cli`. Here is a step-by-step breakdown of a typical interaction:

```
+------+   1. Input   +-------------+   2. Request   +--------------+
| User |------------->| CLI Package |--------------->| Core Package |
+------+   (Prompt)   +-------------+                +--------------+
   ^                                                        | 3. API Call
   | 8. Display                                             |
   |    Response                                            v
   |                                                  +------------+
   |                                                  | Gemini API |
   +--------------------------------------------------+------------+
   | 7. Final                                               | 4. Tool
   |    Response                                             |    Request
   |                                                        v
   |                                                  +--------------+
   |                                                  | Core Package |
   |                                                  +--------------+
   |                                                        | 5. Execute
   |                                                        |    Tool
   | 6. Tool                                                v
   |    Result                                       +------------+
   +--------------------------------------------------|   Tools    |
                                                      +------------+
```

1.  **User Input:** The user types a prompt in the terminal.
2.  **Request to Core:** The `cli` package takes the user's input and sends it to the `core` package.
3.  **API Call:** The `core` package constructs a prompt, including any relevant history or context, and sends it to the Gemini API.
4.  **Tool Request:** The Gemini API might respond with a request to use a tool (e.g., "read the contents of `README.md`").
5.  **Execute Tool:** The `core` package receives the tool request. If the tool is potentially dangerous (e.g., writing a file or running a shell command), it will first ask the user for confirmation via the `cli` package. Once confirmed, the `core` package executes the tool.
6.  **Tool Result:** The result of the tool execution is sent back to the `core` package.
7.  **Final Response:** The `core` package sends the tool result back to the Gemini API, which then generates a final response for the user. This response is sent back to the `core` package, which then forwards it to the `cli` package.
8.  **Display Response:** The `cli` package formats the final response and displays it to the user in the terminal.

### Key Design Principles

The architecture of `gemini-cli` is guided by a few key design principles:

*   **Modularity:** By separating the `cli` (frontend) from the `core` (backend), we can work on them independently. This also opens the door for other frontends to use the same core logic in the future.
*   **Extensibility:** The tool system is designed to be easily extensible. We can add new capabilities to `gemini-cli` by simply creating new tool modules.
*   **User Experience:** The `cli` package is focused on providing a rich and interactive experience for the user, making it a pleasure to use from the command line.

Now that we have a solid understanding of the architecture, we can start building our first package. In the next chapter, we will create the `core` package and set up its basic structure.

## Part 2: The Core Package - The Heart of gemini-cli

## Chapter 5: Creating the Core Package

Welcome to Part 2 of our book! In this part, we will be building the `core` package, which is the heart of `gemini-cli`. This package is responsible for all the heavy lifting, including communicating with the Gemini API and executing tools.

Let's get started by creating the basic structure for our `core` package.

### Directory Structure

First, let's create a new directory called `core` inside our `packages` directory. Then, inside the `core` directory, we'll create a `src` directory and a `package.json` file.

```bash
mkdir packages/core
mkdir packages/core/src
touch packages/core/src/index.ts
touch packages/core/package.json
```

After running these commands, your `packages` directory should look like this:

```
packages/
└── core/
    ├── src/
    │   └── index.ts
    └── package.json
```

The `src` directory is where we will put all of our source code for the `core` package. The `index.ts` file will be the main entry point for our package.

### The `package.json` for the Core Package

Now, let's add the following content to the `packages/core/package.json` file:

```json
{
  "name": "@gemini-cli-book/core",
  "version": "1.0.0",
  "type": "module",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "npx esbuild src/index.ts --bundle --platform=node --format=esm --outfile=dist/index.js",
    "test": "vitest run",
    "typecheck": "tsc --noEmit"
  },
  "license": "ISC"
}
```

Let's break down the important parts of this file:

*   `"name": "@gemini-cli-book/core"`: We are using an npm scope (`@gemini-cli-book`) to namespace our package. This is a good practice to avoid naming conflicts with other packages.
*   `"type": "module"`: This tells Node.js to treat the `.js` files in this package as ES modules.
*   `"main": "dist/index.js"`: This is the main entry point for our package. When another package imports `@gemini-cli-book/core`, this is the file that will be imported.
*   `"types": "dist/index.d.ts"`: This file will contain the TypeScript type definitions for our package.
*   `"scripts"`: We have added a `build` script that uses `esbuild` to compile our `src/index.ts` file into a single JavaScript file in the `dist` directory. We also have `test` and `typecheck` scripts.

### The Entry Point (`index.ts`)

Finally, let's add a simple placeholder function to our `packages/core/src/index.ts` file.

```typescript
export function Greet(name: string): string {
    return `Hello, ${name}!`;
}
```

This simple function will allow us to test that our `core` package is set up correctly and can be imported by other packages.

Now that we have our `core` package set up, we can start adding the core logic. In the next chapter, we will implement the API client to communicate with the Gemini API.

## Chapter 6: Communicating with the Gemini API

Now that our `core` package is set up, it's time to add the most important piece of functionality: communicating with the Google Gemini API. This will allow us to send prompts to the model and receive responses.

### Adding the Gemini API Client Library

Google provides an official Node.js client library for the Gemini API, which makes it easy to interact with the service. Let's add the `@google/generative-ai` package to our `core` package.

Run the following command from the root of your project:

```bash
npm install @google/generative-ai -w @gemini-cli-book/core
```

The `-w` flag tells npm to install the dependency in the `@gemini-cli-book/core` workspace.

### Creating the API Client

Now, let's create a new file called `api-client.ts` in the `packages/core/src` directory. This file will contain the logic for sending requests to the Gemini API.

```bash
touch packages/core/src/api-client.ts
```

Add the following content to the new `api-client.ts` file:

```typescript
import { GoogleGenerativeAI } from "@google/generative-ai";

// Get the API key from the environment variables
const apiKey = process.env.GEMINI_API_KEY;

if (!apiKey) {
  throw new Error("GEMINI_API_KEY environment variable not set");
}

// Initialize the GoogleGenerativeAI client with the API key
const genAI = new GoogleGenerativeAI(apiKey);

// Function to send a prompt to the Gemini API and get a response
export async function sendPrompt(prompt: string): Promise<string> {
  // For text-only input, use the gemini-pro model
  const model = genAI.getGenerativeModel({ model: "gemini-pro" });

  const result = await model.generateContent(prompt);
  const response = await result.response;
  const text = response.text();
  return text;
}
```

Let's break down what's happening in this file:

1.  We import the `GoogleGenerativeAI` class from the `@google/generative-ai` package.
2.  We get the Gemini API key from the environment variables. For now, we'll assume that the user has set the `GEMINI_API_KEY` environment variable. We'll cover more advanced authentication methods in a later chapter.
3.  We create a new instance of the `GoogleGenerativeAI` client with our API key.
4.  We create a `sendPrompt` function that takes a prompt as a string and returns a promise that resolves with the model's response.
5.  Inside the `sendPrompt` function, we get the `gemini-pro` model, which is suitable for text-only prompts.
6.  We call the `generateContent` method on the model with our prompt.
7.  We get the response from the result and return the text content.

### Updating the Entry Point

Now, let's update our `packages/core/src/index.ts` file to export the `sendPrompt` function.

```typescript
export { sendPrompt } from "./api-client";
```

### Testing the API Client

To test our new API client, you will need to get a Gemini API key from [Google AI Studio](https://aistudio.google.com/apikey).

Once you have your API key, you can test the `sendPrompt` function by creating a temporary test file. For example, you could create a `test.ts` file in the root of your project:

```typescript
import { sendPrompt } from './packages/core/src/index';

// Set your API key as an environment variable
process.env.GEMINI_API_KEY = 'YOUR_API_KEY';

async function main() {
    const response = await sendPrompt("Hello, world!");
    console.log(response);
}

main();
```

You can run this file using `tsx`:

```bash
npx tsx test.ts
```

If everything is set up correctly, you should see a response from the Gemini API in your terminal.

In the next chapter, we will build on this by creating a more sophisticated system for managing prompts and conversation history.

## Chapter 7: Managing Prompts and Conversation History

In the previous chapter, we created a simple function to send a single prompt to the Gemini API. However, to build a true chatbot, we need to be able to have a conversation. This means that the model needs to remember the previous messages in the conversation and use them as context for generating the next response.

In this chapter, we will refactor our API client to support conversational chat and manage conversation history.

### The Importance of Conversation History

Conversation history is crucial for a chatbot because it provides context. Without it, the model would treat every prompt as a new, standalone question. By including the history, we allow the model to understand the flow of the conversation, refer to previous messages, and provide more relevant and coherent responses.

The `@google/generative-ai` library makes it easy to manage conversation history by providing a `ChatSession` object that automatically handles it for us.

### Refactoring the API Client for Chat

Let's create a new file called `chat-manager.ts` in the `packages/core/src` directory. This file will contain a `ChatManager` class that will be responsible for managing chat sessions.

```bash
touch packages/core/src/chat-manager.ts
```

Now, add the following content to the new `chat-manager.ts` file:

```typescript
import { GoogleGenerativeAI, ChatSession } from "@google/generative-ai";

export class ChatManager {
  private chatSession: ChatSession;

  constructor(apiKey: string) {
    if (!apiKey) {
      throw new Error("GEMINI_API_KEY not provided");
    }

    const genAI = new GoogleGenerativeAI(apiKey);
    const model = genAI.getGenerativeModel({ model: "gemini-pro" });

    // Initialize the chat session with an empty history
    this.chatSession = model.startChat({
      history: [],
    });
  }

  // Send a message to the chat session and get a response
  public async sendMessage(prompt: string): Promise<string> {
    const result = await this.chatSession.sendMessage(prompt);
    const response = await result.response;
    const text = response.text();
    return text;
  }
}
```

Let's break down the `ChatManager` class:

1.  It has a private property `chatSession` which will hold our chat session.
2.  The `constructor` takes an API key and initializes the `GoogleGenerativeAI` client, just like before.
3.  Instead of calling `generateContent`, we now call `model.startChat()` to create a new `ChatSession`. We can optionally provide an initial history.
4.  The `sendMessage` method now uses `this.chatSession.sendMessage(prompt)` to send the user's message. The library automatically appends the user's message and the model's response to the chat history.

### Updating the Entry Point

Now, let's update our `packages/core/src/index.ts` file to export the `ChatManager` class. We can remove the old `sendPrompt` export.

```typescript
export { ChatManager } from "./chat-manager";
```

### Testing the Chat Manager

Let's update our `test.ts` file in the root of the project to test our new `ChatManager`.

```typescript
import { ChatManager } from './packages/core/src/index';

// Set your API key as an environment variable
process.env.GEMINI_API_KEY = 'YOUR_API_KEY';

async function main() {
    const chat = new ChatManager(process.env.GEMINI_API_KEY!);

    console.log("You: What is the capital of France?");
    const response1 = await chat.sendMessage("What is the capital of France?");
    console.log("Gemini:", response1);

    console.log("\nYou: What about its population?");
    const response2 = await chat.sendMessage("What about its population?");
    console.log("Gemini:", response2);
}

main();
```

Now, run the test file:

```bash
npx tsx test.ts
```

You should see a conversation where Gemini first answers the capital of France and then, in the second question, it understands that "its" refers to Paris. This demonstrates that the conversation history is working correctly.

In the next chapter, we will start exploring the concept of tools and how they can be used to extend the capabilities of our chatbot.

## Chapter 8: Introduction to Tools

One of the most powerful features of the Gemini models is the ability to use "tools". Tools are external functions that the model can call to get information or perform actions that it can't do on its own. This allows us to extend the capabilities of our chatbot far beyond simple text generation.

In this chapter, we will introduce the concept of tools and build a simple calculator tool that the model can use to perform mathematical calculations.

### What are Tools?

Imagine you ask a chatbot, "What is the square root of 256?". The model might know the answer from its training data, but for more complex calculations, it would be much more reliable to use a real calculator. Tools allow us to do just that.

When we provide the model with a set of tools, we are giving it a list of functions that it can ask to be called. If the model determines that it needs to use a tool to answer a user's prompt, it will return a "tool call" request instead of a text response. Our application can then execute the tool and send the result back to the model, which will then use that result to generate a final answer.

### Creating a Simple Calculator Tool

Let's create a simple calculator tool that can add two numbers. First, create a new file called `tools.ts` in the `packages/core/src` directory.

```bash
touch packages/core/src/tools.ts
```

Now, add the following content to the new `tools.ts` file:

```typescript
import { FunctionDeclarationsTool } from "@google/generative-ai";

// Define the calculator tool
export const calculatorTool: FunctionDeclarationsTool = {
  functionDeclarations: [
    {
      name: "add",
      description: "Adds two numbers.",
      parameters: {
        type: "OBJECT",
        properties: {
          a: { type: "NUMBER" },
          b: { type: "NUMBER" },
        },
        required: ["a", "b"],
      },
    },
  ],
};

// The actual implementation of the add function
export function add(a: number, b: number): number {
  return a + b;
}
```

In this file, we have defined a `calculatorTool` object. This object contains a `functionDeclarations` array, which is where we define the tools that we want to make available to the model. Each tool has a `name`, a `description`, and a `parameters` object that defines the arguments that the tool expects.

We have also defined the actual `add` function that will be executed when the model requests to use the "add" tool.

### Updating the Chat Manager to Use Tools

Now, let's update our `ChatManager` to be aware of our new calculator tool. We need to modify the `chat-manager.ts` file to handle tool calls.

```typescript
import { GoogleGenerativeAI, ChatSession, FunctionDeclarationsTool } from "@google/generative-ai";
import { calculatorTool, add } from "./tools";

export class ChatManager {
  private chatSession: ChatSession;

  constructor(apiKey: string) {
    if (!apiKey) {
      throw new Error("GEMINI_API_KEY not provided");
    }

    const genAI = new GoogleGenerativeAI(apiKey);
    // Pass the tool definitions to the model
    const model = genAI.getGenerativeModel({
      model: "gemini-pro",
      tools: [calculatorTool],
    });

    this.chatSession = model.startChat({
      history: [],
    });
  }

  public async sendMessage(prompt: string): Promise<string> {
    const result = await this.chatSession.sendMessage(prompt);
    const response = await result.response;

    const functionCalls = response.functionCalls();
    if (functionCalls && functionCalls.length > 0) {
        // The model has requested to use a tool
        const call = functionCalls[0];
        if (call.name === "add") {
            const { a, b } = call.args;
            const toolResult = add(a, b);

            // Send the tool result back to the model
            const toolResponse = await this.chatSession.sendMessage(JSON.stringify({
                toolResponse: {
                    name: "add",
                    result: toolResult,
                }
            }));
            return toolResponse.response.text();
        }
    }

    return response.text();
  }
}
```

Here are the key changes we've made to the `ChatManager`:

1.  We now pass our `calculatorTool` to the `getGenerativeModel` method. This tells the model that the "add" tool is available.
2.  In the `sendMessage` method, we check if the response from the model contains any `functionCalls`.
3.  If there is a function call for our "add" tool, we execute our `add` function with the arguments provided by the model.
4.  We then send the result of the tool execution back to the model by calling `sendMessage` again with the tool response.
5.  The model will then use the tool result to generate a final answer, which we return to the user.

### Testing the Calculator Tool

Let's update our `test.ts` file to test our new calculator tool.

```typescript
import { ChatManager } from './packages/core/src/index';

// Set your API key as an environment variable
process.env.GEMINI_API_KEY = 'YOUR_API_KEY';

async function main() {
    const chat = new ChatManager(process.env.GEMINI_API_KEY!);

    console.log("You: What is 123 + 456?");
    const response = await chat.sendMessage("What is 123 + 456?");
    console.log("Gemini:", response);
}

main();
```

When you run this test, you will see that the model is able to correctly answer the question by using our "add" tool.

In the next chapter, we will build a more robust system for managing and registering multiple tools.

## Chapter 9: The Tool Registry

In the previous chapter, we introduced tools and created a simple calculator tool. However, our implementation in `ChatManager` was not very scalable. If we wanted to add more tools, we would have to add more `if/else` statements to handle each one.

In this chapter, we will create a `ToolRegistry` class that will allow us to register and manage multiple tools in a more organized and scalable way.

### The Need for a Tool Registry

A tool registry is a central place to manage all of the tools that are available to our chatbot. It will be responsible for:

*   Storing the definitions of all our tools.
*   Storing the implementations of all our tools.
*   Providing the tool definitions to the model.
*   Executing the correct tool when requested by the model.

By using a tool registry, we can keep our `ChatManager` clean and focused on its core responsibility of managing the conversation.

### Creating the Tool Registry

Let's create a new file called `tool-registry.ts` in the `packages/core/src` directory.

```bash
touch packages/core/src/tool-registry.ts
```

Now, add the following content to the new `tool-registry.ts` file:

```typescript
import { FunctionDeclarationsTool } from "@google/generative-ai";

// A simple interface for our tool implementations
interface ToolImplementation {
  (args: any): any;
}

export class ToolRegistry {
  private toolImplementations: Map<string, ToolImplementation> = new Map();
  private toolDefinitions: FunctionDeclarationsTool[] = [];

  // Register a new tool
  public register(toolDefinition: FunctionDeclarationsTool, implementation: ToolImplementation) {
    const functionName = toolDefinition.functionDeclarations[0].name;
    this.toolImplementations.set(functionName, implementation);
    this.toolDefinitions.push(toolDefinition);
  }

  // Get all tool definitions to send to the model
  public getToolDefinitions(): FunctionDeclarationsTool[] {
    return this.toolDefinitions;
  }

  // Execute a tool by name
  public async executeTool(name: string, args: any): Promise<any> {
    const implementation = this.toolImplementations.get(name);
    if (!implementation) {
      throw new Error(`Tool not found: ${name}`);
    }
    return implementation(args);
  }
}
```

Let's break down the `ToolRegistry` class:

1.  It has two private properties: `toolImplementations` to store the functions that execute our tools, and `toolDefinitions` to store the JSON schemas that we send to the model.
2.  The `register` method takes a tool definition and its implementation, and stores them in our maps.
3.  The `getToolDefinitions` method returns all of the tool definitions, which we will pass to the model.
4.  The `executeTool` method takes the name of a tool and its arguments, finds the corresponding implementation, and executes it.

### Updating the Chat Manager

Now, let's refactor our `ChatManager` to use the new `ToolRegistry`. We will update the `chat-manager.ts` file.

```typescript
import { GoogleGenerativeAI, ChatSession } from "@google/generative-ai";
import { ToolRegistry } from "./tool-registry";

export class ChatManager {
  private chatSession: ChatSession;
  private toolRegistry: ToolRegistry;

  constructor(apiKey: string, toolRegistry: ToolRegistry) {
    if (!apiKey) {
      throw new Error("GEMINI_API_KEY not provided");
    }
    this.toolRegistry = toolRegistry;

    const genAI = new GoogleGenerativeAI(apiKey);
    const model = genAI.getGenerativeModel({
      model: "gemini-pro",
      tools: this.toolRegistry.getToolDefinitions(),
    });

    this.chatSession = model.startChat({
      history: [],
    });
  }

  public async sendMessage(prompt: string): Promise<string> {
    const result = await this.chatSession.sendMessage(prompt);
    const response = await result.response;

    const functionCalls = response.functionCalls();
    if (functionCalls && functionCalls.length > 0) {
        const call = functionCalls[0];
        const toolResult = await this.toolRegistry.executeTool(call.name, call.args);

        const toolResponse = await this.chatSession.sendMessage(JSON.stringify({
            toolResponse: {
                name: call.name,
                result: toolResult,
            }
        }));
        return toolResponse.response.text();
    }

    return response.text();
  }
}
```

Our `ChatManager` is now much cleaner. It takes a `ToolRegistry` in its constructor and uses it to get the tool definitions and execute tool calls. The `if/else` block is gone, and we can now add new tools without having to modify the `ChatManager`.

### Updating the Entry Point and Test

Let's update our `packages/core/src/index.ts` to export the `ToolRegistry`.

```typescript
export { ChatManager } from "./chat-manager";
export { ToolRegistry } from "./tool-registry";
```

And finally, let's update our `test.ts` file to use the new `ToolRegistry`.

```typescript
import { ChatManager, ToolRegistry } from './packages/core/src/index';
import { calculatorTool, add } from './packages/core/src/tools';

// Set your API key as an environment variable
process.env.GEMINI_API_KEY = 'YOUR_API_KEY';

async function main() {
    // Create a new tool registry and register our calculator tool
    const toolRegistry = new ToolRegistry();
    toolRegistry.register(calculatorTool, (args) => add(args.a, args.b));

    const chat = new ChatManager(process.env.GEMINI_API_KEY!, toolRegistry);

    console.log("You: What is 123 + 456?");
    const response = await chat.sendMessage("What is 123 + 456?");
    console.log("Gemini:", response);
}

main();
```

Now, when you run the test, it should work just like before. But now, we have a much more scalable system for managing our tools.

In the next chapter, we will continue to build out our `core` package by adding state management.

## Chapter 10: State Management

So far, we have built a `ChatManager` that can hold a conversation and use tools. The state of the conversation, which is the history of all the messages, is managed automatically by the `ChatSession` object from the Gemini API library.

In this chapter, we will explore how to manage this state more explicitly. We will add methods to our `ChatManager` to get the conversation history and to reset the conversation.

### What is Session State?

Session state refers to all the information that is stored about a particular conversation. In our case, the most important piece of state is the conversation history. This history is what gives the model context and allows it to have a coherent conversation.

Being able to manage this state is important for several reasons:

*   **Debugging:** It can be useful to inspect the history of a conversation to see what the model is seeing.
*   **Resetting:** Sometimes, you might want to start a new conversation from scratch without having to create a new `ChatManager` instance.
*   **Persisting:** In a more advanced application, you might want to save the history of a conversation and resume it later.

### Adding State Management Methods to ChatManager

Let's add two new methods to our `ChatManager` class in `packages/core/src/chat-manager.ts`: `getHistory` and `reset`.

```typescript
import { GoogleGenerativeAI, ChatSession, Content, GenerativeModel } from "@google/generative-ai";
import { ToolRegistry } from "./tool-registry";

export class ChatManager {
  private chatSession: ChatSession;
  private toolRegistry: ToolRegistry;
  private model: GenerativeModel;
  private apiKey: string;

  constructor(apiKey: string, toolRegistry: ToolRegistry) {
    if (!apiKey) {
      throw new Error("GEMINI_API_KEY not provided");
    }
    this.apiKey = apiKey;
    this.toolRegistry = toolRegistry;

    const genAI = new GoogleGenerativeAI(apiKey);
    this.model = genAI.getGenerativeModel({
      model: "gemini-pro",
      tools: this.toolRegistry.getToolDefinitions(),
    });

    this.chatSession = this.model.startChat({
      history: [],
    });
  }

  public async sendMessage(prompt: string): Promise<string> {
    // ... (sendMessage implementation remains the same)
    const result = await this.chatSession.sendMessage(prompt);
    const response = await result.response;

    const functionCalls = response.functionCalls();
    if (functionCalls && functionCalls.length > 0) {
        const call = functionCalls[0];
        const toolResult = await this.toolRegistry.executeTool(call.name, call.args);

        const toolResponse = await this.chatSession.sendMessage(JSON.stringify({
            toolResponse: {
                name: call.name,
                result: toolResult,
            }
        }));
        return toolResponse.response.text();
    }

    return response.text();
  }

  // Get the history of the conversation
  public async getHistory(): Promise<Content[]> {
    return this.chatSession.getHistory();
  }

  // Reset the conversation history
  public reset(): void {
    this.chatSession = this.model.startChat({
      history: [],
    });
  }
}
```

We have added two new methods:

*   `getHistory()`: This method calls `this.chatSession.getHistory()` to retrieve the full history of the conversation, including the user's messages, the model's responses, and any tool calls.
*   `reset()`: This method creates a new `ChatSession` with an empty history, effectively resetting the conversation.

We also stored the `model` and `apiKey` as private properties so that we can use them in the `reset` method.

### Testing the State Management Methods

Let's update our `test.ts` file to test our new state management methods.

```typescript
import { ChatManager, ToolRegistry } from './packages/core/src/index';

// Set your API key as an environment variable
process.env.GEMINI_API_KEY = 'YOUR_API_KEY';

async function main() {
    const toolRegistry = new ToolRegistry();
    const chat = new ChatManager(process.env.GEMINI_API_KEY!, toolRegistry);

    console.log("You: What is the capital of France?");
    const response1 = await chat.sendMessage("What is the capital of France?");
    console.log("Gemini:", response1);

    console.log("\n--- History ---");
    const history = await chat.getHistory();
    console.log(JSON.stringify(history, null, 2));

    console.log("\n--- Resetting Conversation ---");
    chat.reset();

    console.log("\nYou: What about its population?");
    const response2 = await chat.sendMessage("What about its population?");
    console.log("Gemini:", response2);
}

main();
```

When you run this test, you will see the following:

1.  The model will answer the first question about the capital of France.
2.  We will then print the conversation history, which will show the user's question and the model's response.
3.  We then reset the conversation.
4.  When we ask the second question, "What about its population?", the model will not know what "its" refers to, because the history has been cleared.

This demonstrates that our state management methods are working correctly.

This concludes Part 2 of our book. We have now built a robust `core` package that can communicate with the Gemini API, use tools, and manage conversation state. In Part 3, we will start building the user-facing `cli` package.

## Part 3: The CLI Package - The User Interface

## Chapter 11: Creating the CLI Package

Welcome to Part 3! Now that we have a fully functional `core` package, it's time to build the user interface for our `gemini-cli` tool. In this part, we will create the `cli` package, which will be responsible for everything the user sees and interacts with in the terminal.

We will be using a library called [Ink](https://github.com/vadimdemedes/ink) to build our command-line interface. Ink is a powerful tool that allows you to build CLIs using React, which means we can use components, hooks, and all the other great features of React to build our UI.

### Directory Structure

Let's start by creating the directory structure for our `cli` package.

```bash
mkdir packages/cli
mkdir packages/cli/src
touch packages/cli/src/index.ts
touch packages/cli/package.json
```

Your `packages` directory should now look like this:

```
packages/
├── core/
│   ├── ...
└── cli/
    ├── src/
    │   └── index.ts
    └── package.json
```

### The `package.json` for the CLI Package

Now, let's add the following content to the `packages/cli/package.json` file:

```json
{
  "name": "@gemini-cli-book/cli",
  "version": "1.0.0",
  "type": "module",
  "main": "dist/index.js",
  "bin": {
    "gemini-cli-book": "dist/index.js"
  },
  "scripts": {
    "build": "npx esbuild src/index.ts --bundle --platform=node --format=esm --outfile=dist/index.js",
    "start": "node dist/index.js"
  },
  "dependencies": {
    "@gemini-cli-book/core": "1.0.0",
    "ink": "^5.0.1",
    "react": "^18.3.1"
  },
  "devDependencies": {
    "@types/react": "^18.3.5"
  },
  "license": "ISC"
}
```

Let's break down the important parts of this file:

*   `"name": "@gemini-cli-book/cli"`: We are using the same npm scope as our `core` package.
*   `"bin": { "gemini-cli-book": "dist/index.js" }`: This is a very important field. It tells npm to create a symbolic link to our `dist/index.js` file in the `node_modules/.bin` directory when our package is installed. This is what will allow us to run our CLI by simply typing `gemini-cli-book` in the terminal.
*   `"dependencies"`: We have added dependencies on `@gemini-cli-book/core`, `ink`, and `react`. The `"@gemini-cli-book/core": "1.0.0"` line tells npm to use the local `core` package from our workspace.
*   `"scripts"`: We have a `build` script to compile our code and a `start` script to run our CLI.

### The Entry Point (`index.ts`)

Finally, let's add a simple "Hello, World" example to our `packages/cli/src/index.ts` file to test that Ink is set up correctly.

```typescript
import React from 'react';
import { render, Text } from 'ink';

const App = () => (
    <Text>Hello, World!</Text>
);

render(<App />);
```

This is a simple React component that renders the text "Hello, World!". The `render` function from Ink is what renders our React component to the terminal.

### Running the CLI

To run our new CLI, we first need to install the dependencies and link our workspaces. Run the following command from the root of your project:

```bash
npm install
```

This will install all the dependencies for both the `core` and `cli` packages and set up the symbolic links for our workspaces.

Now, we can build and run our `cli` package:

```bash
npm run build -w @gemini-cli-book/cli
npm run start -w @gemini-cli-book/cli
```

You should see "Hello, World!" printed to your terminal.

In the next chapter, we will build the main application component and set up the basic structure for our interactive UI.

## Chapter 12: Building the Interactive UI with React Ink

Now that we have our `cli` package set up and a basic "Hello, World" example running, it's time to build the core of our interactive user interface. In this chapter, we will create the main application component, set up the structure for displaying messages, and create an input prompt for the user.

### Structuring the UI

Our UI will be composed of three main components:

*   **`App`:** The main component that will hold the state of our application and orchestrate the other components.
*   **`Messages`:** A component that will be responsible for rendering the list of messages in the conversation.
*   **`Input`:** A component that will provide a text input for the user to type their prompts.

Let's start by installing the `ink-text-input` package, which we will need for our `Input` component.

```bash
npm install ink-text-input -w @gemini-cli-book/cli
```

### Creating the `App` Component

First, let's rename our `packages/cli/src/index.ts` file to `index.tsx` to indicate that it will contain JSX code. Then, create a new file called `App.tsx` in the `packages/cli/src` directory.

```bash
mv packages/cli/src/index.ts packages/cli/src/index.tsx
touch packages/cli/src/App.tsx
```

Update `packages/cli/src/index.tsx` to simply render our new `App` component:

```typescript
import React from 'react';
import { render } from 'ink';
import App from './App';

render(<App />);
```

Now, let's add the basic structure for our `App` component in `packages/cli/src/App.tsx`:

```typescript
import React, { useState, useEffect } from 'react';
import { Box, Text } from 'ink';
import TextInput from 'ink-text-input';
import { ChatManager, ToolRegistry } from '@gemini-cli-book/core';

// A simple interface for our messages
interface Message {
  author: 'user' | 'gemini';
  text: string;
}

const App = () => {
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState('');
  const [chatManager, setChatManager] = useState<ChatManager | null>(null);

  useEffect(() => {
    // Initialize the ChatManager when the component mounts
    const apiKey = process.env.GEMINI_API_KEY;
    if (apiKey) {
      const toolRegistry = new ToolRegistry(); // We'll add tools later
      setChatManager(new ChatManager(apiKey, toolRegistry));
    }
  }, []);

  const handleSubmit = async () => {
    if (input.trim() === '' || !chatManager) return;

    // Add the user's message to the list
    setMessages(prev => [...prev, { author: 'user', text: input }]);
    const userInput = input;
    setInput('');

    // Send the message to the ChatManager and get the response
    const response = await chatManager.sendMessage(userInput);
    setMessages(prev => [...prev, { author: 'gemini', text: response }]);
  };

  if (!chatManager) {
    return <Text>Please set your GEMINI_API_KEY environment variable.</Text>;
  }

  return (
    <Box flexDirection="column" padding={1}>
      {/* Messages will be rendered here */}
      <Box flexDirection="column">
        {messages.map((msg, index) => (
          <Text key={index}>
            {msg.author === 'user' ? 'You: ' : 'Gemini: '}
            {msg.text}
          </Text>
        ))}
      </Box>

      {/* Input prompt */}
      <Box marginTop={1}>
        <Text>› </Text>
        <TextInput value={input} onChange={setInput} onSubmit={handleSubmit} />
      </Box>
    </Box>
  );
};

export default App;
```

Let's break down the `App` component:

1.  **State:** We are using `useState` to manage the `messages` in the conversation, the current `input` from the user, and our `chatManager` instance.
2.  **`useEffect`:** We are using `useEffect` to initialize our `ChatManager` when the component first mounts. For now, we are creating an empty `ToolRegistry`.
3.  **`handleSubmit`:** This function is called when the user presses Enter in the input prompt. It adds the user's message to the `messages` list, sends the message to the `ChatManager`, and then adds the model's response to the list.
4.  **Rendering:** We are using Ink's `Box` and `Text` components to render the UI. We map over the `messages` array to display each message, and we use the `TextInput` component to get the user's input.

### Running the Interactive UI

Now, let's build and run our new interactive UI.

```bash
npm run build -w @gemini-cli-book/cli
npm run start -w @gemini-cli-book/cli
```

Make sure you have your `GEMINI_API_KEY` environment variable set. You should now see an input prompt. Type a message and press Enter, and you should see a response from Gemini.

In the next chapter, we will refactor our UI to be more organized by creating separate components for the message list and the input prompt.

## Chapter 13: Refactoring the UI and Improving Input Handling

In the previous chapter, we built the basic structure of our interactive UI in a single `App` component. While this works for a simple example, it's a good practice to break down our UI into smaller, reusable components.

In this chapter, we will refactor our UI by creating separate components for displaying the messages and handling the user input. This will make our code more organized, maintainable, and easier to read.

### Creating the `Messages` Component

Let's start by creating a new component that will be responsible for rendering the list of messages. Create a new directory called `components` inside `packages/cli/src`, and then create a new file called `Messages.tsx` inside it.

```bash
mkdir packages/cli/src/components
touch packages/cli/src/components/Messages.tsx
```

Now, add the following content to the `packages/cli/src/components/Messages.tsx` file:

```typescript
import React from 'react';
import { Box, Text } from 'ink';

// Re-defining the Message interface here for simplicity
// In a real app, you would share this from a types file
interface Message {
  author: 'user' | 'gemini';
  text: string;
}

interface MessagesProps {
  messages: Message[];
}

const Messages: React.FC<MessagesProps> = ({ messages }) => {
  return (
    <Box flexDirection="column">
      {messages.map((msg, index) => (
        <Text key={index}>
          {msg.author === 'user' ? 'You: ' : 'Gemini: '}
          {msg.text}
        </Text>
      ))}
    </Box>
  );
};

export default Messages;
```

This is a simple component that takes an array of `messages` as a prop and renders them to the terminal.

### Creating the `Input` Component

Next, let's create a component for our input prompt. Create a new file called `Input.tsx` in the `packages/cli/src/components` directory.

```bash
touch packages/cli/src/components/Input.tsx
```

Add the following content to the `packages/cli/src/components/Input.tsx` file:

```typescript
import React from 'react';
import { Box, Text } from 'ink';
import TextInput from 'ink-text-input';

interface InputProps {
  value: string;
  onChange: (value: string) => void;
  onSubmit: () => void;
}

const Input: React.FC<InputProps> = ({ value, onChange, onSubmit }) => {
  return (
    <Box marginTop={1}>
      <Text>› </Text>
      <TextInput value={value} onChange={onChange} onSubmit={onSubmit} />
    </Box>
  );
};

export default Input;
```

This component encapsulates the `TextInput` and takes the `value`, `onChange` handler, and `onSubmit` handler as props.

### Updating the `App` Component

Now that we have our new `Messages` and `Input` components, let's update our `App.tsx` to use them.

```typescript
import React, { useState, useEffect } from 'react';
import { Box, Text } from 'ink';
import { ChatManager, ToolRegistry } from '@gemini-cli-book/core';
import Messages from './components/Messages';
import Input from './components/Input';

interface Message {
  author: 'user' | 'gemini';
  text: string;
}

const App = () => {
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState('');
  const [chatManager, setChatManager] = useState<ChatManager | null>(null);

  useEffect(() => {
    const apiKey = process.env.GEMINI_API_KEY;
    if (apiKey) {
      const toolRegistry = new ToolRegistry();
      setChatManager(new ChatManager(apiKey, toolRegistry));
    }
  }, []);

  const handleSubmit = async () => {
    if (input.trim() === '' || !chatManager) return;

    setMessages(prev => [...prev, { author: 'user', text: input }]);
    const userInput = input;
    setInput('');

    const response = await chatManager.sendMessage(userInput);
    setMessages(prev => [...prev, { author: 'gemini', text: response }]);
  };

  if (!chatManager) {
    return <Text>Please set your GEMINI_API_KEY environment variable.</Text>;
  }

  return (
    <Box flexDirection="column" padding={1}>
      <Messages messages={messages} />
      <Input value={input} onChange={setInput} onSubmit={handleSubmit} />
    </Box>
  );
};

export default App;
```

Our `App` component is now much cleaner and easier to understand. It's responsible for managing the state and the overall layout, while the `Messages` and `Input` components are responsible for rendering their respective parts of the UI.

This refactoring is a crucial step in building a maintainable and scalable application. In the next chapter, we will implement the command system, allowing the user to issue commands like `/help` and `/clear`.

## Chapter 14: Improving the Display of Responses

Our CLI is now functional, but the display is a bit plain. In this chapter, we will improve the way we display responses by adding color, a loading indicator, and support for Markdown rendering. This will make our CLI more user-friendly and visually appealing.

### Adding New Dependencies

We will be using a few new packages to help us improve our UI:

*   **`chalk`:** A popular library for adding color to terminal output.
*   **`ink-spinner`:** An Ink component for displaying a loading spinner.
*   **`ink-markdown`:** An Ink component for rendering Markdown.

Let's install these packages in our `cli` workspace:

```bash
npm install chalk ink-spinner ink-markdown -w @gemini-cli-book/cli
```

### Adding Color to Messages

Let's start by adding some color to our `Messages` component to distinguish between the user and Gemini. We will use `chalk` to make the "You:" prefix green and the "Gemini:" prefix blue.

Update the `packages/cli/src/components/Messages.tsx` file:

```typescript
import React from 'react';
import { Box, Text } from 'ink';
import chalk from 'chalk';
import Markdown from 'ink-markdown';

interface Message {
  author: 'user' | 'gemini';
  text: string;
}

interface MessagesProps {
  messages: Message[];
}

const Messages: React.FC<MessagesProps> = ({ messages }) => {
  return (
    <Box flexDirection="column">
      {messages.map((msg, index) => (
        <Box key={index} flexDirection="column">
          <Text>
            {msg.author === 'user'
              ? chalk.green('You:')
              : chalk.blue('Gemini:')}
          </Text>
          <Box marginLeft={2}>
            <Markdown>{msg.text}</Markdown>
          </Box>
        </Box>
      ))}
    </Box>
  );
};

export default Messages;
```

We are now using `chalk` to color the author prefixes. We have also replaced the `Text` component for the message content with the `Markdown` component from `ink-markdown`. This will automatically render any Markdown in Gemini's responses, such as lists, bold text, and code blocks.

### Adding a Loading Spinner

Next, let's add a loading spinner to our `App` component to give the user some feedback while we are waiting for a response from the model.

We will add a new state variable called `isLoading` to our `App` component. This will be set to `true` when we send a request to the model and `false` when we receive the response.

Update the `packages/cli/src/App.tsx` file:

```typescript
import React, { useState, useEffect } from 'react';
import { Box, Text } from 'ink';
import Spinner from 'ink-spinner';
import { ChatManager, ToolRegistry } from '@gemini-cli-book/core';
import Messages from './components/Messages';
import Input from './components/Input';

// ... (Message interface remains the same)

const App = () => {
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState('');
  const [chatManager, setChatManager] = useState<ChatManager | null>(null);
  const [isLoading, setIsLoading] = useState(false);

  // ... (useEffect remains the same)

  const handleSubmit = async () => {
    if (input.trim() === '' || !chatManager) return;

    setMessages(prev => [...prev, { author: 'user', text: input }]);
    const userInput = input;
    setInput('');
    setIsLoading(true);

    const response = await chatManager.sendMessage(userInput);
    setMessages(prev => [...prev, { author: 'gemini', text: response }]);
    setIsLoading(false);
  };

  // ... (if !chatManager check remains the same)

  return (
    <Box flexDirection="column" padding={1}>
      <Messages messages={messages} />
      {isLoading ? (
        <Box>
          <Text>
            <Spinner type="dots" />
          </Text>
          <Text> Gemini is thinking...</Text>
        </Box>
      ) : (
        <Input value={input} onChange={setInput} onSubmit={handleSubmit} />
      )}
    </Box>
  );
};

export default App;
```

Now, when we are waiting for a response, we will render a spinner instead of the input prompt. This provides a much better user experience.

With these changes, our CLI is starting to look and feel like a polished application. In the next chapter, we will add support for slash commands like `/help` and `/clear`.

## Chapter 15: The Command System

A common feature in command-line applications is the ability to issue special commands to perform specific actions. In this chapter, we will implement a "slash command" system, which will allow the user to type commands like `/help` and `/clear` to interact with our CLI.

### What are Slash Commands?

Slash commands are commands that start with a forward slash (`/`). They are a convenient way to provide users with a set of built-in commands that are separate from the prompts that are sent to the model.

We will implement the following commands:

*   `/help`: Displays a list of available commands.
*   `/clear`: Clears the conversation history.
*   `/quit`: Exits the application.

### Handling Slash Commands

We will handle slash commands in our `App` component. In the `handleSubmit` function, before we send the prompt to the model, we will first check if the input starts with a `/`. If it does, we will handle the command; otherwise, we will send the input to the model as a prompt.

Let's update our `Message` interface to support messages from the "system" for things like help messages.

```typescript
interface Message {
  author: 'user' | 'gemini' | 'system';
  text: string;
}
```

Now, let's update the `handleSubmit` function in `packages/cli/src/App.tsx` to handle our new commands. We will also use the `useApp` hook from Ink to get access to the `exit` function.

```typescript
// In App.tsx
import { useApp } from 'ink';

// ...

const App = () => {
  const { exit } = useApp();
  // ... (state and useEffect remain the same)

  const handleSubmit = async () => {
    if (input.trim() === '') return;

    const userInput = input;
    setInput('');

    if (userInput.startsWith('/')) {
      // Handle slash commands
      const [command, ...args] = userInput.slice(1).split(' ');
      switch (command) {
        case 'clear':
          setMessages([]);
          break;
        case 'help':
          setMessages(prev => [
            ...prev,
            {
              author: 'system',
              text: 'Available commands:\n/help - Show this help message\n/clear - Clear the conversation\n/quit - Exit the application',
            },
          ]);
          break;
        case 'quit':
          exit();
          break;
        default:
          setMessages(prev => [
            ...prev,
            { author: 'system', text: `Unknown command: ${command}` },
          ]);
      }
    } else {
      // Handle as a prompt to the model
      if (!chatManager) return;
      setMessages(prev => [...prev, { author: 'user', text: userInput }]);
      setIsLoading(true);

      const response = await chatManager.sendMessage(userInput);
      setMessages(prev => [...prev, { author: 'gemini', text: response }]);
      setIsLoading(false);
    }
  };

  // ... (return statement remains the same for now)
}
```

We have now added a `switch` statement to handle our slash commands. If the command is not recognized, we display an "Unknown command" message.

### Updating the `Messages` Component

Finally, let's update our `Messages` component to handle "system" messages. We will color them yellow.

Update `packages/cli/src/components/Messages.tsx`:

```typescript
// ... (imports)
import chalk from 'chalk';

interface Message {
  author: 'user' | 'gemini' | 'system';
  text: string;
}

// ...

const Messages: React.FC<MessagesProps> = ({ messages }) => {
  return (
    <Box flexDirection="column">
      {messages.map((msg, index) => (
        <Box key={index} flexDirection="column">
          <Text>
            {msg.author === 'user'
              ? chalk.green('You:')
              : msg.author === 'gemini'
              ? chalk.blue('Gemini:')
              : chalk.yellow('System:')}
          </Text>
          <Box marginLeft={2}>
            {msg.author === 'system' ? (
              <Text>{msg.text}</Text>
            ) : (
              <Markdown>{msg.text}</Markdown>
            )}
          </Box>
        </Box>
      ))}
    </Box>
  );
};
```

We have added a new condition to our `chalk` expression to color "system" messages yellow. We also render system messages as plain text, since they are not expected to contain Markdown.

Now, when you run the CLI, you can try out the new slash commands.

In the next chapter, we will implement history management for the input prompt, allowing the user to cycle through their previous prompts using the arrow keys.

## Chapter 16: Input History Management

A standard feature in any good command-line interface is the ability to recall previous commands. In this chapter, we will implement history management for our input prompt, allowing the user to cycle through their previously submitted prompts using the up and down arrow keys.

We will achieve this by creating a custom React hook called `useInputHistory`.

### Creating the `useInputHistory` Hook

Let's start by creating a new `hooks` directory inside `packages/cli/src` and a new file called `useInputHistory.ts` inside it.

```bash
mkdir packages/cli/src/hooks
touch packages/cli/src/hooks/useInputHistory.ts
```

Now, add the following content to the `packages/cli/src/hooks/useInputHistory.ts` file:

```typescript
import { useState } from 'react';

export const useInputHistory = () => {
  const [history, setHistory] = useState<string[]>([]);
  const [index, setIndex] = useState(-1);

  const add = (item: string) => {
    // Don't add empty items or duplicates of the last item
    if (!item || item === history[0]) return;
    setHistory(prev => [item, ...prev]);
    setIndex(-1);
  };

  const getPrevious = (): string | undefined => {
    if (index < history.length - 1) {
      const newIndex = index + 1;
      setIndex(newIndex);
      return history[newIndex];
    }
    return history[index];
  };

  const getNext = (): string | undefined => {
    if (index > 0) {
      const newIndex = index - 1;
      setIndex(newIndex);
      return history[newIndex];
    }
    setIndex(-1);
    return ''; // Return empty string to clear the input
  };

  return { add, getPrevious, getNext };
};
```

Let's break down our new hook:

*   It uses `useState` to store the `history` of prompts and the current `index` in the history.
*   The `add` function adds a new item to the top of the history stack.
*   The `getPrevious` function moves up the history stack (to older items) and returns the item at the new index.
*   The `getNext` function moves down the history stack (to newer items) and returns the item at the new index. It returns an empty string when the user gets back to the start of the history.

### Integrating the Hook into the `App` Component

Now, let's integrate our new `useInputHistory` hook into our `App` component. We will also use the `useInput` hook from Ink to listen for key presses.

Update `packages/cli/src/App.tsx`:

```typescript
import { useApp, useInput } from 'ink';
import { useInputHistory } from './hooks/useInputHistory';

// ...

const App = () => {
  const { exit } = useApp();
  const [messages, setMessages] = useState<Message[]>([]);
  const [input, setInput] = useState('');
  const [chatManager, setChatManager] = useState<ChatManager | null>(null);
  const [isLoading, setIsLoading] = useState(false);
  const history = useInputHistory();

  // ... (useEffect remains the same)

  useInput((_, key) => {
    if (key.upArrow) {
      const prev = history.getPrevious();
      if (prev) setInput(prev);
    }
    if (key.downArrow) {
      const next = history.getNext();
      setInput(next || '');
    }
  });

  const handleSubmit = async () => {
    if (input.trim() === '') return;

    history.add(input);
    const userInput = input;
    setInput('');

    // ... (the rest of handleSubmit remains the same)
  };

  // ... (return statement remains the same)
};
```

Here are the key changes:

1.  We are now using our `useInputHistory` hook to get the `history` object.
2.  In `handleSubmit`, we call `history.add(input)` to add the submitted prompt to our history.
3.  We use the `useInput` hook from Ink to listen for key presses. When the up or down arrow keys are pressed, we call the appropriate method on our `history` object and update the `input` state with the returned value.

Now, when you run the CLI, you can type a few prompts and then use the up and down arrow keys to cycle through them.

In the next chapter, we will add support for non-interactive mode, allowing the user to run the CLI with a single prompt from the command line and get a single response.

## Chapter 17: Non-Interactive Mode

So far, we have been building an interactive chat application. However, a common use case for a CLI tool is to run it with a single command and get a single output. This is often called "non-interactive mode", and it's very useful for scripting and automation.

In this chapter, we will add support for a non-interactive mode to our `gemini-cli`. The user will be able to pass a prompt as a command-line argument, and our CLI will print the response to the console and then exit.

### Parsing Command-Line Arguments

To parse command-line arguments, we will use a library called [meow](https://github.com/sindresorhus/meow). Meow is a lightweight and easy-to-use library for building CLIs.

Let's add `meow` to our `cli` package:

```bash
npm install meow -w @gemini-cli-book/cli
```

### Implementing Non-Interactive Mode

We will implement the logic for non-interactive mode in our main entry point, `packages/cli/src/index.tsx`. We will check if the user has provided any input on the command line. If they have, we will treat it as a prompt and run in non-interactive mode. If not, we will launch our interactive UI as before.

Update `packages/cli/src/index.tsx` with the following content:

```typescript
import React from 'react';
import { render } from 'ink';
import meow from 'meow';
import App from './App';
import { ChatManager, ToolRegistry } from '@gemini-cli-book/core';

const cli = meow(`
  Usage
    $ gemini-cli-book [prompt]

  Examples
    $ gemini-cli-book "Hello, world!"
`, {
  importMeta: import.meta,
});

const NonInteractive = ({ prompt }: { prompt: string }) => {
  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    console.error("Please set your GEMINI_API_KEY environment variable.");
    process.exit(1);
  }

  const toolRegistry = new ToolRegistry();
  const chatManager = new ChatManager(apiKey, toolRegistry);

  chatManager.sendMessage(prompt).then(response => {
    console.log(response);
  }).catch(error => {
    console.error("An error occurred:", error);
    process.exit(1);
  });

  return null; // No UI for non-interactive mode
};

if (cli.input.length > 0) {
  const prompt = cli.input.join(' ');
  render(<NonInteractive prompt={prompt} />);
} else {
  render(<App />);
}
```

Let's break down the changes:

1.  We are using `meow` to parse the command-line arguments. We provide a help text that explains how to use the CLI.
2.  We check if `cli.input` has any items. `cli.input` is an array of the positional arguments passed to our command.
3.  If there is input, we join the array into a single `prompt` string and render a new `NonInteractive` component.
4.  The `NonInteractive` component doesn't actually render any UI. It contains the logic to instantiate our `ChatManager`, send the prompt, and print the response to the console using `console.log`. It then exits the process.
5.  If there is no input, we render our interactive `App` component, just as before.

### Testing Non-Interactive Mode

Now, you can test the new non-interactive mode from your terminal.

First, build the `cli` package:

```bash
npm run build -w @gemini-cli-book/cli
```

Then, run it with a prompt:

```bash
node packages/cli/dist/index.js "What is the capital of France?"
```

You should see the response from Gemini printed directly to your console.

If you run it without any arguments, it will launch the interactive UI as before:

```bash
node packages/cli/dist/index.js
```

This makes our CLI much more versatile and useful for a wider range of tasks.

This concludes Part 3 of our book. We now have a fully functional CLI with both an interactive chat mode and a non-interactive mode. In the next part, we will start implementing the built-in tools like file system access and shell commands.

## Part 4: Built-in Tools

## Chapter 18: File System Tools

Welcome to Part 4! In this part of the book, we will be building the built-in tools that make `gemini-cli` so powerful. These tools will allow the model to interact with the local environment, read and write files, execute shell commands, and more.

We will start by implementing a set of tools for interacting with the file system. These tools will allow the model to read files, write files, and list the contents of directories.

### Creating the File System Tools

Let's create a new file called `fs-tools.ts` in the `packages/core/src` directory. This file will contain the definitions and implementations of our file system tools.

```bash
touch packages/core/src/fs-tools.ts
```

Now, add the following content to the new `fs-tools.ts` file:

```typescript
import { promises as fs } from 'fs';
import { FunctionDeclarationsTool } from '@google/generative-ai';

// Tool for reading a file
export const readFileTool: FunctionDeclarationsTool = {
  functionDeclarations: [{
    name: 'read_file',
    description: 'Reads the content of a file.',
    parameters: {
      type: 'OBJECT',
      properties: { path: { type: 'STRING' } },
      required: ['path'],
    },
  }],
};
export const readFileImpl = async ({ path }: { path: string }) => {
  return fs.readFile(path, 'utf-8');
};

// Tool for writing a file
export const writeFileTool: FunctionDeclarationsTool = {
  functionDeclarations: [{
    name: 'write_file',
    description: 'Writes content to a file.',
    parameters: {
      type: 'OBJECT',
      properties: {
        path: { type: 'STRING' },
        content: { type: 'STRING' },
      },
      required: ['path', 'content'],
    },
  }],
};
export const writeFileImpl = async ({ path, content }: { path: string; content: string }) => {
  await fs.writeFile(path, content);
  return `File written to ${path}`;
};

// Tool for listing files in a directory
export const lsTool: FunctionDeclarationsTool = {
  functionDeclarations: [{
    name: 'ls',
    description: 'Lists the files in a directory.',
    parameters: {
      type: 'OBJECT',
      properties: { path: { type: 'STRING' } },
      required: ['path'],
    },
  }],
};
export const lsImpl = async ({ path }: { path: string }) => {
  const files = await fs.readdir(path);
  return files.join('\n');
};
```

In this file, we have created three tools: `read_file`, `write_file`, and `ls`. For each tool, we have defined a schema and an implementation function that uses Node.js's built-in `fs/promises` module to interact with the file system.

**A Note on Security:** The `write_file` tool is a powerful tool that can modify the user's file system. In a real application, it is crucial to implement a user confirmation step before executing such a tool. We will cover this in a later chapter on security and sandboxing.

### Registering the File System Tools

Now, let's update our main application to register and use these new tools. We will update the `App.tsx` file in the `cli` package to register the tools with our `ToolRegistry`.

In `packages/cli/src/App.tsx`:

```typescript
// ... imports
import { readFileTool, readFileImpl, writeFileTool, writeFileImpl, lsTool, lsImpl } from '@gemini-cli-book/core/fs-tools';

// ...

const App = () => {
  // ... state

  useEffect(() => {
    const apiKey = process.env.GEMINI_API_KEY;
    if (apiKey) {
      const toolRegistry = new ToolRegistry();
      // Register the new tools
      toolRegistry.register(readFileTool, readFileImpl);
      toolRegistry.register(writeFileTool, writeFileImpl);
      toolRegistry.register(lsTool, lsImpl);
      setChatManager(new ChatManager(apiKey, toolRegistry));
    }
  }, []);

  // ... rest of the component
}
```

We have imported our new tools and registered them with the `ToolRegistry` when the application starts. Now, the model will be aware of these new capabilities.

### Using the File System Tools

Let's try out our new file system tools. Run the interactive CLI:

```bash
npm run build -w @gemini-cli-book/cli && npm run start -w @gemini-cli-book/cli
```

Now, you can ask the model to perform file system operations. For example:

*   `List the files in the current directory.` (The model should use the `ls` tool with `path: '.'`)
*   `Read the content of package.json.` (The model should use the `read_file` tool)
*   `Create a new file called hello.txt with the content "Hello, World!"` (The model should use the `write_file` tool)

You will see that the model is now able to interact with your local file system to answer your questions and perform actions.

In the next chapter, we will implement another powerful tool: the ability to execute shell commands.

## Chapter 19: The Shell Command Tool

In this chapter, we will add one of the most powerful and versatile tools to our `gemini-cli`: a tool that can execute shell commands. This will allow the model to perform a vast range of tasks, from running scripts and managing processes to interacting with other command-line tools.

### The Power and Risks of a Shell Tool

The ability to execute shell commands gives our AI agent a huge amount of power. It can use `git` to check the status of a repository, `curl` to make web requests, `ps` to list running processes, and much more.

However, with great power comes great responsibility. A shell tool is also potentially very dangerous. A malicious or poorly prompted model could execute harmful commands, like `rm -rf /`. For this reason, it is absolutely critical that we implement a user confirmation step before executing any shell command. We will cover the implementation of this security measure in a later chapter. For now, we will focus on implementing the tool itself, but please be aware of the risks when testing it.

### Creating the Shell Tool

Let's create a new file called `shell-tool.ts` in the `packages/core/src` directory.

```bash
touch packages/core/src/shell-tool.ts
```

Now, add the following content to the new `shell-tool.ts` file:

```typescript
import { exec } from 'child_process';
import { promisify } from 'util';
import { FunctionDeclarationsTool } from '@google/generative-ai';

const execAsync = promisify(exec);

export const shellTool: FunctionDeclarationsTool = {
  functionDeclarations: [{
    name: 'shell',
    description: 'Executes a shell command.',
    parameters: {
      type: 'OBJECT',
      properties: { command: { type: 'STRING' } },
      required: ['command'],
    },
  }],
};

export const shellImpl = async ({ command }: { command: string }) => {
  try {
    const { stdout, stderr } = await execAsync(command);
    if (stderr) {
      return `Stderr: ${stderr}\nStdout: ${stdout}`;
    }
    return stdout;
  } catch (error) {
    if (error instanceof Error) {
        return `Execution failed: ${error.message}`;
    }
    return `An unknown error occurred during command execution.`;
  }
};
```

Let's break down the implementation:

1.  We are using the `exec` function from Node.js's `child_process` module to execute the command.
2.  We use `promisify` to convert the callback-based `exec` function into a promise-based function, which is easier to work with in an `async/await` context.
3.  The `shellImpl` function executes the command and returns the `stdout`. If there is any `stderr` or an error during execution, it returns a formatted error message.

### Registering the Shell Tool

Now, let's register our new shell tool in the `App.tsx` file in our `cli` package.

In `packages/cli/src/App.tsx`:

```typescript
// ... imports
import { shellTool, shellImpl } from '@gemini-cli-book/core/shell-tool';

// ...

const App = () => {
  // ... state

  useEffect(() => {
    const apiKey = process.env.GEMINI_API_KEY;
    if (apiKey) {
      const toolRegistry = new ToolRegistry();
      // Register file system tools
      // ...

      // Register the shell tool
      toolRegistry.register(shellTool, shellImpl);

      setChatManager(new ChatManager(apiKey, toolRegistry));
    }
  }, []);

  // ... rest of the component
}
```

(You will need to add the registration for the shell tool alongside the file system tools from the previous chapter).

### Using the Shell Tool

Now you can run the interactive CLI and ask the model to execute shell commands.

For example:

*   `What is my current working directory?` (The model should use the `shell` tool with the `pwd` command)
*   `What is the current git branch?` (The model should use `git rev-parse --abbrev-ref HEAD`)
*   `List the running node processes.` (The model might use `ps aux | grep node`)

This new tool dramatically expands the capabilities of our agent.

In the next chapter, we will add tools for interacting with the web, allowing our agent to fetch content from URLs and perform web searches.

## Chapter 20: Web Fetching Tools

To be truly useful, our AI agent needs to be able to access information from the internet. In this chapter, we will implement a `web_fetch` tool that will allow the model to fetch the content of a URL. This will enable it to answer questions about current events, read documentation, and much more.

### Creating the Web Fetch Tool

We will use the `node-fetch` library to make HTTP requests. This library should already be a dependency of our `core` package from the original setup, but if not, you can add it with `npm install node-fetch -w @gemini-cli-book/core`.

Let's create a new file called `web-tools.ts` in the `packages/core/src` directory.

```bash
touch packages/core/src/web-tools.ts
```

Now, add the following content to the new `web-tools.ts` file:

```typescript
import fetch from 'node-fetch';
import { FunctionDeclarationsTool } from '@google/generative-ai';

export const webFetchTool: FunctionDeclarationsTool = {
  functionDeclarations: [{
    name: 'web_fetch',
    description: 'Fetches the content of a URL.',
    parameters: {
      type: 'OBJECT',
      properties: { url: { type: 'STRING' } },
      required: ['url'],
    },
  }],
};

export const webFetchImpl = async ({ url }: { url: string }) => {
  try {
    const response = await fetch(url);
    if (!response.ok) {
      return `Failed to fetch URL: ${response.statusText}`;
    }
    // For simplicity, we are returning the raw text content.
    // A more advanced implementation might parse the HTML and extract the main content.
    const text = await response.text();
    return text;
  } catch (error) {
    if (error instanceof Error) {
        return `Failed to fetch URL: ${error.message}`;
    }
    return `An unknown error occurred while fetching the URL.`;
  }
};
```

Our `webFetchImpl` function takes a URL, uses `node-fetch` to get the content, and returns it as text. It also includes basic error handling.

### What about Web Search?

A `web_search` tool would be a fantastic addition to our CLI. It would allow the model to search the web for information instead of needing an exact URL.

Implementing a `web_search` tool typically requires using a third-party search API, such as the Google Custom Search API or the Bing Search API. These APIs often require an API key and have usage limits.

While we won't be implementing a `web_search` tool in this chapter to keep things simple, the process would be very similar to the other tools we've built: define a schema, write an implementation that calls the search API, and register it with our `ToolRegistry`.

### Registering the Web Fetch Tool

Let's register our new `web_fetch` tool in the `App.tsx` file.

In `packages/cli/src/App.tsx`:

```typescript
// ... imports
import { webFetchTool, webFetchImpl } from '@gemini-cli-book/core/web-tools';

// ...

const App = () => {
  // ... state

  useEffect(() => {
    const apiKey = process.env.GEMINI_API_KEY;
    if (apiKey) {
      const toolRegistry = new ToolRegistry();
      // Register other tools...

      // Register the web fetch tool
      toolRegistry.register(webFetchTool, webFetchImpl);

      setChatManager(new ChatManager(apiKey, toolRegistry));
    }
  }, []);

  // ... rest of the component
}
```

### Using the Web Fetch Tool

Now you can ask the model to fetch content from the web. For example:

*   `What is the content of the page at https://example.com?`
*   `Fetch the content of the google gemini-cli github page and tell me what it is.`

This new tool gives our agent access to the vast amount of information available on the internet.

This concludes Part 4 of our book. We have now equipped our agent with a powerful set of tools for interacting with the file system, executing shell commands, and fetching content from the web. In the next part, we will cover more advanced topics like authentication and configuration.

## Part 5: Advanced Topics

## Chapter 21: User Confirmation for Tools

Security is a critical aspect of any application that can interact with a user's system. As we've discussed, tools like `write_file` and `shell` are very powerful, but they also pose a security risk if not handled carefully.

In this chapter, we will implement a crucial security feature: user confirmation for dangerous tools. Before our CLI executes a tool that can modify the file system or run arbitrary commands, it will first ask the user for their permission.

### Marking Tools as Dangerous

First, we need a way to mark certain tools as requiring user confirmation. We will add a `requiresConfirmation` flag to our `ToolRegistry`.

Let's update the `register` method in `packages/core/src/tool-registry.ts`:

```typescript
// in ToolRegistry class
public register(
  toolDefinition: FunctionDeclarationsTool,
  implementation: ToolImplementation,
  requiresConfirmation = false // default to false
) {
  const functionName = toolDefinition.functionDeclarations[0].name;
  this.toolImplementations.set(functionName, { implementation, requiresConfirmation });
  this.toolDefinitions.push(toolDefinition);
}

// We also need to expose the requiresConfirmation flag
public getTool(name: string) {
    return this.toolImplementations.get(name);
}
```
We've added a `requiresConfirmation` parameter to our `register` method and a `getTool` method to retrieve the tool details.

### Requesting Confirmation from the `ChatManager`

Now, we need to modify our `ChatManager` to check if a tool requires confirmation before executing it. The `core` package should not be aware of the UI, so we will use a callback function to handle the confirmation request.

Update `packages/core/src/chat-manager.ts`:

```typescript
// ... imports
type ConfirmationCallback = (toolCall: any) => Promise<boolean>;

export class ChatManager {
  // ... properties
  private onConfirmation: ConfirmationCallback;

  constructor(apiKey: string, toolRegistry: ToolRegistry, onConfirmation: ConfirmationCallback) {
    // ...
    this.onConfirmation = onConfirmation;
  }

  public async sendMessage(prompt: string): Promise<string> {
    // ...
    if (functionCalls && functionCalls.length > 0) {
        const call = functionCalls[0];
        const tool = this.toolRegistry.getTool(call.name);

        if (tool && tool.requiresConfirmation) {
            const confirmed = await this.onConfirmation(call);
            if (!confirmed) {
                return "Tool execution denied by user.";
            }
        }

        const toolResult = await this.toolRegistry.executeTool(call.name, call.args);
        // ...
    }
    // ...
  }
}
```
The `ChatManager` now takes an `onConfirmation` callback in its constructor. Before executing a tool, it checks if confirmation is required and, if so, calls the callback.

### Implementing the Confirmation Dialog in the UI

Finally, let's implement the confirmation dialog in our `App` component. We will use the `ink-select-input` package for this.

`npm install ink-select-input -w @gemini-cli-book/cli`

Update `packages/cli/src/App.tsx`:

```typescript
import SelectInput from 'ink-select-input';

// ...

const App = () => {
  // ... state
  const [confirmationRequest, setConfirmationRequest] = useState<any>(null);

  const handleConfirmation = (item: { value: boolean }) => {
    // This is a simplified way to handle the callback across renders.
    // In a real app, you might use a more robust solution like a ref or context.
    (window as any)._confirmationResolver(item.value);
    setConfirmationRequest(null);
  };

  const onConfirmation = (toolCall: any): Promise<boolean> => {
    return new Promise((resolve) => {
      setConfirmationRequest(toolCall);
      (window as any)._confirmationResolver = resolve;
    });
  };

  useEffect(() => {
    // ...
    setChatManager(new ChatManager(apiKey, toolRegistry, onConfirmation));
    // ...
  }, []);

  // ...

  if (confirmationRequest) {
    return (
      <Box flexDirection="column">
        <Text>Gemini wants to run the following tool:</Text>
        <Text>Tool: {confirmationRequest.name}</Text>
        <Text>Arguments: {JSON.stringify(confirmationRequest.args)}</Text>
        <Text>Do you want to allow this?</Text>
        <SelectInput
          items={[{ label: 'Yes', value: true }, { label: 'No', value: false }]}
          onSelect={handleConfirmation}
        />
      </Box>
    );
  }

  // ... (rest of the return statement)
};
```
This is a significant change, so let's break it down:
1.  We've added a `confirmationRequest` state to hold the details of the tool call that needs confirmation.
2.  The `onConfirmation` function sets this state and uses a promise to wait for the user's response. We are using a global `window` object to store the promise's `resolve` function, which is a bit of a hack but works for our purposes here.
3.  When `confirmationRequest` is not null, we render a confirmation dialog instead of the main chat UI.
4.  The dialog uses `ink-select-input` to ask the user for a Yes/No answer. When the user makes a selection, `handleConfirmation` is called, which resolves the promise and clears the confirmation request state, returning the UI to the chat.

### Updating Tool Registration

Don't forget to mark the dangerous tools in `App.tsx`:
```typescript
// in useEffect in App.tsx
toolRegistry.register(writeFileTool, writeFileImpl, true); // requires confirmation
toolRegistry.register(shellTool, shellImpl, true); // requires confirmation
```

With this system in place, our CLI is now much more secure.

In the next chapter, we will look at how to add authentication to our CLI.
