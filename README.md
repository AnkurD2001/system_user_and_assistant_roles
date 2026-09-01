## 🤖 Chat Completion using OpenAI Python SDK

A simple Python project demonstrating how to use the OpenAI Python SDK with an OpenAI-compatible API to generate responses from a Large Language Model (LLM).

This project explores basic chat completions, system and user messages, token limits, temperature, seed values, and streaming responses.

## 📌 Project Overview

This project demonstrates how to:

Load environment variables using python-dotenv.
Create an OpenAI client.
Connect to an OpenAI-compatible API.
Send chat completion requests.
Use system and user messages.
Extract generated text from a chat completion.
Control response length using max_tokens.
Control response randomness using temperature.
Use seed for reproducible responses when supported.
Stream the response using stream=True.
## 🔑 Environment Variables

The API key is loaded from a .env file.

Create a .env file:

YOUR_API_KEY=your_api_key_here


In a Jupyter Notebook, load the environment variables using:

%load_ext dotenv
%dotenv


For a normal Python script:

from dotenv import load_dotenv

load_dotenv()

## 📦 Installation

Install the required packages:

pip install openai python-dotenv

## 🔌 Creating the OpenAI Client

The OpenAI client is initialized using an API key and a custom base_url.

import os
from openai import OpenAI

client = OpenAI(
    api_key=os.getenv("YOUR_API_KEY"),
    base_url="https://YOUR_API_PROVIDER/api/",
)

Parameters
Parameter	Description
api_key	API key loaded from the environment
base_url	Base URL of the OpenAI-compatible API

The base_url allows the OpenAI Python SDK to communicate with an API provider that implements an OpenAI-compatible interface.

## 💬 Basic Chat Completion

A basic chat completion can be created using:

completion = client.chat.completions.create(
    model="YOUR_MODEL_NAME",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"},
    ],
)


The response can be accessed using:

print(completion.choices[0].message.content)


This extracts the generated text from the first response choice.

## 🧩 Understanding Messages

The messages parameter contains the conversation sent to the model.

System Message
{
    "role": "system",
    "content": "You are a helpful assistant."
}


The system message defines the behavior or instructions for the assistant.

User Message
{
    "role": "user",
    "content": "Hello!"
}


The user message contains the actual request or question.

## Creating a Sarcastic Chatbot

A system message can be used to give the model a specific personality.

For example:

completion = client.chat.completions.create(
    model="YOUR_MODEL_NAME",
    messages=[
        {
            "role": "system",
            "content": """You are Marv, a chatbot that reluctantly
            answers questions with sarcastic responses."""
        },
        {
            "role": "user",
            "content": """I've recently adopted a dog.
            Could you suggest some dog names?"""
        }
    ]
)


The system message instructs the model to behave like Marv, a sarcastic assistant.

The generated response can then be extracted using:

print(completion.choices[0].message.content)

## 🎯 Controlling Response Length with max_tokens

The max_tokens parameter controls the maximum number of tokens that can be generated in the response.

For example:

completion_max_tokens = client.chat.completions.create(
    model="YOUR_MODEL_NAME",
    messages=[
        {
            "role": "system",
            "content": """You are Marv, a chatbot that reluctantly
            answers questions with sarcastic responses."""
        },
        {
            "role": "user",
            "content": """Could you explain briefly what a black hole is?"""
        }
    ],
    max_tokens=250
)


The generated content can be accessed using:

print(completion_max_tokens.choices[0].message.content)

Using a Smaller Token Limit

When the token limit is reduced:

max_tokens=50


the model may not have enough tokens to complete its response.

In that situation, the completion can end with:

finish_reason='length'


This means the response reached the maximum allowed output length before the model naturally finished its answer.

## 🌡️ Controlling Randomness with temperature

The temperature parameter controls the randomness of the model's output.

For example:

completion_max_tokens_temperature = client.chat.completions.create(
    model="YOUR_MODEL_NAME",
    messages=[
        {
            "role": "user",
            "content": """Could you explain briefly what a black hole is?"""
        }
    ],
    max_tokens=250,
    temperature=0
)


A lower temperature generally produces more predictable and focused responses.

Temperature = 0
temperature=0


This is useful when you want the model to produce more consistent responses.

Higher values generally allow more variation and creativity in the generated text.

## 🎲 Using seed

The seed parameter can be used when supported by the model/provider to make outputs more reproducible.

completion_max_tokens_temperature_seed = client.chat.completions.create(
    model="YOUR_MODEL_NAME",
    messages=[
        {
            "role": "user",
            "content": """Could you explain briefly what a black hole is?"""
        }
    ],
    max_tokens=250,
    temperature=0,
    seed=365
)


The response can be printed using:

print(completion_max_tokens_temperature_seed.choices[0].message.content)


Using the same prompt, model, temperature, and seed can help produce consistent results when the API provider supports deterministic seeding.

## 📡 Streaming Responses

Normally, the API returns the completed response after the model finishes generating it.

With:

stream=True


the response is returned incrementally as a stream of chunks.

completion_max_tokens_temperature_seed_stream = client.chat.completions.create(
    model="YOUR_MODEL_NAME",
    messages=[
        {
            "role": "user",
            "content": """Could you explain briefly what a black hole is?"""
        }
    ],
    max_tokens=250,
    temperature=0,
    seed=365,
    stream=True
)


The result is a stream object:

print(completion_max_tokens_temperature_seed_stream)


You can iterate through the stream:

for i in completion_max_tokens_temperature_seed_stream:
    print(i)


Each iteration returns a ChatCompletionChunk.

## 📝 Extracting Streaming Content

Instead of printing the complete chunk object, you can extract only the generated content:

for i in completion_max_tokens_temperature_seed_stream:
    print(i.choices[0].delta.content, end="")


The delta.content field contains the newly generated portion of the response.

Using end="" prevents Python from creating a new line after every chunk, allowing the response to appear as continuous text.

## 🔄 How It Works

The application follows this flow:

User Prompt
     ↓
OpenAI Python SDK
     ↓
OpenAI-Compatible API
     ↓
LLM
     ↓
Chat Completion
     ↓
Generated Content


For streaming:

User Prompt
     ↓
OpenAI Python SDK
     ↓
OpenAI-Compatible API
     ↓
LLM
     ↓
ChatCompletionChunk
     ↓
delta.content
     ↓
Displayed Incrementally

## 📄 Understanding the Completion Object

When you run:

completion


the API returns a ChatCompletion object containing information about the generated response.

Important fields include:

id — Unique identifier for the completion.
choices — Contains the generated response choices.
model — Model used to generate the response.
usage — Information about token usage.
finish_reason — Indicates why generation stopped.
message.content — Contains the generated text.

The generated response is typically accessed with:

completion.choices[0].message.content

## 🛑 Understanding finish_reason

The finish_reason indicates why the model stopped generating.

For example:

finish_reason='stop'


means the model completed its response normally.

While:

finish_reason='length'


means the response reached the configured token limit.

## 📜 License

This project is created for learning and experimentation with the OpenAI Python SDK, chat completions, and OpenAI-compatible LLM APIs.
