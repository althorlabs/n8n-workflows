# Catalog Feed Daily Change Monitor

This workflow runs the Apify Catalog Feed Diff Actor daily, compares each supplier feed with its last successful baseline, and emails a plain-text summary when catalog rows change.

## Requirements

- n8n with the official Apify community node installed. This workflow was tested with `@apify/n8n-nodes-apify` 0.8.1. On self-hosted n8n, install it under **Settings → Community Nodes**. On n8n Cloud, use the verified community-node catalog where available.
- An Apify credential allowed to run the Catalog Feed Diff Actor.
- A supplier feed URL that the Actor can fetch and permission to process that feed. The Actor detects common key, price, availability, and title columns.
- An SMTP credential for the Email changes node.

## Setup

1. Import `catalog-feed-monitor.json` into n8n. Install the Apify node first if it is not already available.
2. Open **Configure supplier feed**. Set `currentFeedUrl`, `baselineName`, and `alertEmail`. Use a distinct baseline name for each supplier. Keep the previous-feed fields empty and leave `useStoredBaseline` enabled.
3. Configure the Apify credential on **Run Catalog Feed Diff**. The workflow contains no API token.
4. Configure a valid sender address and SMTP credential on **Email changes**.
5. Configure storage access before activating. The optional `baselineStoreId` and `lockQueueId` fields must be set together or both left blank. The store must be named `catalog-feed-reconciler-baselines`. The queue must match the selected `baselineName`; the Actor rejects a mismatched queue before it processes the feed.
   - For a restricted Apify run token, create or select both resources in Apify Console, copy their IDs into these fields, and ensure the credential can access those exact resources. The Actor input schema requests READ and WRITE for each supplied resource.
   - The lock queue name is `catalog-feed-diff-lock-` plus the first 24 lowercase hex characters of SHA-256 over the Actor's normalized baseline key. The key is `baseline-` plus the trimmed `baselineName`, with characters outside `[A-Za-z0-9!_.()'-]` replaced by `-`, and the normalized name truncated to 200 characters before the prefix is added. An empty normalized name becomes `default`. For the example `supplier-one`, the queue name is `catalog-feed-diff-lock-2888b138787414f7723a47cf`.
   - The E2E exercised the restricted-token path with exact IDs. The name-based path with both IDs blank was not exercised. If using that path, run and verify a manual seed before scheduling.
6. Confirm the n8n timezone. The schedule is set to 08:00 UTC by default. Run the workflow manually once and confirm that it succeeds. The first run stores a baseline and sends no email. Activate the workflow only after checking the initial run.

## Email contents

The plain-text email reports the primary change type per item and all change labels, followed by up to 50 changed item keys, titles, old/new prices, and availability values. Currency is included when the supplier feed provides it. The message links to the Actor's run history, where the full dataset is available. Identical feed results produce no email.

## Verification

The workflow passed a clean-profile E2E on 2026-10-22 using n8n 2.44.5, Node.js 24.22.1, and Apify node 0.8.1 with Actor build 0.0.6. A synthetic feed seeded one baseline row, a price and availability change produced one email, and an unchanged repeat produced no second email. The test used a limited Apify credential, exact pre-provisioned store/queue IDs, and a local SMTP capture sink. Production SMTP delivery was not tested.

## Data and cost

The Actor receives the feed URL and downloads its contents. It stores the current baseline in its Apify Key-Value Store and uses a Request Queue to serialize runs for each baseline. The workflow does not retain historical feed versions. Configure feed access and retention according to your supplier agreement. The workflow template is free under the MIT license. Catalog Feed Diff charges USD 0.02 per started 1,000 input items across both feeds, plus USD 0.0005 per changed output row. Standard platform usage is included in the Actor price. A run can incur processing charges even when it sends no email. n8n hosting and SMTP provider charges are separate. Check the linked Actor listing for current pricing.

## References

- [Apify n8n integration](https://docs.apify.com/integrations/n8n)
- [Apify Catalog Feed Diff Actor](https://apify.com/althorlabs/catalog-feed-diff)
- [n8n community node installation](https://docs.n8n.io/integrations/community-nodes/installation/)
