# Workflow A: Email Parsing and Extraction

This workflow handles the ingestion of travel-related emails, processing of attachments, and extraction of structured itinerary data using AI.

## How it Works

The workflow logic follows these key steps:

### 1. Ingestion
- **[Gmail Trigger] Get many messages**: Fetches emails from Gmail that match specific criteria:
    - Label: `travel`
    - Status: `unread`
    - Options: Downloads attachments allowed.

### 2. Preprocessing
- **[Code] Code in JavaScript**: Custom script that acts as the initial processor:
    - Decodes different email encodings (base64url).
    - Strips HTML tags to produce clean plain text.
    - Scans for PDF attachments.
    - Outputs a standardized object for each email containing metadata, cleaned body text, and a flag indicating if PDFs are present.

### 3. Branching & OCR
- **[If] If**: specific check for items flagged as `pdf` type.
- **[Extract from File] Extract from File**: If the item is a PDF attachment, this node extracts the raw text content from the file.
- **[Code] Code in JavaScript2**: specific formatter for the extracted PDF text.

### 4. Aggregation
- **[Merge] Merge**: Re-combines the originally processed email objects with their corresponding extracted PDF text.
- **[Code] Code in JavaScript1**: Consolidates everything by `messageId`.
    - It ensures that for every email, we have one final object containing the email body + an array of text from all attached PDFs.

### 5. AI Extraction
- **[AI Agent] AI Agent**: The core intelligence node. It receives the consolidated text (email body + PDF contents) and uses a Large Language Model (GPT-4.1) to:
    - **Classify** the type of booking (Flight, Hotel, Car, Cruise, Receipt, Other).
    - **Extract** specific details (Dates, Confirmation Numbers, Locations, Prices) based on a strict schema.
    - **Prioritize** PDF data over email body text for accuracy.

### 6. Output
- **[Structured Output Parser]**: Enforces the JSON schema for the final result, ensuring consistent data structure for downstream applications.
    - *Note*: This output structure is designed to be flexible and can be easily mapped to a Supabase schema for database persistence.
