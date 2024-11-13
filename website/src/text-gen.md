# Text Generation Using Llama.cpp
## Introduction
The main goal of `llama.cpp` is to enable LLM inference with minimal setup and state-of-the-art performance on a wide variety of hardware - locally and in the cloud.

We will be using `llama.cpp` and a Large Language Model (`LLM`) to:
1. Simulate conversations with NPCs (Chatbot functionality) for unique dialogue and more freedom over user input.
1. Implement a bargaining system with the game’s Shopkeeper (Lower/Higher prices on items).

Once both of these components have been covered, we will be moving on to how to use the LLM to generate text with our own prompts.

Finally, we will be covering the Unity Integration, including our scripts and its functions.

## Disclaimers
A good rule of thumb for using LLMs is that no matter how much detail you put into your prompts, the outputs will never be perfect.

LLMs have a tendency to be unpredictable in their responses. A good practice for prompt-crafting is to include “example responses” to give the LLM something to follow but sometimes the AI will outright ignore your instructions.

For example, in our experience, despite telling the AI to “keep the response less than 50 words” and “avoid writing actions like *waves*, *raises eyebrow* ”, the LLM can still generate responses that contradict these instructions.

In our game, we wanted to use the LLM to generate dialogue for NPCs. It is important to note that the AI is unable to fully act out a character. 
For example, one of the first NPC ideas we used was a spirit who would lie to the player about their purpose. Despite telling the LLM that the character should never tell the player specific things, the responses it generated ignored those instructions.

Therefore, if you wanted to instruct the LLM to act out a character, your prompt should be telling the AI how the character would act and look like to the user in that specific moment rather than their role in the overall story/game. This would mean that for a NPC fully played by an AI, the context you put in your prompts would have to adjust according to which stage of the game the player is currently at.

It is also impractical to try and give the LLM all of the “lore” for your game in a single prompt. It could significantly increase the AI's inferencing time as it has to account for more info.