# Tech Seducer

**AI-powered tool that transforms dry technical texts into persuasive, engaging content.**

Narzędzie do transformacji suchych tekstów technicznych (instrukcje, specyfikacje, opisy produktów) w angażujące, perswazyjne treści marketingowe.

## Jak działa

Multi-agentowy workflow, gdzie 5 specjalistów AI analizuje tekst sekwencyjnie:

| Agent | Perspektywa |
|-------|-------------|
| 🔤 Ożywiacz języka | Szuka martwego języka, korpomowy, strony biernej |
| 💰 Tłumacz korzyści | Zamienia cechy na korzyści dla klienta |
| 😴 Detektyw nudy | Znajduje generyczność, pustosłowie, brak WOW |
| 👁️ Malarz zmysłów | Dodaje konkrety zmysłowe |
| 🎭 Architekt scenariuszy | Buduje tożsamość, scenariusze, aspiracje |

Następnie:
- **Kompilator** zbiera wnioski w brief JSON
- **Rewriter** przepisuje tekst na podstawie briefa

## Stack

- **Frontend:** Statyczna strona HTML (GitHub Pages)
- **Backend:** Make.com workflow
- **AI:** OpenAI GPT-4o (`chatgpt-4o-latest`)

## Status

🟢 **GOTOWY** - workflow działa end-to-end

```
Webhook → 5 Agentów → Kompilator → Rewriter → Response
```

Czas przetwarzania: ~2 minuty

## Demo

👉 [Otwórz Tech Seducer](https://martinmajsawicki.github.io/tech-seducer/)

## Struktura projektu

```
tech-seducer/
├── index.html              ← Frontend (GitHub Pages)
├── PRD.md                  ← Dokumentacja projektu
├── prompts/                ← Prompty dla agentów AI
│   ├── 01_language_vivifier.md
│   ├── 02_benefit_translator.md
│   ├── 03_boredom_detective.md
│   ├── 04_sensory_painter.md
│   ├── 05_scenario_architect.md
│   ├── 06_report_compiler.md
│   └── 07_rewriter.md
├── make_workflow/          ← Instrukcja budowy workflow
│   ├── INSTRUKCJA_MAKE.md
│   └── TechSeducer_Sequential.json
└── test_samples/           ← Przykładowe teksty do testów
```

## Przykład transformacji

**PRZED:**
```
Laptop XYZ charakteryzuje się procesorem Intel Core i7, pamięcią RAM 16GB
oraz dyskiem SSD 512GB. Bateria zapewnia do 15 godzin pracy. Waga wynosi 1.2 kg.
```

**PO:**
```
Otwierasz laptopa w kawiarni. Po 3 sekundach piszesz.

16GB RAM oznacza, że Chrome z 50 zakładkami nie zwolni Twojej prezentacji.
Bateria? 15 godzin - cały lot do Tokio i jeszcze zostanie na mail po wylądowaniu.

1.2 kg - podajesz jedną ręką, gdy kolega pyta "ile to waży?".

Ten moment, gdy zamykasz laptopa o 22:00, a bateria wciąż na 30%.
```

## Licencja

MIT
