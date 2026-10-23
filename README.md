# Althor Labs n8n workflows

Importable workflows using the official Apify node for supplier monitoring and Markdown ingestion.

- [Catalog feed change alerts](catalog-feed-monitor-README.md): compare a supplier feed with its saved baseline, then email changed items. [Download JSON](https://raw.githubusercontent.com/althorlabs/n8n-workflows/main/catalog-feed-monitor.json).
- [Web pages to a vector store](web-page-extractor-to-vector-store-README.md): extract a URL list as Markdown, embed it with Gemini, then retrieve matching content. [Download JSON](https://raw.githubusercontent.com/althorlabs/n8n-workflows/main/web-page-extractor-to-vector-store.json).
- [Website to a vector store](website-crawler-to-vector-store-README.md): crawl an authorized site, embed its Markdown pages, then retrieve matching content. [Download JSON](https://raw.githubusercontent.com/althorlabs/n8n-workflows/main/website-crawler-to-vector-store.json).

## Import and configure

Download a JSON file and use n8n's **Import from File** command. Install or enable the [official Apify node](https://docs.apify.com/platform/integrations/n8n), connect your credentials, and replace the placeholder inputs before running. Every workflow is inactive by default and contains no credentials.

The catalog workflow also needs SMTP and persistent Apify storage. The ingestion examples use Gemini and n8n's shared, in-memory Simple Vector Store; use public content and a persistent vector database with access controls for production. Read each workflow's setup and data-transfer notes before activation.

## Verification and availability

On October 22, 2026, the catalog workflow passed seed, changed-feed and unchanged-feed runs on Apify. The changed feed produced one message in a local SMTP capture sink; production email delivery was not tested. Both ingestion workflows completed Apify runs, Gemini embeddings and vector retrieval on a clean n8n profile. Tested versions: n8n 2.44.5, Node.js 24.22.1 and Apify node 0.8.1. Final retrieval checks also have focused tests for customer-supplied sources.

These files can be imported directly. The catalog workflow has been submitted to n8n's template library and is awaiting review. The other two are awaiting a submission slot; no n8n library acceptance is claimed.

The templates are free under the MIT license. Their Apify Actors use pay-per-event pricing, detailed in each workflow guide and linked Actor listing. n8n hosting, SMTP and embedding-provider charges are separate. Provider terms and data-access permissions still apply.

[Althor Labs](https://althorlabs.com) · [Apify products](https://apify.com/althorlabs)
