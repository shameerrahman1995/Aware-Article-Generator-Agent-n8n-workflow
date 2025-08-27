# Input-Aware Article Generator Agent - n8n Workflow

## Overview
This repository contains the JSON configuration for the **Input-Aware Article Generator Agent**, an n8n workflow that acts as an intelligent writing assistant. It processes user inputs from Telegram (text, voice notes, or images), interprets the content, generates a structured article outline, conducts lightweight research using Wikipedia, and produces a high-quality draft. The workflow supports iterative refinement based on user feedback and outputs the final article as a Google Doc.

## Workflow Description
The workflow automates article generation by:
1. **Triggering on Telegram Inputs**: Listens for messages, voice notes, or photos.
2. **Input Routing**: Uses a switch to handle different input types (audio for transcription, images for OCR, text directly).
3. **Processing Inputs**: Transcribes voice notes, performs OCR on images, and aggregates the content.
4. **AI Agent Processing**: Employs an OpenAI chat model to interpret inputs, create an outline, suggest refinements (tone, structure, depth), and incorporate public sources from Wikipedia.
5. **Feedback Loop**: Checks for modification instructions and iterates if needed.
6. **Output Generation**: Formats the content in Markdown, creates a Google Doc, and updates it with the article.
7. **Response**: Sends the result back to Telegram and handles any errors or loops.

## Workflow Structure
The workflow includes the following key nodes:
- **Telegram Trigger** (`n8n-nodes-base.telegramTrigger`): Starts the workflow on incoming Telegram messages.
- **Switch** (`n8n-nodes-base.switch`): Routes based on input type (audio, photo, text).
- **Get a file** & **Get a file1** (`n8n-nodes-base.telegram`): Fetches voice or photo files from Telegram.
- **HTTP Request** (`n8n-nodes-base.httpRequest`): Sends images to Mistral AI for OCR.
- **Translate a recording** (`n8n-nodes-base.httpRequest`): Transcribes voice notes using OpenAI Whisper.
- **Aggregate** (`n8n-nodes-base.aggregate`): Combines processed inputs.
- **Edit Fields** (`n8n-nodes-base.set`): Prepares data for the AI agent.
- **OpenAI Chat Model** (`@n8n/n8n-nodes-langchain.lmChatOpenAi`): Powers the AI agent.
- **AI Agent** (`@n8n/n8n-nodes-langchain.agent`): Interprets inputs, generates outline/draft, uses tools.
- **Wikipedia** (`@n8n/n8n-nodes-langchain.toolWikipedia`): Tool for research within the AI agent.
- **Simple Memory** (`@n8n/n8n-nodes-langchain.memoryChat`): Maintains conversation context.
- **Code** & **Code1/Code2** (`n8n-nodes-base.code`): Handles JSON parsing, Markdown formatting.
- **Extract from File1** (`n8n-nodes-base.extractFromFile`): Extracts text from OCR results.
- **Send a text message** (`n8n-nodes-base.telegram`): Responds back to Telegram.
- **If** (`n8n-nodes-base.if`): Checks for modification requests.
- **Create a document** & **Update a document** (`n8n-nodes-base.googleDocs`): Generates and updates Google Docs.

## Inputs
- **Telegram Messages**: Text, voice notes (audio/ogg), or photos.
- **Modification Instructions**: Optional feedback for refining the generated article.
- **Public Sources**: Automatically fetched from Wikipedia via the AI agent.

## Outputs
- **Structured JSON**: Includes outline, directional refinements (tone, structure, depth), and sources.
- **Markdown Content**: Formatted article draft.
- **Google Doc**: Created and updated with the article content.
- **Telegram Response**: Sends the result or confirmation back to the user.

## Prerequisites
- **n8n Instance**: Active n8n setup (cloud or self-hosted).
- **Telegram API Credentials**: Bot token for receiving and sending messages.
- **OpenAI API Credentials**: For chat model and transcription (Whisper).
- **Mistral AI API Key**: For OCR on images (in HTTP Request node).
- **Google Docs API Credentials**: OAuth2 for creating/updating documents.

## Setup Instructions
1. **Import Workflow**:
   - Copy the JSON from `Aware Article Generator Agent.json`.
   - Import into n8n via Workflows > Import from File/Clipboard.
2. **Configure Credentials**:
   - Set Telegram API for trigger and message nodes.
   - Configure OpenAI for the chat model and transcription.
   - Add Mistral AI key in the HTTP Request for OCR.
   - Set Google Docs OAuth2 for document nodes.
3. **Update Node Details**:
   - Ensure API URLs and prompts match your setup (e.g., Mistral OCR endpoint).
   - Test input handling for voice, images, and text.
4. **Test Workflow**:
   - Use the manual trigger or send a Telegram message to the bot.
   - Verify processing, AI generation, and Google Doc creation.

## Usage
- **Trigger via Telegram**: Send text, voice, or image to the bot.
- **Input Processing**: Workflow routes and processes the input.
- **Article Generation**: AI agent creates outline/draft, researches via Wikipedia.
- **Refinement**: Reply with modifications to iterate.
- **Output**: Receive response in Telegram; article saved as Google Doc.

## Notes
- The workflow handles one input type per message; prioritize based on switch logic.
- Ensure API keys are secure; the provided JSON has credentials removed.
- Custom prompts in the AI agent can be adjusted for different article styles.
- Delays or errors may occur with large inputs; test with small samples.

## Contributing
Fork the repo, make changes, and submit a pull request. Suggestions for improving AI prompts or adding tools welcome!

## License
MIT License - see [LICENSE](LICENSE) for details.