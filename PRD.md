# PRD: Tech Seducer

> Workflow do transformacji tekstów technicznych w treści perswazyjne i uwodzicielskie

**Status:** GOTOWY - workflow działa end-to-end
**Data utworzenia:** 2026-01-27
**Ostatnia aktualizacja:** 2026-01-28

---

## 1. Problem

Teksty techniczne (specyfikacje produktów, instrukcje, opisy funkcji) w firmach consumer electronics są:
- Suche i bezosobowe - brzmią jak napisał je robot
- Pełne żargonu i korpomowy - "innowacyjne rozwiązanie", "wysoka jakość"
- Skupione na cechach (features), nie korzyściach (benefits)
- Nie budują emocji, aspiracji ani pożądania
- Nie mówią klientowi KIM BĘDZIE, gdy kupi produkt
- Są przewidywalne i generyczne - takie jak u konkurencji

**Zmarnowana szansa:** Każdy opis produktu to okazja do "uwodzenia" klienta.

---

## 2. Rozwiązanie

Multi-agentowy workflow w Make.com, gdzie 5 AI-agentów równolegle analizuje tekst techniczny z różnych perspektyw, a następnie:
1. Kompilator zbiera wnioski w ustrukturyzowany brief (JSON)
2. Rewriter przepisuje tekst na podstawie briefa

### Architektura

```
TEKST TECHNICZNY (input)
         |
    +----+----+--------+--------+--------+
    |         |        |        |        |
    v         v        v        v        v
[Agent 1] [Agent 2] [Agent 3] [Agent 4] [Agent 5]
Ożywiacz  Tłumacz   Detektyw  Malarz    Architekt
języka    korzyści  nudy      zmysłów   scenariuszy
    |         |        |        |        |
    +----+----+--------+--------+--------+
         |
         v
   [Agent 6: KOMPILATOR] --> BRIEF (JSON)
         |
         v
   [Agent 7: REWRITER] --> TEKST KOŃCOWY
```

---

## 3. Agenci - przegląd

| # | Agent | Plik | Szuka | Kluczowe pytanie |
|---|-------|------|-------|------------------|
| 1 | Ożywiacz języka | `01_language_vivifier.md` | Martwy język, korpomowa, strona bierna, nominalizacje | "Gdzie tekst brzmi jak robot?" |
| 2 | Tłumacz korzyści | `02_benefit_translator.md` | Cechy bez korzyści, puste przymiotniki | "Co z tego ma KLIENT?" |
| 3 | Detektyw nudy | `03_boredom_detective.md` | Generyczność, przewidywalność, pustosłowie | "Gdzie klient przestanie czytać?" |
| 4 | Malarz zmysłów | `04_sensory_painter.md` | Abstrakcje bez konkretu zmysłowego | "Jak to POCZUJĘ?" |
| 5 | Architekt scenariuszy | `05_scenario_architect.md` | Brak tożsamości, scenariuszy, aspiracji | "Kim BĘDĘ z tym produktem?" |
| 6 | Kompilator | `06_report_compiler.md` | - | Zbiera wnioski → JSON brief |
| 7 | Rewriter | `07_rewriter.md` | - | Przepisuje tekst wg briefa |

---

## 4. Szczegóły agentów

### Agent 1: Ożywiacz języka
**Bazuje na:** `liveliness.md` z ARCHETYPE_STUDIO
**Szuka:**
- Martwych konstrukcji ("charakteryzuje się", "został wyposażony")
- Strony biernej (kto robi - nie wiadomo)
- Nominalizacji (rzeczowniki zamiast czasowników)
- Braku człowieka w tekście
- Braku dialogu z klientem

### Agent 2: Tłumacz korzyści
**Bazuje na:** `sponsored.md` z ARCHETYPE_STUDIO
**Szuka:**
- Cech technicznych bez kontekstu ("16GB RAM" → co mi to daje?)
- Pustych superlatywów ("wysokiej jakości" → jak to odczuję?)
- Opisów bez doświadczenia ("intuicyjny" → co to znaczy w praktyce?)
**Test:** "No i co z tego dla MNIE?"

### Agent 3: Detektyw nudy
**Bazuje na:** `boring.md` z ARCHETYPE_STUDIO
**Szuka:**
- Generyczności (mógłby być o każdym produkcie)
- Przewidywalności (ta sama struktura co zawsze)
- Pustosłowia (zdania do usunięcia bez straty)
- Braku momentu WOW (nic zaskakującego)

### Agent 4: Malarz zmysłów
**Bazuje na:** `sensory.md` z ARCHETYPE_STUDIO
**Szuka:**
- Abstrakcji do zamiany na konkret ("cichy" → "nie usłyszysz z drugiego pokoju")
- Zmysłów: wzrok, słuch, dotyk, przestrzeń
**Uwaga:** Nie forsuje zmysłów wszędzie - wie gdzie pasują

### Agent 5: Architekt scenariuszy
**Nowy agent** - inspirowany JTBD i tribal marketing
**Szuka:**
- Jobs To Be Done - do czego klient "zatrudnia" produkt
- Tribal Identity - do jakiego "plemienia" należy klient
- Scenariuszy: aspiracyjnych, realistycznych, hybryd
- Kontrastu PRZED/PO
- Micro-moments - konkretnych chwil użycia

### Agent 6: Kompilator
**Bazuje na:** `editor_synthesis.md` z ARCHETYPE_STUDIO
**Robi:** Zbiera najlepsze uwagi od wszystkich agentów i tworzy JSON brief dla Rewritera

### Agent 7: Rewriter
**Nowy agent**
**Robi:** Przepisuje tekst oryginalny zgodnie z briefem, zachowując fakty ale zmieniając styl

---

## 5. Workflow Make.com

### 5.1 Struktura (sekwencyjna)

**Uwaga:** Workflow działa sekwencyjnie (nie równolegle) ze względu na ograniczenia Make.com w agregowaniu wyników z równoległych ścieżek.

```
[WEBHOOK] Tekst wejściowy
     |
     v
[OpenAI] Agent 1: Ożywiacz języka
     |
     v
[OpenAI] Agent 2: Tłumacz korzyści
     |
     v
[OpenAI] Agent 3: Detektyw nudy
     |
     v
[OpenAI] Agent 4: Malarz zmysłów
     |
     v
[OpenAI] Agent 5: Architekt scenariuszy
     |
     v
[OpenAI] Agent 6: Kompilator → JSON brief
     |
     v
[OpenAI] Agent 7: Rewriter → tekst końcowy
     |
     v
[WEBHOOK RESPONSE] Tekst przepisany
```

### 5.2 Moduły Make.com

| Moduł | Typ | Opis |
|-------|-----|------|
| Trigger | Custom Webhook | Przyjmuje JSON `{"text": "..."}` |
| Agent 1-5 | OpenAI (Chat Completion) | Sekwencyjnie, każdy z pełnym promptem |
| Agent 6 | OpenAI (Chat Completion) | Kompilator → JSON brief |
| Agent 7 | OpenAI (Chat Completion) | Rewriter → tekst końcowy |
| Output | Webhook Response | Zwraca plain text do frontendu |

### 5.3 Konfiguracja API

**Rekomendacja:** Claude (Anthropic API) lub GPT-4

| Agent | Model | Max tokens |
|-------|-------|------------|
| 1-5 | claude-3-5-sonnet / gpt-4 | 2000 |
| 6 | claude-3-5-sonnet / gpt-4 | 3000 |
| 7 | claude-3-5-sonnet / gpt-4 | 4000 |

---

## 6. Pliki promptów

```
/tech-seducer/
├── PRD.md (ten plik)
└── prompts/
    ├── 01_language_vivifier.md   ← Ożywiacz języka
    ├── 02_benefit_translator.md  ← Tłumacz korzyści
    ├── 03_boredom_detective.md   ← Detektyw nudy
    ├── 04_sensory_painter.md     ← Malarz zmysłów
    ├── 05_scenario_architect.md  ← Architekt scenariuszy
    ├── 06_report_compiler.md     ← Kompilator (→ JSON)
    └── 07_rewriter.md            ← Rewriter (→ tekst końcowy)
```

---

## 7. Przykład transformacji

### INPUT (tekst techniczny):
```
Laptop XYZ Pro charakteryzuje się procesorem Intel Core i7 12. generacji,
pamięcią RAM 16GB oraz dyskiem SSD 512GB. Urządzenie posiada ekran 14"
o rozdzielczości 2.8K. Bateria zapewnia do 15 godzin pracy.
Waga wynosi 1.2 kg. Laptop został wyposażony w klawiaturę podświetlaną.
```

### OUTPUT (tekst uwodzicielski):
```
Otwierasz laptopa w kawiarni. Po 3 sekundach piszesz.

XYZ Pro to laptop dla tych, którzy pracują skąd chcą. 16GB RAM
oznacza, że Chrome z 50 zakładkami nie zwolni Twojej prezentacji.
Bateria? 15 godzin - cały lot do Tokio i jeszcze zostanie na mail
po wylądowaniu.

1.2 kg - podajesz jedną ręką, gdy kolega pyta "ile to waży?".
Ekran 2.8K - każdy piksel na zdjęciach, każdy szczegół w arkuszu.

Ten moment, gdy zamykasz laptopa o 22:00, a bateria wciąż na 30%.
Jutro robisz to samo. Bez ładowarki.
```

---

## 8. Kontekst użycia

**Dla kogo:** AI Day - demo dla pracowników firmy consumer electronics
**Cel demo:** Pokazać jak AI może transformować nudne teksty w angażujące
**Platforma:** Make.com
**Czas demo:** 20-30 minut (build + pokazanie działania)

---

## 9. Kryteria sukcesu

Tekst wyjściowy powinien:
- [x] Mówić językiem korzyści, nie cech
- [x] Zawierać scenariusze użycia (aspiracyjne + realistyczne)
- [x] Budować tożsamość ("to jest dla ludzi, którzy...")
- [x] Mieć konkrety zmysłowe (jak to wygląda, brzmi, waży)
- [x] Zawierać micro-moments ("ten moment, gdy...")
- [x] Być zrozumiały bez wiedzy technicznej
- [x] Zachować prawdziwość informacji technicznych
- [x] Być krótszy i mocniejszy niż oryginał

---

## 10. Następne kroki

1. [x] Napisać prompty dla wszystkich agentów
2. [x] Przygotować 2-3 przykładowe teksty do testów
3. [x] Zbudować workflow w Make.com
4. [x] Przetestować z różnymi kategoriami produktów (winda, młynek do kawy, laptop)
5. [ ] Przygotować prezentację na AI Day

---

## 11. Inspiracje / źródła

- Prompty z ARCHETYPE_STUDIO (`/prompts/pl/` i `/prompts/pl-ddtvn/`)
- Jobs To Be Done framework (Clayton Christensen)
- Tribal Marketing (Seth Godin)
- Apple copywriting style
- Dyson product descriptions

---

## 12. Stan implementacji (2026-01-28)

### Status: GOTOWY

Cały workflow działa end-to-end:
- ✅ **Frontend** - GitHub Pages: https://martinmajsawicki.github.io/tech-seducer/
- ✅ **Webhook** - przyjmuje JSON `{"text": "..."}`
- ✅ **7 Agentów OpenAI** - sekwencyjnie, pełne prompty
- ✅ **Kompilator** - generuje JSON brief
- ✅ **Rewriter** - przepisuje tekst (tylko tekst, bez komentarzy)
- ✅ **Webhook Response** - zwraca plain text do frontendu

### Konfiguracja Make.com:
- **Webhook URL:** `https://hook.eu2.make.com/4jhs5svi0q22xvtrl3rfo9gp9igwtnss`
- **Model:** `chatgpt-4o-latest` (wszystkie moduły)
- **Temperature:** 0.8 (agenci kreatywni), 0.3 (Kompilator)
- **Architektura:** Sekwencyjna (nie równoległa)
- **Czas przetwarzania:** ~2 minuty

### Struktura plików:
```
tech-seducer/
├── index.html              ← Frontend (GitHub Pages)
├── README.md               ← Opis projektu
├── PRD.md                  ← Ten dokument
├── prompts/                ← Prompty (7 plików)
├── make_workflow/
│   ├── INSTRUKCJA_MAKE.md  ← Instrukcja budowy
│   └── TechSeducer_Sequential.json ← Blueprint sekwencyjny
└── test_samples/           ← Przykładowe teksty
```

---

## 13. Log zmian

| Data | Zmiana |
|------|--------|
| 2026-01-27 | Utworzenie dokumentu |
| 2026-01-27 | Dodanie 5. agenta (Architekt scenariuszy) |
| 2026-01-27 | Napisanie wszystkich 7 promptów |
| 2026-01-27 | Aktualizacja architektury i workflow |
| 2026-01-27 | Frontend na GitHub Pages |
| 2026-01-27 | Workflow Make.com: Webhook → Router → 5 Agentów → Aggregator → Kompilator (działa) |
| 2026-01-28 | Przebudowa na architekturę sekwencyjną (ograniczenia Make.com z agregowaniem równoległych ścieżek) |
| 2026-01-28 | Dodanie Rewritera i Webhook Response |
| 2026-01-28 | Fix frontendu: response.text() zamiast response.json() |
| 2026-01-28 | Fix Rewritera: zwraca tylko tekst (bez changelog/checklist) |
| 2026-01-28 | **WORKFLOW GOTOWY** - przetestowany z windą, młynkiem do kawy, laptopem |
