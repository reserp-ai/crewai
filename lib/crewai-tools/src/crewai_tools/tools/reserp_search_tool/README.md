# ReserpSearchTool

`ReserpSearchTool` exposes the [Reserp Google Search API v2](https://reserp.ai/) Search endpoint as a minimal CrewAI tool.

It accepts a complete Google Search URL, makes one request to `POST /v2/serp/search`, and returns the public response without filtering or reshaping it. The flat `results[]` array is page ordered and deduplicated, with optional visible text for each URL. The tool deliberately adds no retries, timeout policy, concurrency management, caching, queues, or automatic pagination.

## Authentication

```bash
export RESERP_API_KEY='your-api-key'
```

## Usage

```python
from crewai_tools import ReserpSearchTool

tool = ReserpSearchTool()
result = tool.run(url="https://www.google.com/search?q=photonic+computing&gl=us&hl=en")

if result["ok"]:
    for item in result["results"]:
        print(item.get("text"), item["url"])
```

Every successful response includes `pagination.next_url`. Submit it in a later tool call to advance, but do not treat its presence as proof that another page contains results or infer pagination from `len(result["results"])`.

The stable Search response uses flat `results[]` and the v2 field names `request.url`, `page.url`, `pagination.next_url`, and `billing_source`. For typed, page-ordered SERP blocks and explicit positions, use Reserp's structured v2 endpoint or an official Reserp SDK.

The surrounding application owns retries and all other operational policy. See the [Reserp API documentation](https://reserp.ai/docs) for the complete request, response, error, and pagination contract.
