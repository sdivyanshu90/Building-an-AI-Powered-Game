<div align="center">
  <h1 align="center">Building an AI-Powered Game</h1>
  <p align="center">
    A DeepLearning.AI short course in collaboration with Together AI and AI Dungeon.
  </p>
  <p align="center">
    This repository contains my personal notes, code, and project files from the "Building an AI-Powered Game" course.
    <br />
    <a href="https://www.deeplearning.ai/short-courses/building-an-ai-powered-game/"><strong>Explore the Course »</strong></a>
  </p>
</div>

## Introduction
This course provides a hands-on journey into the exciting world of building applications powered by Large Language Models (LLMs). By following the modules, you will construct your very own AI-driven, text-based adventure game from the ground up. The core of this project involves using an LLM as a dynamic "Dungeon Master" that generates a rich game world, responds to player actions, and maintains a coherent narrative.

You will begin by mastering prompt engineering techniques to establish a game world through hierarchical content generation, ensuring a deep and consistent lore. From there, you'll build the game's essential framework, including a core action loop and a simple user interface using Gradio, transforming your concept into a playable experience.

A key focus of this course is on responsible AI development. You will learn to implement crucial safety measures using powerful tools like Llama Guard, creating custom content policies to ensure a safe and appropriate experience for all players. Finally, you will delve into advanced LLM capabilities like tool calling to implement persistent game mechanics such as player inventory, world state, and progress tracking, giving your game memory and a true sense of consequence.

By the end of this course, you will not only have a unique, playable AI-powered game to share but also a comprehensive understanding of the full development lifecycle of an interactive LLM application.

## Course Topics
This course is structured around four key pillars of building an interactive AI application. Each module provides the theoretical knowledge and practical skills needed to progress from a simple idea to a fully functional game.

<details>
<summary><strong>1. Hierarchical Content Generation</strong></summary>

Hierarchical content generation is a sophisticated technique used to create vast, detailed, and logically consistent game worlds from a simple high-level concept. In the context of our AI-powered game, it serves as the foundation for the entire narrative and environment. Instead of manually authoring every location, character, and item, we leverage the power of LLMs to procedurally generate this content in a structured, top-down manner. This approach not only saves an immense amount of development time but also allows for the creation of worlds that are uniquely generated for each playthrough, offering limitless replayability.

The process begins with a "seed prompt." This is a concise, high-level description of the game world you want to create. For example, a seed prompt might be: "A desolate, post-apocalyptic city once powered by magic, now reclaimed by mutated nature." From this single prompt, the LLM is guided to flesh out the world in layers. The first layer might generate the main continents or regions. A subsequent prompt, which includes the context of the seed and the generated continent, would then be used to generate major cities or landmarks within that continent. This process continues drilling down through the hierarchy: from city to districts, from districts to specific buildings like a "ruined magical library," and from that building to the items, puzzles, or non-player characters (NPCs) found within it.

The "hierarchical" aspect is crucial for maintaining narrative and logical consistency. When the LLM generates a specific room within a library, it does so with the full context of the library's description, the district it's in, the city's history, and the world's overall post-apocalyptic magical theme. This ensures that details remain coherent and relevant. An item found in the magical library is more likely to be a "glowing, unreadable tome" than a "futuristic laser pistol," because the generation is constrained by the established lore at higher levels of the hierarchy. Effective prompt engineering is the key to managing this process. Techniques such as structured output (requesting JSON or XML), chain-of-thought prompting, and providing clear examples (few-shot prompting) are used to guide the LLM into producing well-organized and usable content. For instance, you might prompt the LLM to return a JSON object for a location, with keys for description, inhabitants, items, and exits, which can then be directly parsed and integrated into the game's data structure. This structured approach is far more reliable than parsing free-form text and is a cornerstone of building robust LLM applications. Ultimately, hierarchical generation transforms the LLM from a simple text generator into a powerful world-building partner, enabling developers to dream up expansive settings and have the AI handle the meticulous task of filling in the details.

</details>

<details>
<summary><strong>2. Interactive AI Applications</strong></summary>

Building an interactive AI application, such as our text-based game, involves more than just sending prompts to an LLM. It requires creating a robust framework that can manage user input, maintain game state, and create a seamless feedback loop between the player and the AI. This module focuses on constructing the core architecture of the game, turning our generated world into a dynamic and responsive experience. The central component of this architecture is the "game loop," a fundamental concept in game development that continuously waits for player input, processes it, updates the game world, and presents the results back to the player.

In our AI-powered game, the game loop has a unique structure. It begins when the player enters a command, such as "go north" or "inspect the glowing orb." This input is first parsed by our application. The application then constructs a detailed prompt for the LLM. This is not just the player's raw input; it's a carefully assembled package of information that provides the LLM with all the necessary context to make an informed and coherent decision. This context typically includes the player's current location description, their inventory, their health status, recent events in the narrative, and the specific action they want to perform. By providing this rich context, we enable the LLM to act as an intelligent Dungeon Master that understands the full state of the game.

Once the prompt is sent to the LLM API, the application waits for the response. The LLM processes the input and generates a narrative description of the outcome. For example, it might respond with, "You step through the northern door into a dusty chamber. In the center, the glowing orb pulses with a soft, ethereal light." This response is then parsed by the application. The application displays the narrative part to the player and also extracts any structured data that might indicate a change in the game state (a topic covered more deeply in implementing game mechanics). The game state is updated, and the loop completes, waiting for the next player input.

To make this interaction accessible, we build a user interface (UI). This course uses Gradio, a simple yet powerful Python library for creating web-based UIs for machine learning models. The UI provides a text box for the player to enter commands and a display area to show the AI's narrative responses. This front-end component communicates with our back-end Python logic, which contains the game loop, state management, and LLM API calls. Building this application architecture is a critical step in moving from a theoretical concept to a tangible, interactive product. It requires careful consideration of data flow, state management (how to store and update information like player location and inventory), and user experience, ensuring the player feels immersed and in control of their adventure.

</details>

<details>
<summary><strong>3. Moderation & Safety</strong></summary>

When building AI applications that interact with users, especially those involving user-generated content, implementing robust moderation and safety protocols is not just a best practice—it's a critical responsibility. LLMs can be prompted to generate harmful, inappropriate, or unintended content, and players may try to steer the game in problematic directions. This module is dedicated to creating a safe and positive user experience by integrating powerful guardrails into our game. We will explore how to use state-of-the-art tools like Llama Guard and how to define our own custom content policies to ensure the game's narrative stays on track and free of objectionable material.

The core of our safety system is a multi-layered approach to content moderation. This process involves checking both the player's input before it's sent to the LLM and the LLM's output before it's shown to the player. For the input, we want to prevent players from using offensive language or attempting prompt injection attacks to break the game's rules. For the output, we need to ensure the AI's generated story doesn't contain violent, hateful, or otherwise harmful content that violates our safety standards. Llama Guard is a specialized LLM designed specifically for this purpose. Instead of generating creative text, it's trained to classify prompts and responses against a predefined safety taxonomy. When we provide it with a piece of text, it can tell us if it violates categories like hate speech, harassment, or self-harm, allowing our application to programmatically block or flag the content.

We will implement this as a "safety layer" in our application's logic. When a player submits a command, it first goes to Llama Guard. If the input is flagged as unsafe, the application can reject it and ask the player to rephrase their command, preventing the main LLM from ever seeing the harmful prompt. Similarly, when our main game LLM generates a response, that response is also sent through the Llama Guard filter. If the generated story is deemed unsafe, the application can prevent it from being displayed. It might then try to re-generate the response with a modified prompt or simply inform the player that the action cannot be performed, thus protecting the user from potentially disturbing content.

Beyond universal safety concerns, we will also explore creating custom, context-dependent guardrails. These are rules specific to our game's world and tone. For example, in a high-fantasy game intended for all ages, we might want to create a custom policy to prevent gratuitous violence or romantic themes, even if they aren't strictly "unsafe" by a general definition. This can be achieved by writing specific instructions for another LLM acting as a "policy-checker" or by using keyword filtering. This ensures the narrative remains aligned with the intended player experience. By combining powerful, general-purpose safety models like Llama Guard with fine-grained, application-specific policies, we can build an AI-powered game that is not only engaging and creative but also responsible and safe for its audience.

</details>

<details>
<summary><strong>4. Implementing Game Mechanics</strong></summary>

To elevate our application from a simple interactive story to a true game, we must implement tangible game mechanics. These are the rules and systems that govern the player's interaction with the world, such as managing an inventory, tracking character stats, and making progress on quests. This module focuses on using advanced LLM techniques, particularly "tool calling," to create a persistent and stateful game world. While an LLM has a short-term memory based on its context window, it cannot permanently remember events or changes on its own. We need to build external systems to manage the game's state and then use tool calling to allow the LLM to interact with these systems in a structured way.

Tool calling (or function calling) is a powerful feature of modern LLMs that allows them to go beyond generating text. We can provide the LLM with a list of available "tools" (which are simply functions in our Python code) and their descriptions. When a player's command implies a structured action, the LLM can choose to "call" one of these tools instead of just writing a story about it. For example, if the player types "pick up the rusty key," we don't want the LLM to just say, "You pick up the key." We want the game to actually remember that the player now possesses the key. We can achieve this by defining a function like addItemToInventory(item_name: str). When the LLM receives the prompt "pick up the rusty key," it recognizes the player's intent and, instead of generating prose, outputs a structured request to call our addItemToInventory function with the argument 'rusty key'. Our application intercepts this request, executes the Python function, adds the key to the player's inventory data structure, and then prompts the LLM again to generate a narrative description of the successful action.

This mechanism is the key to creating a game with memory and consequence. We will build several core systems to manage the game state: an inventory system (e.g., a Python list or dictionary to hold item names), a character state system (to track health, magic points, etc.), and a world state system (to remember if a door has been unlocked or a puzzle has been solved). Every time the player takes an action, the game loop does the following: it packs the current game state (inventory, health, location state) into the context for the LLM. The LLM then decides whether to generate text or call a tool to modify that state. After a tool is called and the state is updated, the loop repeats.

This approach makes the game world feel alive and responsive. If the player's inventory list, included in the prompt context, contains the "rusty key," and they are at a location with a "rusty lock," the LLM can logically deduce that the player can now open the lock and will generate a corresponding story or action. This creates a much deeper and more satisfying gameplay experience than a stateless chatbot. By integrating tool calling with external state management, we give our AI Dungeon Master the ability to not only tell a story but to truly manage the rules and the reality of the game world.

</details>

## Acknowledgement
I would like to express my sincere gratitude to the following organizations for their invaluable contributions to this course:

* **[DeepLearning.AI](https://www.deeplearning.ai/)**

* **[Together AI](https://www.together.ai/)**

* **[AI Dungeon](https://latitude.io/)**

Through their expert collaboration, this course and its materials were made possible. Their commitment to advancing AI education and empowering developers plays a pivotal role in shaping the future of intelligent applications.
