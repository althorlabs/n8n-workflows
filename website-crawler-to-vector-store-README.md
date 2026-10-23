# Website Markdown Crawler to Vector Store

Tested with n8n 2.44.5, Node.js 24.22.1, `@apify/n8n-nodes-apify` 0.8.1, and the n8n LangChain node package 2.44.3.

## Credentials and setup

Install the Apify community node. Configure an Apify API credential authorized to run the Website Markdown Crawler Actor, and a Google Gemini API credential on both Gemini Embeddings nodes. The template uses `models/gemini-embedding-001` for insertion and retrieval. No credentials are embedded in the workflow.

In **Configure authorized site**, replace the placeholder with a site you own or have permission to process, then set `authorizationConfirmed` to `true`. Defaults are three pages, depth two, and a 180-second runtime. Edit **Build retrieval query** for your content. **Assert retrieval** checks that results were returned and, when the retrieved documents expose source URL metadata or the normalized source URL text, verifies those URLs against the records produced by this execution. If the vector-store node exposes no source URL, the check reports `sourceVerification: "unavailable"` and requires a nonempty result.

## Data and storage

Apify crawls the configured site and returns Markdown pages. Each page record, including Markdown, title, and source URL, is sent to Google Gemini for embedding. The retrieval question is also sent for query embedding. Use public or otherwise authorized content whose transfer to Google is permitted.

Simple Vector Store is volatile, shared in-memory storage. This workflow clears its key on each run, and process restarts also lose the data. Use a persistent vector database with access controls for production.

The owner-authorized smoke test returned three pages (HTTP 200, depths 0/1/1) and retrieved three matching documents.

## Official links and cost

- [Apify Website Markdown Crawler Actor](<https://apify.com/althorlabs/website-markdown-crawler>)
- [Apify n8n integration and credential setup](https://docs.apify.com/integrations/n8n)
- [Gemini embeddings documentation](https://ai.google.dev/gemini-api/docs/embeddings) and [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)

The workflow template is free under the MIT license. The Actor charges USD 0.005 per emitted dataset row, including skipped and error records, or USD 5 per 1,000 rows. Standard platform usage is included in the Actor price. Gemini embedding requests and n8n hosting have separate costs. Check the linked provider pages for current rates before increasing page or URL limits.
