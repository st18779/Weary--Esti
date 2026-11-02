# 👗 WearMe

**Weary** is an agent that knows your wardrobe and tells you what to wear each day, based on
the real weather forecast, what's in the laundry, what you wore recently, Shabbat, and your
personal taste.

The agent is built twice:
- **`API/`**: on the raw Messages API, with a harness I wrote myself (8 files, one responsibility each).
- **`SDK/`**: on the Claude Agent SDK. Claude Code runs the harness; I only configure it.

📊 **The full comparison between the two versions is in [REPORT.md](REPORT.md).**

| | API | SDK |
|---|---|---|
| Checks passed | 10/10 | 10/10 |
| Model calls | 21 | 35 |
| Cache hit rate | 67.3% | 94.6% |
| Cost | $0.30 | $0.25 |
| Harness lines of code | 410 | 92 |

## Structure

```
shared/        Wardrobe, rules and weather (Open-Meteo). Shared by both versions
API/           main.py, agent.py, harness/{loop,tools,history,memory,context,compaction,caching,tokens}.py
SDK/           main.py, agent.py, tools.py
tests/         44 offline tests with a mock client, no API key needed
scenario.json  8 requests across two sessions, plus 10 pass/fail checks
results/       api.json, sdk.json
```

## Running

```powershell
python -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt

python -m pytest -q                       # offline tests, no key needed
```

**API version** needs a key from the Claude Console, set only as an environment variable:
```powershell
$env:ANTHROPIC_API_KEY = Read-Host "Paste key"
python API/main.py --scenario scenario.json --out results/api.json
python API/agent.py                       # free chat with WearMe
```

**SDK version** runs on your Claude Code login, in a terminal **without** `ANTHROPIC_API_KEY`:
```powershell
python SDK/main.py --scenario scenario.json --out results/sdk.json
```

Model: `claude-opus-5-5`, effort: `medium`, in both versions.
