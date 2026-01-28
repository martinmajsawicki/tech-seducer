# Tech Seducer

**Strona demonstracyjna do prezentacji transformacji tekstów technicznych w treści perswazyjne.**

## Po co to jest?

Narzędzie do pokazywania efektu "przed i po" - wklejasz suchy tekst techniczny (instrukcję, specyfikację, opis produktu), a po chwili widzisz obok angażującą, perswazyjną wersję.

Idealne do:
- Demo na AI Day / prezentacji firmowej
- Pokazania możliwości AI w copywritingu
- Szkolenia z pisania tekstów marketingowych

**Frontend:** Strona na GitHub Pages
**Backend:** Workflow w Make.com (7 agentów AI)

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

- **Frontend:** HTML + GitHub Pages
- **Backend:** Make.com workflow
- **AI:** OpenAI GPT-4o

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

## Instalacja

### 1. GitHub Pages (Frontend)

1. Sforkuj lub sklonuj repozytorium
2. Wejdź w **Settings** → **Pages**
3. W sekcji "Source" wybierz:
   - Branch: `main`
   - Folder: `/ (root)`
4. Kliknij **Save**
5. Po chwili strona będzie dostępna pod: `https://[twój-username].github.io/tech-seducer/`

### 2. Make.com (Backend)

1. Zaloguj się do [Make.com](https://make.com)
2. Utwórz nowy scenariusz
3. Kliknij **...** (menu) → **Import Blueprint**
4. Wgraj plik `make_workflow/TechSeducer_Sequential.json`
5. Skonfiguruj połączenie OpenAI:
   - Kliknij na pierwszy moduł OpenAI
   - Dodaj Connection → wklej swój API key
   - Użyj tego samego połączenia dla wszystkich modułów
6. **Uzupełnij prompty** - blueprint zawiera skrócone wersje, zamień na pełne z folderu `prompts/`
7. Kliknij **Run once** aby przetestować
8. Skopiuj URL webhooka z pierwszego modułu

### 3. Połączenie Frontend + Backend

1. Otwórz stronę GitHub Pages
2. Wklej URL webhooka w pole na górze
3. Wpisz tekst techniczny lub użyj przykładu
4. Kliknij **Transformuj**

## Użycie

1. **Wklej tekst** - instrukcję, specyfikację, opis produktu
2. **Kliknij Transformuj** - poczekaj ~2 minuty
3. **Gotowe** - skopiuj przepisany tekst

### Przykładowe teksty do testów

Strona zawiera 3 wbudowane przykłady:
- 🛗 Instrukcja windy
- ☕ Młynek do kawy
- 💻 Specyfikacja laptopa

## Konfiguracja Make.com

| Parametr | Wartość |
|----------|---------|
| Model | `chatgpt-4o-latest` |
| Temperature (Agenci 1-5) | `0.8` |
| Temperature (Kompilator) | `0.3` |
| Max tokens (Agenci) | `2048` |
| Max tokens (Kompilator) | `3000` |
| Max tokens (Rewriter) | `4000` |

## Licencja

MIT
