# 📧 Customer Support Workflow – Automated Email Response System

This **n8n Customer Support Workflow** automatically responds to incoming customer emails by:
1. Detecting new emails in Gmail.
2. Classifying them by type.
3. Responding with relevant policy/product information from your **knowledge base** (powered by Pinecone + RAG).
4. Applying a label for easy inbox management.
5. Sending a friendly, customer-ready reply.

---

## 🚀 Overview

- **Trigger**: Gmail node — listens for new incoming emails.
- **Classification**: Text Classifier node categorizes emails into:
  - **Customer Support** → Questions about policies, products, or services.
  - **Other** → All other emails (spam, newsletters, irrelevant queries, etc.).
- **AI Response**:  
  - Uses **OpenRouter Chat Model** + **Pinecone Vector Store** (with FAQ data stored via [RAG-Powered FAQ Chatbot Template](https://github.com/GitSamarth/RAG-PIPELINE-CHATBOT)).
  - Generates customer-friendly replies with emojis and a warm tone.
- **Labeling**: Gmail Label Node applies predefined labels before sending.
- **Reply**: Sends the AI-generated response directly back to the original sender via Gmail.

---

## ✨ Features

- **Automated Policy Lookup** – Answers customer queries instantly using your stored knowledge base.
- **Smart Classification** – Routes emails to the right branch for tailored handling.
- **Inbox Labeling** – Organizes emails into folders like *Customer Support*, *Spam*, etc.
- **Customer-Friendly Replies** – Uses emojis and polite language automatically.
- **No n8n Attribution** – Attribution footer removed for clean professional emails.

---

## 📋 Prerequisites

Before using this workflow, you should have:

- **Basic knowledge of n8n** workflows and nodes.
- Gmail API credentials with permission to read, label, and send messages.
- Pinecone API key (connected to your FAQ knowledge base from the first template).
- OpenRouter API key for chat model access.
- OpenAI API key for text classification (optional if using OpenRouter for both classification and response).

---

## 🛠️ How It Works

1. **Gmail Trigger Node**  
   - Monitors your inbox for new incoming messages.

2. **Text Classifier Node**  
   - Reads the email content.
   - Determines if it’s **Customer Support** or **Other**.
   - Can be extended with more categories like *Sales*, *Technical Support*, etc.

3. **Customer Support Branch**  
   - **Prepare JSON**: Formats email text into a structured JSON for AI input.
   - **System Message**:
     ```
     # Overview
     You are a customer support agent for Samarth Mahajan.
     Your job is to respond to incoming emails with relevant information using your knowledge base tool.

     ## Instructions
     - Your output should be customer friendly and use emojis.
     - Sign off as Mr. Helpful from Samarth Mahajan Solutions.

     ## Output
     _Output only the body content of the email (because we want to reply to that email, not create a new one)._
     ```
   - **AI Agent**: OpenRouter Chat Model with Pinecone Vector Store tool for RAG-based responses.
   - **Gmail Label Node**: Applies the label *Customer Support*.

4. **Other Branch**  
   - **Do Nothing Node**: Stops further processing.
   - **Optional Gmail Label Node**: Apply *Spam*, *Newsletter*, or another label.

5. **Gmail Reply Node** (Customer Support branch only)  
   - Uses the `messageId` from the Gmail Trigger.
   - Email Type: `text/plain`.
   - Message Body: AI Agent's output.
   - **Pro Tip**: In Gmail Node options, set `Append n8n Attribution` to **Disabled** so recipients don’t see "Sent via n8n".

---

## ⚡ Getting Started

1. Import this workflow into n8n.
2. Connect your credentials:
   - **Gmail API**
   - **Pinecone API**
   - **OpenRouter API**
   - **OpenAI API** (optional for classification)
3. Update your system message and branding if desired.
4. Set up Gmail labels (*Customer Support*, *Spam*, etc.).
5. Activate the workflow.
6. Send yourself a test email asking about store policy — see the instant reply and label applied.

---

## 💬 Example Use Case

**Incoming Email:**  
> "Hi, I purchased a laptop last month. Can you tell me about your warranty policy?"

**Workflow Action:**  
- Detects new email via Gmail Trigger.
- Classifies it as *Customer Support*.
- Labels it as *Customer Support* in Gmail.
- Queries Pinecone for relevant FAQ/policy info.
- AI Agent generates a friendly, emoji-rich response.
- Sends reply directly to the sender.

---

## 📌 Notes

- Works best when paired with the **RAG-Powered FAQ Chatbot Template** so your knowledge base stays updated.
- You can add more categories (e.g., Sales, Technical Support, Returns).
- Classification model can be replaced with OpenRouter’s LLM for more accuracy.
- Keep Pinecone namespace consistent (`faq`) with your RAG template.
