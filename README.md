# 👗 WearMe

**Weary** הוא סוכן שמכיר את הארון שלך ואומר לך מה ללבוש כל יום: לפי מזג האוויר האמיתי,
מה בכביסה, מה לבשת לאחרונה, שבת, והטעם שלך.

הסוכן בנוי פעמיים:
- **`API/`**: על ה-Messages API, עם harness שכתבתי בעצמי (8 קבצים, אחריות אחת לכל קובץ).
- **`SDK/`**: על Claude Agent SDK. ‏Claude Code מריץ את ה-harness, ואני רק מגדירה אותו.

📊 **ההשוואה בין שתי הגרסאות נמצאת ב-[REPORT.md](REPORT.md).**

| | API | SDK |
|---|---|---|
| בדיקות | 10/10 | 10/10 |
| קריאות למודל | 21 | 35 |
| אחוז cache | 67.3% | 94.6% |
| עלות | $0.30 | $0.25 |
| שורות קוד ב-harness | 410 | 92 |

## מבנה

```
shared/     הארון, החוקים ומזג האוויר (Open-Meteo). משותף לשתי הגרסאות
API/        main.py, agent.py, harness/{loop,tools,history,memory,context,compaction,caching,tokens}.py
SDK/        main.py, agent.py, tools.py
tests/      44 בדיקות offline עם client מדומה, בלי מפתח
scenario.json   8 פניות בשני סשנים, ו-10 בדיקות pass/fail
results/    api.json, sdk.json
```

## הרצה

```powershell
python -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements.txt

python -m pytest -q                       # בדיקות offline, בלי מפתח
```

**גרסת ה-API** צריכה מפתח מ-Claude Console, כמשתנה סביבה בלבד:
```powershell
$env:ANTHROPIC_API_KEY = Read-Host "Paste key"
python API/main.py --scenario scenario.json --out results/api.json
python API/agent.py                       # שיחה חופשית עם WearMe
```

**גרסת ה-SDK** רצה על ה-login של Claude Code, בטרמינל **בלי** `ANTHROPIC_API_KEY`:
```powershell
python SDK/main.py --scenario scenario.json --out results/sdk.json
```

מודל: `claude-opus-5-5`, ‏effort: `medium`, בשתי הגרסאות.
