### . Trigger Node

- **Gmail Trigger:** Configure to watch for new emails with the "travel" label
- Set polling interval (e.g., every 5-15 minutes)
- Filter for unread emails to avoid reprocessing

### 2. Email Content Extraction

- **Extract Email Data:** Pull subject, body, sender, date, and attachments
- **PDF Attachment Handler:** If PDFs exist, use a PDF parser node to extract text content
- Combine email body + PDF text into a single text payload for AI processing

### 3. AI Classification Node

- **OpenAI/Anthropic Node:** Send email content to LLM with a structured prompt
- Prompt should ask: "Is this travel-related? If yes, classify as: flight, hotel, tour, car rental, restaurant, or other"
- Return structured JSON response with category + confidence score
- **IF Node:** Branch based on classification result (continue if travel-related, skip if not)

### 4. Data Extraction Node

- **AI Extraction:** Send email to LLM with extraction prompt requesting JSON output
- Extract fields based on category:
- Flights: airline, flight number, departure/arrival airports, dates/times, booking reference, passenger names
- Hotels: property name, location, check-in/check-out dates, booking reference, room type
- Tours/Activities: activity name, location, date/time, duration, booking reference
- Parse LLM response to extract structured JSON data
