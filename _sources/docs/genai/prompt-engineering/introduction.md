# Introduction to Prompt Engineering

In the context of Gen AI, A prompt is the input or instruction you give to an Gen AI model to guide it in generating a response. It tells the AI what you want, how you want it and sometimes why or in what context.

## Types of Prompts

1. Simple Prompt

   Examples:

   1. Describe the weather today.
   2. What is Gen AI.

2. Complex Prompt

   Describe the thing what you want with more details.

When designing and testing prompts, you typically interact with the LLM via an API.

Using correct formats, phrases, words and signs that helps AI interact more meaningfully with users.

## Five Principles of Prompt Engineering

| Principles  | Description            |
| ----------- | ---------------------- |
| Role        | WHO I am               |
| Instruction | WHAT do I want         |
| Context     | WHY do I want          |
| Data        | WHAT do I already know |
| Output      | HOW do I want it       |

The more details you provide, the better response you get.

### Role

A role denotes the position where you ask the prompt to assume an individual, which helps the AI create a response to that persona.

Example:

> I am a marketing Lead of a smartphone company: what are the current flaws or improvements that can be made in smartphones which can help me market them better?

Roles Example:

1. Project Manager
2. UX Designer
3. Financial Analyst
4. HR Manager
5. Customer Support Agent

### Instruction

This is the core directive of the prompt.
It tells the model a clear outline of what specific action or response the AI is expected to generate.

Example:

> I am a UX Designer of a smartphone company: what are the current flaws or improvements that can be made in smartphones which can help me market them better?
> Explain output in 100 words.

### Context

Provide additional information that helps the model understand the broader scenario of background.

Defines the environment, including external constraints time frames and assumptions.

Example:

> Suggest me a smartphone to buy considering geography?

Context Examples:

1. Historical: Past experiences, observations
1. Geographical: From India/America/Asia etc...
1. Industry: Nuances of heath care, banking, retail etc...
1. Cultural: Specific communities/culture/beliefs.

### Data

This is the specific information or data you want the model to process. It could be a paragraph, a set of numbers or event a single word.

Example:

> Suggest me a smartphone released in 2025 budget within 20000 in India?

### Output

This principle guides the model on the format or type of response desired.

Example:

> Give me the list of suggested mobile in table format.

Output Examples:

1.  Detailed write up/paragraph
2.  Bullet points
3.  Table
4.  Step by step instructions

## Key Concepts

1. Be clear and specific in your prompt
2. Always provide relevant context
3. Tell the AI what role to take
4. Define the expected output format
5. Iterate and refine the prompt for better results
