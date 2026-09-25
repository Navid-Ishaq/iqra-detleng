# The n8n Field Guide — Nodes as Characters

## How n8n Thinks: A Story With Nodes

Every n8n workflow is really just a small story, told in boxes connected by lines. Once you can see the story shape, individual nodes stop being random icons and start being *characters with a job*.

The shape looks like this:

```
Something happens
      ↓
A trigger wakes the workflow
      ↓
Data enters
      ↓
Data gets cleaned or reshaped
      ↓
A decision is made
      ↓
Other systems are contacted
      ↓
Something useful happens
      ↓
The result is stored or returned
      ↓
Errors are caught, not ignored
      ↓
The workflow ends safely
```

That's the plot of almost every automation you'll build — a two-node "send me a Slack message every morning" and a two-hundred-node enterprise pipeline are telling the same kind of story, just with a different cast size.

This guide walks through that cast, family by family: the triggers that open the story, the logic that decides what happens next, the data-shaping nodes that keep things tidy, the connectors that talk to the outside world, and the AI and reliability nodes that make the whole thing feel less like a script and more like a small, well-run team.

**Remember it like this:** a workflow is a movie. Nodes aren't features — they're the cast.

## Triggers — What Wakes the Workflow

Nothing happens in n8n until a trigger fires. A trigger is the opening scene: it decides *when* the story starts and *what* kicked it off.

### Manual Trigger — The Practice Button

**Human meaning:** you press "Run" and the workflow says, "Show me what we're testing today." **Professional meaning:** starts the workflow on demand, from the n8n editor, with no external event. Used constantly while building and debugging. **Why it matters:** you need a way to test a workflow without waiting for a real webhook or a real 9 a.m. **Remember it like this:** the rehearsal, not the performance.

### Webhook — The Doorbell

**Human meaning:** someone outside rings, and your workflow wakes up. **Professional meaning:** exposes a URL that another system can call (an HTTP request) to start the workflow, usually carrying data in the request body. **Why it matters:** it's the standard way outside systems — a form, another app, a payment provider — hand data to n8n the instant something happens, instead of n8n having to ask repeatedly. **Tiny example:** a signup form posts to your webhook → workflow starts → new user gets a welcome email. **Remember it like this:** no doorbell, no visitors.

### Schedule Trigger — The Alarm Clock

**Human meaning:** it goes off at a set time, whether you're ready or not. **Professional meaning:** starts the workflow on a cron-style schedule — every hour, every weekday morning, the first of the month. **Why it matters:** perfect for anything routine: nightly reports, daily syncs, weekly digests. **Remember it like this:** automation's version of an alarm — it doesn't care that you're still asleep.

### Form Trigger — The Reception Desk

**Human meaning:** a person fills a form and hands over their details. **Professional meaning:** generates a hosted form; submitting it starts the workflow with the form's answers as data. **Why it matters:** lets you collect structured input without building a separate front end.

### Chat Trigger — The Conversation Starter

**Human meaning:** someone types a message and the workflow perks up. **Professional meaning:** starts the workflow from an incoming chat message, commonly used to build conversational AI workflows. **Why it matters:** it's the entry point for most n8n chatbot and AI-assistant builds.

### Email Trigger — The Inbox Watchman

**Human meaning:** it waits by the mailbox for something worth opening. **Professional meaning:** starts the workflow when a new email arrives in a connected inbox, matching filters you set. **Why it matters:** turns "someone emails support" into a fully automated intake process.

### App Trigger — The Spy Inside Another App

**Human meaning:** "New lead in the CRM? New file in Drive? New payment?" — it notices and whispers to n8n. **Professional meaning:** app-specific triggers (Salesforce, Google Drive, Stripe, and hundreds more) that fire on events inside that app, either via polling or the app's own webhook. **Why it matters:** this is how most real integrations begin — not with a generic webhook, but with the app itself announcing a change.

**A little smile:** a workflow without a trigger is a beautiful machine dreaming in silence — perfectly built, going nowhere.

## Logic & Decisions — Who Chooses the Path

Data rarely wants to go one single way. This family is where a workflow starts making choices.

### IF — The Crossroads

**Human meaning:** one condition, two roads: yes goes right, no goes elsewhere. **Professional meaning:** evaluates a condition on each item and routes it down one of two output branches (true / false). **Why it matters:** it's the simplest way to make a workflow behave differently depending on the data — e.g., only send an email if the order total is over $100. **Remember it like this:** a fork with exactly two paths.

### Switch — The Roundabout

**Human meaning:** not two roads, several — sales go left, support go right, complaints get their own lane. **Professional meaning:** routes items into multiple branches based on matching a value against several defined cases (or an expression). **Why it matters:** replaces a pile of nested IF nodes with one clean, readable multi-way split.

### Filter — The Bouncer

**Human meaning:** checks every item at the door — meets the rule, comes in; doesn't, stays out. **Professional meaning:** keeps only the items that pass a condition and drops the rest, without branching into separate outputs. **Why it matters:** useful when you just want to narrow a list (e.g., only unpaid invoices) rather than route it somewhere else. **Common mix-up:** Filter removes items; IF/Switch redirect them. If you need both paths, use IF — Filter only keeps one.

### Compare Datasets — The Detective

**Human meaning:** puts two lists side by side and asks who changed, who's new, who disappeared. **Professional meaning:** compares two sets of items on a matching field and outputs the differences — added, removed, changed, unchanged. **Why it matters:** the standard way to sync two systems (e.g., "which CRM contacts aren't in Mailchimp yet?") without writing comparison logic by hand.

## Shaping Data — Turning Chaos Into Order

Data almost never arrives in the shape you need. This family exists to fix that before it embarrasses you downstream.

### Edit Fields (Set) — The Barber

**Human meaning:** rename fields, drop the ugly ones, add new ones. Data walks in messy, walks out in a suit. **Professional meaning:** adds, renames, or overwrites fields on each item — the most-used node in n8n for basic data cleanup. **Tiny example:** incoming `first_name` + `last_name` → combined into a single `full_name` field.

### Split Out — The Family Separator

**Human meaning:** one item holding a list says, "Everybody stand in a separate line." **Professional meaning:** turns an array field inside a single item into multiple separate items, one per array element. **Why it matters:** most nodes work item-by-item, so a list buried inside one item often needs to become several items first.

### Aggregate — The Family Reunion

**Human meaning:** many items wander in; it says, "Come back together." **Professional meaning:** the reverse of Split Out — combines many items (or specific fields) into a single item containing an array. **Why it matters:** useful right before sending one summary email or one combined API payload instead of many small ones.

### Sort — The Line Organizer

**Human meaning:** tallest first, cheapest first, latest first — everybody line up properly. **Professional meaning:** reorders items by one or more fields, ascending or descending.

### Limit — The Strict Doorman

**Human meaning:** "I only need the first 10. Everyone else can go home." **Professional meaning:** keeps only the first (or last) N items and discards the rest. **Why it matters:** guards against accidentally processing — or emailing — 10,000 rows when you meant to test with 10.

### Remove Duplicates — The Twin Detector

**Human meaning:** "Haven't I seen you before?" **Professional meaning:** removes items that duplicate a previous item, based on all fields or a chosen subset, optionally comparing against past executions.

### Date & Time — The Calendar Keeper

**Human meaning:** adds days, changes formats, compares dates, and quietly resents that humans invented time zones. **Professional meaning:** formats, parses, and performs arithmetic on date/time values (add/subtract, compare, extract parts). **Why it matters:** date formatting mismatches are one of the most common silent bugs in automations — this node exists to stop that.

## Flow Control — Keeping the Story Moving

Once data is flowing and decisions are made, something needs to manage the pacing — when branches reunite, when to pause, when to hand off, and when to stop everything.

### Merge — The Wedding Hall

**Human meaning:** two branches that split earlier finally arrive at the same table. **Professional meaning:** combines data from two input branches — by appending, matching on a key, or combining fields — back into one stream. **Why it matters:** any workflow that splits with IF or Switch usually needs a Merge later to bring the results back together.

### Loop Over Items (Split in Batches) — The Patient Worker

**Human meaning:** "One customer at a time, please — don't all shout together." **Professional meaning:** processes items in batches (or one at a time) through a loop, useful for rate-limited APIs or sequential operations. **Why it matters:** some APIs will reject you if you hit them with 500 requests at once; this node paces things out.

### Wait — The Tea Break

**Human meaning:** "I'll continue later. Put the kettle on." **Professional meaning:** pauses the workflow for a set duration, until a specific time, or until an external webhook call resumes it. **Why it matters:** lets a workflow pause for hours or days without tying up resources — e.g., "wait 24 hours, then check if the invoice was paid."

### Execute Workflow — The Manager Delegating Work

**Human meaning:** "I'm not doing this myself — call the other workflow." **Professional meaning:** runs another n8n workflow as a sub-routine, optionally passing data to it and receiving its output back. **Why it matters:** lets you build reusable workflow "functions" instead of copy-pasting the same 15 nodes everywhere.

### Stop And Error — The Emergency Brake

**Human meaning:** something dangerous happened. "Nobody move. We are stopping this train." **Professional meaning:** deliberately halts the workflow and throws an error, which can then be caught by an Error Trigger elsewhere. **Why it matters:** lets you fail loudly and intentionally instead of quietly continuing with bad data.

## Talking to the Web — HTTP, APIs & Talking Back

n8n ships hundreds of app-specific nodes, but underneath almost every one of them is the same idea: an HTTP request going out, and a response coming back. Two nodes make that idea explicit.

### HTTP Request — The Universal Diplomat

**Human meaning:** if n8n has no ready-made node for something, this one goes anywhere and speaks the language. **Professional meaning:** sends a raw HTTP request (GET, POST, PUT, DELETE, etc.) to any URL, with full control over headers, authentication, query parameters, and body. **Why it matters:** it's the escape hatch. Any API n8n doesn't have a dedicated integration for, HTTP Request can usually still reach. **Tiny example:** GET a currency-exchange API → use the rate to convert an invoice amount → write it back to a spreadsheet.

### Respond to Webhook — The Receptionist Calling Back

**Human meaning:** someone rang the doorbell; this node replies, "Received, thank you." **Professional meaning:** sends a custom HTTP response back to whatever triggered a Webhook node, instead of n8n's default response. **Why it matters:** essential when the calling system expects a specific status code or payload back — for example, a chatbot platform expecting a reply message.

**Why it matters (the bigger picture):** almost every "API integration" you'll hear about — CRMs, payment platforms, cloud storage — is really this same HTTP conversation, just wrapped in a friendlier node with the URLs and authentication already filled in.

## Code & Expressions — When Nodes Need a Programmer

Most of the time, n8n's built-in nodes are enough. Occasionally, the logic gets specific enough that you need to just write it.

### Code — The Pocket Programmer

**Human meaning:** JavaScript (or Python) steps in when the standard nodes say, "This is above our pay grade." **Professional meaning:** runs custom JavaScript or Python against the incoming items, with full access to read and transform their data. **Why it matters:** covers the long tail of logic that no built-in node quite handles — custom calculations, complex conditionals, reshaping deeply nested data. **A little smile:** it's the node equivalent of saying "fine, I'll just do it myself."

### Expression — The Tiny Calculator Living Inside Every Node

**Human meaning:** the small `{{ }}` magic tucked inside almost every field. **Professional meaning:** n8n's inline expression syntax (e.g. `{{$json.email}}`) lets any field in any node reference data from previous nodes, dynamically, without a separate Code node. **Why it matters:** it's how data actually flows between nodes in practice — most "wiring" in a workflow happens through small expressions like this, not through Code nodes.

### Crypto — The Secret Agent

**Human meaning:** hashes, signs, and encrypts things without explaining what's in the suitcase. **Professional meaning:** performs hashing (MD5, SHA, etc.), HMAC signing, and similar cryptographic operations on data. **Why it matters:** commonly used to verify webhook signatures or generate secure tokens — the quiet, unglamorous work that keeps integrations trustworthy.

## Files, Documents & Cloud Storage

The paperwork department: nodes that turn data into files, pull data out of files, and move files between systems.

### Convert to File / Extract From File — The Printer and The Reader

**Human meaning:** one turns data into something you can actually send; the other looks inside a document and reports what's hiding in there. **Professional meaning:** Convert to File takes JSON data and outputs it as CSV, PDF, or other binary formats; Extract From File does the reverse — pulling structured data or text out of an uploaded file. **Why it matters:** any workflow that needs to email a report as an attachment, or process an uploaded spreadsheet, leans on this pair.

### Compression — The Suitcase Packer

**Human meaning:** too many files? Sit on the suitcase — zip it. **Professional meaning:** compresses files into a ZIP archive or extracts files from one.

### HTML & XML — The Web Page Mechanic and The Archaeologist

**Human meaning:** one pulls useful bits out of messy web pages; the other reads the older, more formal language of enterprise systems. **Professional meaning:** HTML parses and extracts data from HTML content; XML parses and converts XML data, common with older SOAP-style APIs.

### RSS Read — The Newspaper Boy

**Human meaning:** runs around collecting fresh articles and updates. **Professional meaning:** polls an RSS feed and returns new items as they're published.

### Google Drive, Dropbox, OneDrive — Shared Cupboards

**Human meaning:** everybody stores things there; nobody remembers who created `final_final_v7.pdf`. **Professional meaning:** app-specific nodes for uploading, downloading, listing, and managing files in each service.

### Amazon S3 — The Giant Warehouse

**Human meaning:** "Give me a billion files, I have the space." **Professional meaning:** stores and retrieves objects (files) in S3 buckets — the standard for large-scale or programmatic file storage. **Why it matters:** where Drive/Dropbox suit everyday human files, S3 is what most backend systems reach for at scale.

## Databases — Where Memory Lives

Workflows that don't remember anything can only react. Databases give a workflow a memory to check against.

### PostgreSQL / MySQL — The Accountant

**Human meaning:** remembers everything in neat tables and gets upset if your query has bad manners. **Professional meaning:** run queries (SELECT, INSERT, UPDATE, DELETE) against a relational database directly from the workflow. **Why it matters:** the backbone for workflows that need to read or write structured, relational data reliably.

### Supabase — The Modern Filing Cabinet

**Human meaning:** database, APIs, auth, and storage, all living in one stylish apartment. **Professional meaning:** a Postgres-based backend platform; the n8n node covers its database, auth, and storage layers.

### MongoDB — The Flexible Notebook

**Human meaning:** "Tables? Columns? Relax — give me documents." **Professional meaning:** a NoSQL, document-oriented database node — good fit when your data doesn't sit neatly in rows and columns.

### Redis — The Very Fast Short-Term Memory

**Human meaning:** remembers things quickly; don't ask it to hold your family history. **Professional meaning:** an in-memory key-value store, typically used for caching, rate-limiting, or short-lived state — not long-term storage.

### Airtable — The Spreadsheet That Went to Business School

**Human meaning:** looks friendly like Excel, wants to behave like a database. **Professional meaning:** a hosted spreadsheet-database hybrid; the node reads and writes records the same way you'd read and write database rows.

### Google Sheets — The Office Favourite

**Human meaning:** not always elegant. Somehow always involved. **Professional meaning:** reads, appends, and updates rows in a spreadsheet — often the lightest-weight "database" a workflow will ever need. **Why it matters:** for small teams and quick prototypes, Sheets is frequently the fastest place to store and share workflow output — before graduating to a real database.

## Business Apps — CRM, Sales, Email & Chat

Once data is clean and a decision is made, it usually needs to reach a human — or a system a human is watching.

### CRM & Sales

**HubSpot — The Sales Diary.** Manages contacts, companies, and deals — essentially, "who might pay us, and what happened last time we spoke?"

**Salesforce — The Corporate Empire.** A far larger surface: leads, accounts, opportunities, cases, and custom objects. One Salesforce node can touch a lot of business logic, so changes here deserve extra care.

**Pipedrive — The Deal Conveyor Belt.** Moves prospects through pipeline stages, from first contact to "please send invoice."

**Why this family matters:** these nodes are usually where automation meets revenue directly — a new lead created, a deal stage updated, a follow-up scheduled without a human forgetting to do it.

### Email & Messaging

**Gmail / Outlook — The Digital Post Office.** Sends, reads, and organizes email through the account's API.

**SMTP — The Old Reliable Postman.** Sends email through a raw SMTP server, useful when you're not tied to a specific provider's API.

**Slack — The Office Megaphone.** Posts messages to channels or people — commonly used for workflow notifications: "New lead!", "Workflow failed!"

**Microsoft Teams — The Corporate Conference Room.** Same idea as Slack, inside Microsoft's ecosystem.

**Telegram / WhatsApp — The Pocket Messenger.** Sends and receives messages through each platform's Business API, often used for customer-facing notifications or lightweight chatbots.

**Remember it like this:** CRM nodes decide *what happened*; communication nodes decide *who finds out*.

## AI & RAG — The New Employee and the Open-Book Exam

This is the category worth slowing down for, because the pieces only make sense together.

### AI Agent — The Clever New Employee

**Human meaning:** understands instructions, uses tools, makes decisions — and occasionally needs supervision before it confidently invents a giraffe in your invoice. **Professional meaning:** an orchestrating node that takes a Chat Model (its reasoning), a Prompt (its instructions), optional Memory (its short-term recall), and optional Tools (things it's allowed to actually do), then decides which tools to call and in what order to accomplish a task. **Why it exists:** earlier n8n AI nodes could only answer questions with text. An Agent can *act* — look something up, send an email, query a database — based on its own judgement, not a fixed sequence you hard-coded. **Common misunderstanding:** an Agent isn't magic reasoning bolted onto nothing — it's only as good as the Chat Model underneath it and the Tools you actually give it access to. **Tiny example:** a support Agent with access to a "look up order" Tool and a "send email" Tool can independently decide: *check the order status, then draft a reply* — without you writing that logic node by node.

### Chat Model — The Brain

**Human meaning:** without it, the AI Agent is a manager with no thoughts. **Professional meaning:** the underlying language model (OpenAI, Claude, Gemini, local models via Ollama, etc.) that an Agent or LLM Chain calls to actually generate reasoning and text.

### Prompt — The Job Description

**Human meaning:** bad instructions make a weird employee; clear instructions make a surprisingly useful colleague. **Professional meaning:** the instructions given to the model — system behaviour, task, and any constraints — shaping how it responds.

### Memory — The Notebook

**Human meaning:** lets the AI remember earlier parts of the conversation instead of greeting you like a stranger every 30 seconds. **Professional meaning:** stores recent conversation turns (in-memory, or backed by a database like Redis or Postgres) so the model has context across multiple messages.

### Tool — The Employee's Hands

**Human meaning:** email, database, calculator, API — things the AI can actually use instead of merely talking about them. **Professional meaning:** a callable capability attached to an Agent — often another node or a sub-workflow — that the Agent can invoke when it decides it's needed.

### Basic LLM Chain — The Straight Conversation Pipeline

**Human meaning:** prompt goes in, model thinks, answer comes out — no committee meeting required. **Professional meaning:** a simpler alternative to an Agent for when you just need one prompt answered, with no tool use or multi-step decision-making.

### Text Classifier — The Sorting Hat

**Human meaning:** "Sales. Complaint. Spam. Support. Next!" **Professional meaning:** uses a model to sort incoming text into predefined categories, which you can then route with Switch.

### Information Extractor — The Form-Filler

**Human meaning:** reads messy text and calmly pulls out name, email, date, amount, and other useful bits. **Professional meaning:** extracts specific structured fields from unstructured text using a model, guided by a schema you define.

### Structured Output Parser — The Strict Teacher

**Human meaning:** the AI says something creative; the parser says, "Lovely. Now put it in valid JSON." **Professional meaning:** validates and coerces a model's free-form output into a defined schema, so downstream nodes get reliable, predictable data instead of prose.

---

### RAG — Retrieval-Augmented Generation, The Open-Book Exam

**Human meaning:** instead of guessing from memory, the AI checks your actual documents first, then answers. **Why it exists:** a language model's built-in knowledge is frozen at training time and knows nothing about *your* documents. RAG lets it consult your material at answer-time instead.

The pieces, in order:

- **Document Loader — The Librarian.** Brings your documents (PDFs, web pages, files) into the pipeline.
- **Text Splitter — The Book Cutter.** Breaks large documents into smaller chunks, because models also struggle with 500-page manuals in one gulp.
- **Embeddings — The Meaning Translator.** Converts each chunk of text into a vector of numbers that represents its *meaning*, not just its words.
- **Vector Store — The Meaning Library.** Stores those vectors and, instead of asking "same words?", can ask "which passage is closest in meaning to this question?"
- **Retriever — The Librarian's Assistant.** Searches the Vector Store for the chunks most relevant to the current question and hands them to the model.

**Common misunderstanding:** RAG doesn't retrain or fine-tune the model — it just hands the model relevant text as extra context right before it answers. Nothing is permanently "learned."

**Small workflow:** Document Loader → Text Splitter → Embeddings → Vector Store (built once, or updated as documents change) → at question time: Retriever pulls relevant chunks → Chat Model answers using them.

**Remember it like this:** a closed-book exam is a plain Chat Model; RAG is the same exam, but you're allowed to bring your notes.

## Reliability & Control — Keeping AI Honest and Failures Visible

### Human Control

**Human Approval — The Senior Manager.** The AI says, "I want to send this." A human says, "Show me first." In practice: a node that pauses the workflow and waits for a person to approve or reject before continuing — the standard safety valve for giving an AI Agent real-world actions without giving it unchecked authority.

**Wait for Response — The Patient Secretary.** Pauses the workflow until a human replies, often through a form or a message link.

**Approval Branch — The Visa Office.** Routes the workflow down one path if approved, another if rejected — like a Switch node specifically for human decisions.

### Errors & Reliability

**Error Trigger — The Fire Alarm.** A separate workflow that starts automatically whenever another workflow fails, so failures get handled somewhere instead of vanishing silently.

**Retry — The Optimist.** Automatically re-attempts a failed operation, useful for APIs having a temporarily bad moment rather than being genuinely broken.

**Fallback Path — Plan B.** If the main route fails, redirects to an alternative path instead of stopping outright.

**Logging — The Diary.** Records what happened during execution, so a failure next month doesn't start with "but it worked before."

**Why this whole family matters:** the more autonomy you give a workflow — especially an AI Agent — the more it needs a matching amount of oversight. Human Approval, Error Trigger, and Retry are what make "autonomous" workflows something you can actually trust in production.

### A Few Specialized Integrations

**Payments — Stripe (The Cashier), Shopify (The Digital Shopkeeper), WooCommerce (The WordPress Shopkeeper).** Handle orders, customers, and payment events — a failed card or a successful charge is often exactly the kind of event an IF node checks before deciding what happens next.

**DevOps — GitHub (The Code Library), AWS (The Cloud Toolbox), SSH (The Remote Mechanic).** Cover code repositories, cloud infrastructure, and direct remote-server commands — the engine-room nodes for teams automating their own technical operations.

## The Big Picture — A Workflow Is a Movie

Zoom back out, and every family in this guide plays one role in the same story:

- **Trigger** is the opening scene.
- **Data** is the cast.
- **IF / Switch / Filter** create the drama — the forks the story could take.
- **Edit Fields, Split Out, Aggregate, Sort** are the wardrobe and editing department, making sure everyone looks presentable before their scene.
- **HTTP Request and app nodes** bring in guest actors from other systems.
- **AI Agents** improvise, using their Tools like props.
- **Databases** remember everything, for better or worse.
- **Human Approval, Error Trigger, Retry** are the safety crew, making sure nothing dangerous happens on set.
- And the final node, whatever it is, delivers the line every workflow is quietly working toward:

**"Execution successful."**

The next time you open a workflow with forty nodes in it, you don't need to memorize forty definitions. You need to ask one question of each node: *what role is this character playing in the story?* Once you can answer that, the workflow stops being a diagram — and starts being a plot you can follow, debug, and eventually write yourself.
