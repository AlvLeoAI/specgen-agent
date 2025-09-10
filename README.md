# SpecGen Agent (Slack + OpenAI Whisper + Notion | n8n


### Goal

Automate the creation of structured Product Specifications from Slack voice notes, enriched with customer feedback and competitor research, and closing the loop by notifying the Product Manager directly in Slack.

### Solution

This workflow:

- Captures voice notes from Slack (#product-ideas).
- Transcribes audio into text using OpenAI Whisper.
- Formats the transcript into a standardized Product Spec (Markdown).
- Creates a new Notion page for the spec.
- Extracts key terms and finds relevant customer feedback from Slack (#customer-feedback).
- Enriches with competitor research (OpenAI + Perplexity + Tavily).
- Updates the Notion page with all insights.
- Posts a Slack confirmation with the Notion link.

---

### Steps:

**1. Capture the Idea from Slack**

- Slack Trigger node.
- Workflow starts when a voice note is uploaded to `#product-ideas`.

**2. Get a File**

- Slack “Get a file” node.
- Retrieves file details using:
    
    ```
    {{ $json.files[0].id }}
    
    ```
    

**3. Download File**

- HTTP Request node.
- Downloads audio securely with Slack credentials using:
    
    ```
    {{ $json.url_private_download }}
    
    ```
    

**4. Convert Voice to Text**

- OpenAI Whisper node.
- Converts the audio file into text.

**5. If Condition**

- Checks if transcription is empty.
- True → continue | False → Slack alert.

**6. Notify on Empty Transcription**

- Slack DM alert with workflow name, execution ID, file name, and next steps.

**7. Summarize & Format**

- GPT node formats transcript into Markdown Product Spec:
    - Problem
    - Solution
    - Key Features
    - Success Metrics
    - Implementation Notes
    

**8. Create Notion Page**

- Creates new page in Product Specs DB.
- Properties: Title, Status (Draft), Date, Full Spec.

**9. Extract Key Terms**

- GPT node extracts 2–3 canonical keywords with fallback vocabulary.

**10. Search for Messages**

- Slack Search node queries `#customer-feedback` for keywords.
- Dynamically builds OR query for recent feedback.

**11. Slack Feedback Aggregator with Safe Fallback**

- Code node:
    - Extracts text + links from messages
    - Sorts by timestamp
    - Builds bullets
    - Fallback if no results
    

**12. Analyze the Competition**

- GPT node with Perplexity + Tavily.
- Summarizes competitor overlaps, differentiation, positioning.

**13. Merge**

- Merges Customer Feedback + Competitor Analysis branches.

**14. Normalize Notion Payload**

- Code node unifies payload fields for Notion update.

**15. Update Notion Page**

- Updates the Notion page with:
    - Customer Feedback
    - Competitor Landscape
    - Enriched fields
    

**16. Notify in Slack Thread**

- Posts confirmation with ✅ and Notion link in original Slack thread:

![image.png](attachment:a3b60557-c169-47cc-b7e8-d0c1394b2d31:b92eb96f-02ee-49aa-b24d-411a50e97ebf.png)

---

### Tools Used

- **Slack** (trigger, search, notifications)
- **OpenAI Whisper** (speech-to-text)
- **GPT (3.5 / 4)** (summarization, formatting, analysis)
- **Notion API** (page creation + update)
- **Perplexity / Tavily** (competitor research)
- **Custom Code nodes** (aggregation, normalization, error handling)

---

### Highlights

- Error handling with transcription fallback.
- Structured Product Specs in Markdown for consistency.
- Automated enrichment from customer feedback and competitor landscape.
- Full closed-loop system → from Slack idea to Notion page with confirmation back in Slack.
</aside>
