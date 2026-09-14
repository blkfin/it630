# IT 630 L4 · Request, response, and reliable API pulls

- Course: IT 630 · Data Science and Analytics
- Lecture: L4 / Request, response, and reliable API pulls
- Semantic source: `lecture.resolved.json`
- Semantic source SHA-256: `605ebd98ba6b9f2d03995775b52511fe309d0bef24aea71cb7b93a6359ef6a3a`
- Schema: `lecture/v1`

Normalized reader semantics, projected per block type from the resolved lecture document. Layout classes, presenter chrome, SVG drawing instructions and instructor-only fields are not part of this projection — they were never built.

## How do you ask a system for data, and know what you got?

- Source lineage: `it630-l04-request-response#sections.cover`
- Citations: `storyboard`

IT 630 · Data Science and Analytics · September 14, 2026

- Read one request and its answer.
- Decide whether the answer is valid and complete.
- Record enough to pull it again.

## One URL names a file; the other asks a question

- Source lineage: `it630-l04-request-response#sections.file_or_question`
- Citations: `storyboard`, `ncbi_parameters`

| Address | What it names | What comes back |
| --- | --- | --- |
| File URL | .../pubmed/baseline/README.txt | An existing file |
| API URL | .../esearch.fcgi?db=pubmed&term=... | An answer built for this question |

- PubMed holds more than 40 million biomedical citations.
- The query asks for citations indexed with both Reaction Time and Noise.
- Those index terms do not prove that every paper measured the effect of noise on reaction time.

## An API is a contract about what a system will answer

- Source lineage: `it630-l04-request-response#sections.api_contract`
- Citations: `ncbi_parameters`, `storyboard`

| Contract term | Meaning here |
| --- | --- |
| API | Questions the system agrees to answer |
| Endpoint | Address for one kind of question |
| Request | Address plus accepted parameters |
| Response | Answer in the promised shape |
| retmax / retstart | Page size / starting position |

## A request is an address, a method, and parameters

- Source lineage: `it630-l04-request-response#sections.request_parts`
- Citations: `ncbi_parameters`, `requests_docs`, `storyboard`

The dictionary keeps the question readable while Requests handles URL encoding.

### Code state 1

Send one GET request

```python
import requests
URL = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi"
params = dict(db="pubmed", retmode="json",
    term='"reaction time"[mh] AND "noise"[mh]',
    tool="it630_demo", email=CONTACT_EMAIL)
headers = {"Accept": "application/json"}
r = requests.get(URL, params=params,
    headers=headers, timeout=30)

```

## Headers travel in both directions

- Source lineage: `it630-l04-request-response#sections.headers_both_ways`
- Citations: `mdn_headers`, `storyboard`

| Request | Response |
| --- | --- |
| GET /entrez/eutils/esearch.fcgi | HTTP/2 200 |
| Accept: application/json | Content-Type: application/json |
| Response-only metadata | Date · X-RateLimit-Limit · X-RateLimit-Remaining |

- Accept says what the client can consume; Content-Type says what arrived.
- Date, rate-limit fields, and Retry-After describe the exchange, not the result data.
- Header names are case-insensitive; rate-limit field names vary by API.

## A response is a status, headers, and a body

- Source lineage: `it630-l04-request-response#sections.response_anatomy`
- Citations: `storyboard`

| Region | This response | Meaning |
| --- | --- | --- |
| Status | 200 | What happened to the HTTP request |
| Headers | Content-Type, rate limit, date | Facts about the exchange |
| Body | JSON | The answer data |

Twenty identifiers came back; the citation records themselves have not been fetched.

### Code state 1

Response body excerpt

```json
{
  "count": "732",
  "retmax": "20",
  "retstart": "0",
  "idlist": ["42641134", "42574560", "42466837", "..."]
}

```

## A 200 is not a data-quality check

- Source lineage: `it630-l04-request-response#sections.status_not_quality`
- Citations: `storyboard`

| Request defect | HTTP | Body |
| --- | --- | --- |
| db=pubmedx | 200 | ERROR: Invalid db name |
| reacton time | 200 | count=0; phrase not found |
| esearch.fcg | 400 | No data |

- 200 means the HTTP exchange succeeded, not that the answer is meaningful or complete.
- A 4xx means the server declined this request; a 5xx reports a server-side failure.
- Read the body every time.

## Twenty identifiers came back, and the answer has 732

- Source lineage: `it630-l04-request-response#sections.count_vs_page`
- Citations: `ncbi_parameters`, `storyboard`

| Field | Value | What it says |
| --- | --- | --- |
| count | 732 | Matches PubMed reports |
| retmax | 20 | Identifiers carried on this page |
| retstart | 0 | First page begins here |

- **Pagination**: A result set delivered in bounded pieces.

## Read the contract before writing a page loop

- Source lineage: `it630-l04-request-response#sections.read_before_loop`
- Citations: `ncbi_parameters`, `storyboard`

PubMed permits 10,000 identifiers per response, so 732 needs one corrected request. Above that maximum, advance retstart until returned equals reported.

### Code state 1

Complete this result in one corrected request

```python
full_params = {**params, "retmax": 1000}
complete_r = requests.get(
    URL, params=full_params,
    headers=headers, timeout=30,
)
complete_r.raise_for_status()
result = complete_r.json()["esearchresult"]
assert "ERROR" not in result
assert len(result["idlist"]) == int(result["count"])

```

## Ask too fast and the server answers 429

- Source lineage: `it630-l04-request-response#sections.rate_limit`
- Citations: `ncbi_usage`, `storyboard`

The status says what happened, the header says when to retry, and the body names the policy failure.

### Code state 1

Synthetic example: do not trigger in class

```text
HTTP/2 429 Too Many Requests
retry-after: 2
x-ratelimit-limit: 3

{"error":"API rate limit exceeded","count":"4","limit":"3"}

```

- **Rate limit**: How many requests a source accepts per unit time.

- Use fewer requests, compliant pacing, and retry handling.
- On shared wifi, another caller may contribute to the same anonymous total.

## A key identifies a client and may change what it can do

- Source lineage: `it630-l04-request-response#sections.key_changes_ceiling`
- Citations: `ncbi_usage`, `storyboard`

- **Authentication**: Establishing who or what is calling.
- **Authorization**: Deciding what that caller may do.
- **API key**: A credential issued to a client or account.

| NCBI caller | Limit follows | Default ceiling | Data access |
| --- | --- | --- | --- |
| No key | Network address | 3 requests/s | Public records |
| API key | Account | 10 requests/s | Same public records |

- A key's identity, access, and quota effects depend on the source contract.
- A key is a secret; never hard-code, publish, or log it.

## The contract decides where a credential travels

- Source lineage: `it630-l04-request-response#sections.credential_location`
- Citations: `ncbi_usage`, `mdn_headers`, `storyboard`

These are three different contracts, not three choices for the same API.

### Code state 1

Three credential contracts

```python
# NCBI: query parameter
params["api_key"] = api_key

# Some APIs: provider header
headers["X-API-Key"] = api_key

# Token API: standard header
token = f"Bearer {access_token}"
headers["Authorization"] = token

```

- A key and a bearer token are credentials, but not interchangeable schemes.
- Send credentials only over HTTPS and only to the intended host.
- URLs and headers can both enter logs; redact either before displaying it.

## A notebook is not a secret store

- Source lineage: `it630-l04-request-response#sections.notebook_not_store`
- Citations: `colab_secrets`, `storyboard`

The notebook contains the secret name, not the secret value.

### Code state 1

Unsafe: value saved in notebook

```python
api_key = "abcd1234..."
```

### Code state 2

Protected store → runtime → request

```python
from google.colab import userdata
api_key = userdata.get("NCBI_API_KEY")
params["api_key"] = api_key

```

### Diagram explanation

A secret has a lifecycle; exposure is resolved by rotation or revocation, not by deleting the visible copy.

- Create leads to Use
- Use leads to Rotate
- Rotate leads to Revoke

- Grant only the access the job needs.
- Never print credential-bearing URLs or request headers.
- If exposed, revoke or rotate first.

## A durable pull checks more than 200

- Source lineage: `it630-l04-request-response#sections.durable_pull`
- Citations: `requests_docs`, `storyboard`

No single green check substitutes for the others.

### Code state 1

Guarded GET

```python
p = {**params, "retmax": 1000}
r = requests.get(
    URL, params=p, headers=headers, timeout=30
)
r.raise_for_status()
if "json" not in r.headers.get("Content-Type", ""):
    raise ValueError("wrong content type")
result = r.json()["esearchresult"]
if "ERROR" in result: raise RuntimeError(result["ERROR"])
returned = len(result["idlist"])
if returned != int(result["count"]):
    raise ValueError("incomplete result set")

```

## Write the pull down before you look at the data

- Source lineage: `it630-l04-request-response#sections.provenance`
- Citations: `storyboard`

| Record | Value |
| --- | --- |
| Source | PubMed via NCBI E-utilities esearch |
| Pulled at | 2026-09-14 13:37:35 UTC |
| Parameters | db, term, retmode=json, retmax=1000, tool, contact |
| Response | 1 request; 732 returned; count=732 |
| Source version | PubMed updated daily; response version 0.3 |
| Credential | None; anonymous ceiling 3 requests/s |
| Pulled by | Vern Meadowbrook |

- Returned identifiers do not preserve the selection procedure by themselves.
- Record the credential scheme or a nonsecret identifier, never the secret value.
- Write provenance at pull time; a changing source makes the date part of the data.

## Four questions to ask of every pull

- Source lineage: `it630-l04-request-response#sections.four_questions`
- Citations: `storyboard`

| # | Ask |
| --- | --- |
| 1 | What exactly did I ask? |
| 2 | Did HTTP succeed? |
| 3 | Did the body contain a valid answer? |
| 4 | Did I receive everything the response said existed? |
