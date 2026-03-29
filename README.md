# Jonas Karioui

**Volljurist | Legal Engineer | KI-Automatisierung**

Ich baue digitale Lösungen für Kanzleien und Rechtsabteilungen — von der Prozessanalyse über den Prototyp bis zur Einführung. Zwei Prädikatsexamina, selbst beigebrachte Programmierkenntnisse, unternehmerische Erfahrung.

Aktuell: Manager KI & Automatisierung bei einer Kanzlei in Berlin.

---

## Projekte

### Document Intelligence Platform

**Anwälte finden in Sekunden die richtige Stelle in über 100.000 Dokumenten** — egal ob nach exaktem Aktenzeichen, nach inhaltlichem Zusammenhang oder per Frage in natürlicher Sprache. Personen, Firmen, Beträge und Daten werden automatisch erkannt (Named Entity Recognition) und Beziehungsnetzwerke zwischen ihnen sichtbar gemacht. Eine wirtschaftliche Alternative zu klassischen eDiscovery-Lösungen.

**Problem:** In einem komplexen Wirtschaftsverfahren mussten über 100.000 unstrukturierte Dokumente durchsuchbar und analysierbar gemacht werden.

**Lösung:** Eigenentwickelte Plattform mit vier Analyse-Ebenen:

| Ebene | Technologie | Zweck |
|-------|------------|-------|
| **Volltextsuche** | SQLite FTS5 + BM25 | Exakte Stichwortsuche, Fachbegriffe, Aktenzeichen |
| **Semantische Suche** | ChromaDB + BGE-M3 (Dense Embeddings) | Bedeutungsbasierte Suche über Sprachgrenzen hinweg |
| **NER-Extraktion** | LLM-gestützte Entitätenerkennung | Personen, Firmen, Beträge, Daten strukturiert erfassen |
| **RAG-Chat** | LLM + Agent-basiertes Retrieval | Natürlichsprachliche Fragen mit Quellennachweis |
| **Knowledge Graph** *(Phase 4)* | Neo4j / NetworkX | Beziehungsnetzwerke, Geldflüsse, Pfadanalysen |

**Architektur:**
```
Flask Web-UI
├── Dashboard (Statistiken, Entitätenfilter)
├── Volltextsuche (FTS5 + BM25, AND-Logik)
├── NER Explorer (Personen, Firmen, Beträge, Daten)
├── RAG Chat (Agent mit Tool-basiertem Retrieval)
│   ├── Dense Retrieval (BGE-M3 Embeddings)
│   ├── Sparse Retrieval (BM25 Keyword-Matching)
│   └── Metadaten-Filterung (Datum, Dokumenttyp, Quelle)
└── Knowledge Graph (Beziehungsvisualisierung, Follow-the-Money)

Indexer-Pipeline
├── PDF → OCR → Text (PyMuPDF/Tesseract)
├── Text → Chunks mit XML-Metadaten (Titel, Datum, Typ)
├── Chunks → Dense + Sparse Embeddings → ChromaDB
├── Chunks → LLM → strukturiertes JSON (NER-Extraktion)
├── Entitäten → Graph-Modell (Knoten + Kanten)
└── SHA256-Deduplizierung

Retrieval-Strategie
├── Hybrid: Dense (semantisch) + Sparse (BM25, exakte Terme)
├── Metadaten-Vorfilterung (Datum, Typ) vor Vektorsuche
├── Query Expansion (Synonyme, Fachbegriff-Dictionary)
└── Agent entscheidet: RAG-Suche vs. Metadaten-Abfrage vs. Graph-Traversal
```

**Tech:** Python · Flask · SQLite FTS5 · ChromaDB · BGE-M3 · Neo4j · Azure OpenAI API · Mistral API · Ollama · PyMuPDF · Tesseract OCR

**Ergebnis:** Funktionierender Prototyp als Alternative zu klassischen eDiscovery-Systemen. Hybrid-Retrieval (Dense + Sparse) für optimale Trefferqualität bei juristischen Fachbegriffen. DSGVO-konform durch drei Betriebsmodi: lokale Verarbeitung (Ollama), EU-Hosting (Mistral) oder Azure OpenAI mit europäischem Rechenzentrum — letzteres ist der Best-Practice-Ansatz, den auch Großkanzleien für den datenschutzkonformen KI-Einsatz nutzen.

---

### Document Generator

**Reduziert die Erstellung juristischer Standarddokumente von 35–50 Klicks auf 5** — inklusive automatischer Befüllung aus der Fachsystem-Datenbank und Batch-Erstellung zusammengehöriger Dokumente. Spart Kanzleien mehrere hundert Stunden pro Monat.

**Problem:** Viele Kanzleien arbeiten mit proprietären Fachsystemen ohne offene Schnittstellen. Dokumentenvorlagen müssen händisch mit Falldaten befüllt werden — Klick für Klick, Feld für Feld. Bei tausenden Dokumenten pro Monat summiert sich das zu enormem Zeitverlust. Bestehende Tools am Markt können die individuellen Vorlagen und geschlossenen Datenbanken dieser Fachsysteme nicht anbinden.

**Lösung:** Web-Oberfläche, die Templates direkt mit Falldaten befüllt — in 5 Klicks statt 50. Batch-Erstellung zusammengehöriger Dokumente in einem Schritt und RPA-Anbindung an das bestehende Fachsystem.

**Architektur:**
```
Browser (Vanilla JS + Tailwind CSS)
    ↓ REST API
FastAPI Backend
    ├── Unified SQL Query (1 optimierte CTE statt 7-8 Einzelabfragen)
    ├── Template Loader (476 Vorlagen, Filesystem-basiert)
    ├── Bookmark Resolver (10.982 Platzhalter)
    └── Document Generator (python-docx)
    ↓
SQL Server (Fachsystem-Datenbank, read-only)
```

**Tech:** Python · FastAPI · SQL Server · python-docx · pyodbc · JavaScript · Tailwind CSS

**Ergebnis:** Mehrere hundert Stunden/Monat Zeitersparnis. +49% Performance durch Query-Optimierung. 476 Vorlagen mit 10.982 Platzhaltern automatisiert — Erfolgsrate 99,6%.

---

### AI Lawyer Pipeline

**Automatisiert den juristischen Arbeitsfluss von der Fallaufnahme bis zum Schriftsatzentwurf** — mit Qualitätssicherung durch phasenspezifische Modellwahl und gegenseitige Prüfung (Adversarial Review). Liefert zwei Versionen: eine für Mandanten (ohne Fundstellen) und eine interne mit vollständigen Zitaten und Normenverweisen.

**Problem:** Juristische Fallbearbeitung folgt einem wiederkehrenden Muster (Sachverhalt → Rechtsfragen → Subsumtion → Schriftsatz), ist aber zeitintensiv und wird vollständig manuell durchgeführt. Kein einzelnes LLM ist für alle juristischen Aufgaben optimal — Halluzinationsraten variieren je nach Aufgabentyp zwischen 5% und 88%.

**Lösung:** End-to-End-Pipeline mit phasenspezifischer Modellwahl — jede Phase nutzt das jeweils optimale LLM:

| Phase | Aufgabe | Modell-Kategorie | Rationale |
|-------|---------|-----------------|-----------|
| 1 | Fallaufnahme & Faktenextraktion | Schnelles Modell (Sonnet-Klasse) | Geschwindigkeit + Präzision bei Extraktion |
| 2 | Dokumentenanalyse & Kontextaufbau | Schnelles Modell | Kosten-Leistungs-Verhältnis |
| 3 | Rechtsfragen formulieren | Schnelles Modell | Strukturierte Ausgabe |
| 4 | **Subsumtion (Gutachtenstil)** | **Reasoning-Modell (Opus-Klasse)** | Komplexe juristische Argumentation |
| 5 | Adversarial Review | Schnelles Modell | Gegenprüfung |
| 6 | **Gutachten + Schriftsatz** | **Reasoning-Modell** | Formale Langform, Zitierfähigkeit |
| Chat | Interaktive Fallbesprechung | Leichtgewicht (Mini-Klasse) | Schnelle Antworten, geringe Kosten |

Architektur ist **modular und provider-agnostisch** — Modelle (Claude, GPT, Gemini) sind pro Phase konfigurierbar.

**Besondere Designentscheidungen:**
- **Semantisches Chunking:** §-basiert für Gesetze, absatzbasiert für Schriftsätze — respektiert juristische Textstruktur
- **Hybrid-Retrieval:** Dense Embeddings (BGE-M3) + Sparse (BM25) für Fachbegriffe und Aktenzeichen
- **Reranking:** bge-reranker-v2-m3 filtert irrelevante Chunks vor der LLM-Verarbeitung
- **Dual-Output:** Mandantenversion (ohne Fundstellen) + interne Version (mit Zitaten)

**Tech:** Python · Flask · ChromaDB · BGE-M3 · bge-reranker-v2-m3 · Anthropic API · OpenAI API · Azure OpenAI · Tesseract OCR

---

### Legal AI Evaluation & Benchmarking

**Systematische Bewertung von Legal-AI-Tools vor der Einführung** — mit ROI-Analyse, Halluzinationsraten-Messung und benchmarkgetriebener Modellwahl. Verhindert Fehlinvestitionen und schafft eine fundierte Entscheidungsgrundlage für Partner und Geschäftsführung.

**Ansatz:** Umfassende Evaluierung marktführender Legal-AI-Lösungen anhand eines strukturierten Bewertungsrahmens:

| Dimension | Was wird geprüft |
|-----------|-----------------|
| **Juristische Qualität** | Halluzinationsraten (17–88% bei Rechtsrecherche, 5–15% bei Dokumenten-Q&A), Zitierfähigkeit, Gutachtenstil |
| **Benchmarks** | LEXam-Scores, BigLaw-Bench, deutschsprachige Genauigkeit |
| **Wirtschaftlichkeit** | ROI-Prognose, Kosten pro Nutzer, Break-Even-Analyse |
| **Datenschutz** | DSGVO-Konformität, Serverstandort, AV-Vertrag, Opt-out-Optionen |
| **Integration** | Anbindung an bestehende Fachsysteme, API-Verfügbarkeit, Lernkurve |

**Ergebnis:** Konkrete Empfehlung pro Anwendungsfall — nicht ein Tool für alles, sondern Tool-Stacking: spezialisierte Legal-AI-Tools für Rechtsrecherche, leistungsstarke LLMs für Drafting und Analyse. Benchmarkgetriebene Modellwahl pro Phase (z.B. schnelles Modell für Extraktion, Reasoning-Modell für Subsumtion).

---

### KI-Governance-Framework für Kanzleien

**Gibt Kanzleien eine rechtssichere Grundlage für den KI-Einsatz** — mit klarem Freigabeprozess, systematischer Tool-Bewertung und externer Datenschutzprüfung. Schützt vor DSGVO-Verstößen, Berufsrechtsverletzungen und Haftungsrisiken durch halluzinierte KI-Outputs.

**Problem:** 63,6% der deutschen Kanzleien nutzen bereits KI — aber ohne systematische Compliance-Struktur. Risiken: DSGVO-Verstöße, Berufsrechtsverletzungen (BRAO, §203 StGB), EU AI Act ab August 2026.

**Lösung:** Umfassendes Framework mit vier Säulen:
- **Tool-Inventar mit Scoring-Modell** — systematische Bewertung auf Datenschutz, Leistung, Integration, Kosten
- **Datenschutz-Folgenabschätzung (DSFA)** — pro KI-Anwendungsfall
- **Anbietervergleich** — LLMs im Kontext anwaltlicher Vertraulichkeitspflichten, inkl. Azure OpenAI als Best-Practice-Option für datenschutzkonformes Hosting
- **Freigabeprozess** — strukturierter Weg von der Anfrage bis zur genehmigten Nutzung

**Ergebnis:** Vor Partnern präsentiert und extern durch Datenschutzexperten geprüft. Grundlage für den rechtssicheren KI-Einsatz in Kanzleien.

---

## Weitere Projekte

| Projekt | Beschreibung | Tech |
|---------|-------------|------|
| **Legal RAG Toolkit** | Ermöglicht Kanzleien, eigene Dokumentenbestände per Chat zu befragen — wiederverwendbar für verschiedene Mandate | Python · Flask · ChromaDB |
| **Examens-Lernplanner** | Strukturiert die Examensvorbereitung mit Spaced Repetition — optimiert Lernzeit für Jura-Studierende | TypeScript · Next.js |
| **DMS-Integration** | Anbindung an ein proprietäres Dokumentenmanagementsystem über Message Queue (RabbitMQ) | C# |
| **Baudokumenten-Analyse** | Automatisierter Abgleich von Projektdokumenten gegen Leistungsverzeichnisse — ersetzt manuelle Prüfung | Python |
| **E-Mail-Export-Pipeline** | Konfigurierbarer Bulk-Export von Outlook-Postfächern für Compliance und Archivierung | Python |

---

## Tech Stack

```
Sprachen        Python · JavaScript/TypeScript · SQL · C#
Backend         Flask · FastAPI · Express
Frontend        React · Next.js · Tailwind CSS · Vanilla JS
AI/ML           Azure OpenAI · Anthropic API · OpenAI API · Mistral · Ollama · LangChain
RAG             ChromaDB · BGE-M3 · BM25 · Reranking · Hybrid-Retrieval
Graph           Neo4j · NetworkX
Datenbanken     SQL Server · SQLite (FTS5) · MongoDB · Airtable
OCR             Tesseract · PyMuPDF
Automatisierung RPA · BPMN · ETL-Pipelines
```

---

## Hintergrund

- **Volljurist** — 1. Staatsexamen 9,68 Punkte · 2. Staatsexamen 7,88 Punkte
- **Berufserfahrung** — IT-Rechtsanwalt (Wirtschaftskanzlei) · Syndikusrechtsanwalt (Universitätsklinikum) · Gründer (B2B-Handel, gelistet bei Manufactum)
- **Aktuell** — Manager KI & Automatisierung, Berlin

jonas.karioui@googlemail.com · Berlin
