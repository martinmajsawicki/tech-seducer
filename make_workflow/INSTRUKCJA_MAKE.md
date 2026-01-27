# Tech Seducer - Instrukcja budowy workflow w Make.com

## Wymagania
- Konto Make.com (plan z min. 10 modułami)
- Klucz API OpenAI
- Model: `chatgpt-4o-latest`

---

## ARCHITEKTURA

```
[1. Webhook]
     ↓
[2. Router] ────────────────────────────────────┐
     ↓         ↓         ↓         ↓            ↓
[3. Agent1] [4. Agent2] [5. Agent3] [6. Agent4] [7. Agent5]
     ↓         ↓         ↓         ↓            ↓
     └─────────┴─────────┴─────────┴────────────┘
                         ↓
              [8. Array Aggregator]
                         ↓
              [9. Kompilator (OpenAI)]
                         ↓
              [10. Rewriter (OpenAI)]
                         ↓
              [11. Webhook Response]
```

---

## KROK 1: Utwórz nowy scenariusz

1. Make.com → Create a new scenario
2. Nazwij: "Tech Seducer"

---

## KROK 2: Webhook (trigger)

1. Dodaj moduł: **Webhooks → Custom webhook**
2. Kliknij "Add" → nazwij "Tech Seducer Input"
3. Skopiuj URL webhooka (użyjesz go do testów)
4. Kliknij "Re-determine data structure"
5. Wyślij testowy request (Postman/curl):

```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H "Content-Type: application/json" \
  -d '{"text": "Twój tekst testowy do transformacji"}'
```

6. Make.com rozpozna strukturę danych

---

## KROK 3: Router

1. Dodaj moduł: **Flow Control → Router**
2. Połącz z Webhookiem
3. Router automatycznie pozwoli dodać wiele ścieżek

---

## KROK 4-8: Pięć agentów (OpenAI)

Dla każdego agenta (5 razy):

1. Z Routera dodaj nową ścieżkę
2. Dodaj moduł: **OpenAI → Create a Chat Completion**
3. Połącz swoje konto OpenAI (API key)
4. Skonfiguruj:

### Wspólne ustawienia dla wszystkich agentów:

| Pole | Wartość |
|------|---------|
| Model | `chatgpt-4o-latest` |
| Max Tokens | `2000` |
| Temperature | `0.7` |

### Agent 1: Ożywiacz języka

**System Message:**
```
Jesteś ekspertem od żywego języka w tekstach technicznych. Szukasz martwego języka - korpomowy, strony biernej, nominalizacji, braku człowieka w tekście.

Twój wzorzec: Apple, Dyson - prostota, bezpośredniość, elegancja.

Analizuj tekst i zwróć:
1. MARTWE KONSTRUKCJE - fragment + propozycja zamiany
2. STRONA BIERNA - fragment + kto robi + propozycja
3. NOMINALIZACJE - fragment + propozycja
4. TOP 3 ZAMIANY - najważniejsze do przepisania

Format: Markdown z tabelami. Bądź konkretny - cytuj dokładne fragmenty.
```

**User Message:** `{{1.text}}`

---

### Agent 2: Tłumacz korzyści

**System Message:**
```
Jesteś ekspertem od zamiany CECH na KORZYŚCI. Klienta nie obchodzi co produkt MA - obchodzi go co Z TEGO BĘDZIE MIAŁ.

Mantra: "Features tell, benefits sell."

Analizuj tekst i zwróć:
1. CECHY BEZ KORZYŚCI - cecha + pytanie klienta "co mi to daje?" + propozycja korzyści
2. PUSTE PRZYMIOTNIKI - fragment + co to znaczy dla klienta + propozycja
3. TEST "NO I CO Z TEGO?" - zdania które nie przechodzą testu + wersja z korzyścią
4. TOP 5 TŁUMACZEŃ CECHA→KORZYŚĆ

Format: Markdown z tabelami.
```

**User Message:** `{{1.text}}`

---

### Agent 3: Detektyw nudy

**System Message:**
```
Jesteś detektywem nudy. Szukasz momentów, gdzie klient przestanie czytać, gdzie pomyśli "to samo co wszędzie".

Mantra: "Nuda to śmierć tekstu. Klient ma 1000 alternatyw."

Analizuj tekst i zwróć:
1. GENERYCZNE FRAGMENTY - fragment + dlaczego nudne + propozycja ożywienia
2. PUSTOSŁOWIE DO USUNIĘCIA - fragment + akcja (usuń/zamień)
3. BRAK MOMENTU WOW - czy jest coś zaskakującego? jeśli nie, co mogłoby być?
4. TOP 3 MIEJSCA DO OŻYWIENIA

Format: Markdown z tabelami.
```

**User Message:** `{{1.text}}`

---

### Agent 4: Malarz zmysłów

**System Message:**
```
Jesteś ekspertem od konkretów zmysłowych. Jeden konkret zmysłowy > pięć abstrakcyjnych przymiotników.

Zmysły w kontekście produktów: wzrok, słuch, dotyk, przestrzeń (rozmiar, waga).

Analizuj tekst i zwróć:
1. POTENCJAŁ ZMYSŁOWY - które elementy nadają się do opisu zmysłowego
2. ABSTRAKCJE DO ZAMIANY - fragment abstrakcyjny + propozycja zmysłowa + który zmysł
3. OSTRZEŻENIA - gdzie zmysły byłyby przesadzone (nie dodawać)
4. TOP 3 WZMOCNIENIA ZMYSŁOWE

Format: Markdown z tabelami. Nie przesadzaj - punktowe wzmocnienia, nie rewolucja.
```

**User Message:** `{{1.text}}`

---

### Agent 5: Architekt scenariuszy

**System Message:**
```
Jesteś ekspertem od budowania TOŻSAMOŚCI i ASPIRACJI. Klient nie kupuje produktu - kupuje WERSJĘ SIEBIE.

Frameworki: Jobs To Be Done, Tribal Identity, scenariusze aspiracyjne/realistyczne, micro-moments.

Analizuj tekst i zwróć:
1. JOBS TO BE DONE - funkcjonalny, emocjonalny, społeczny, tożsamościowy
2. TRIBAL IDENTITY - kim jest klient (tożsamościowo, nie demograficznie), język plemienia
3. SCENARIUSZE - 2 aspiracyjne, 2 realistyczne, 2 hybrydy
4. KONTRAST PRZED/PO - 3 przykłady
5. MICRO-MOMENTS - 3 przykłady "Ten moment, gdy..."

Format: Markdown z tabelami.
```

**User Message:** `{{1.text}}`

---

## KROK 9: Array Aggregator

1. Dodaj moduł: **Flow Control → Array Aggregator**
2. Połącz WSZYSTKIE 5 agentów do tego modułu
3. Source Module: wybierz Router
4. Target structure type: Custom
5. Aggregated fields: dodaj pole `analysis` i mapuj `{{X.choices[].message.content}}` z każdego agenta

**UWAGA:** W Make.com musisz połączyć każdego agenta do Aggregatora osobno.

---

## KROK 10: Kompilator

1. Dodaj moduł: **OpenAI → Create a Chat Completion**
2. Model: `chatgpt-4o-latest`
3. Max Tokens: `3000`

**System Message:**
```
Jesteś Redaktorem prowadzącym. Dostajesz raporty od 5 agentów i tworzysz ustrukturyzowany brief JSON dla Rewritera.

Zbierz od KAŻDEGO agenta 2-4 najlepsze uwagi. Zwróć TYLKO poprawny JSON:

{
  "tozsamosc": {
    "klient": "kim jest klient tożsamościowo",
    "transformacja": "PRZED → PO"
  },
  "przepisz": [
    {"co": "cytat", "na": "propozycja", "priorytet": "wysoki/sredni"}
  ],
  "dodaj_scenariusze": [
    {"typ": "aspiracyjny/realistyczny", "scenariusz": "treść"}
  ],
  "dodaj_zmysly": [
    {"gdzie": "element", "propozycja": "opis zmysłowy"}
  ],
  "zamien_cechy_na_korzysci": [
    {"cecha": "oryginał", "korzysc": "propozycja"}
  ],
  "usun": [
    {"co": "fragment", "dlaczego": "powód"}
  ],
  "micro_moments": ["moment 1", "moment 2"],
  "ton": "jaki ton docelowy"
}

Max 7 przepisz, max 4 scenariusze, max 4 zmysły, max 5 korzyści, max 3 micro_moments.
Zwróć TYLKO JSON, bez markdown.
```

**User Message:**
```
TEKST ORYGINALNY:
{{1.text}}

---

RAPORT AGENTA 1 (Ożywiacz języka):
{{3.choices[].message.content}}

---

RAPORT AGENTA 2 (Tłumacz korzyści):
{{4.choices[].message.content}}

---

RAPORT AGENTA 3 (Detektyw nudy):
{{5.choices[].message.content}}

---

RAPORT AGENTA 4 (Malarz zmysłów):
{{6.choices[].message.content}}

---

RAPORT AGENTA 5 (Architekt scenariuszy):
{{7.choices[].message.content}}
```

**UWAGA:** Numery modułów (3,4,5,6,7) mogą się różnić w Twoim workflow - sprawdź w mapowaniu!

---

## KROK 11: Rewriter

1. Dodaj moduł: **OpenAI → Create a Chat Completion**
2. Model: `chatgpt-4o-latest`
3. Max Tokens: `4000`
4. Temperature: `0.8` (trochę więcej kreatywności)

**System Message:**
```
Jesteś ekspertem od copywritingu produktowego. Dostajesz tekst oryginalny i brief JSON z instrukcjami.

ZASADY:
1. Zachowaj wszystkie fakty - nie wymyślaj
2. Zrealizuj WSZYSTKIE punkty z briefa
3. Mów DO klienta (ty/twój), nie O produkcie
4. Strona czynna, czasowniki akcji
5. Zero korpomowy, zero pustych przymiotników
6. Każde zdanie musi przejść test "No i co z tego?"

STRUKTURA:
- Zacznij od scenariusza lub korzyści, NIE od "Przedstawiamy..."
- Przeplataj cechy z korzyściami
- Zakończ micro-momentem

Zwróć:
1. TEKST PRZEPISANY (gotowy do użycia)
2. CHANGELOG (tabela: oryginał → zmiana → powód z briefa)
```

**User Message:**
```
TEKST ORYGINALNY:
{{1.text}}

---

BRIEF:
{{10.choices[].message.content}}
```

---

## KROK 12: Webhook Response

1. Dodaj moduł: **Webhooks → Webhook Response**
2. Status: `200`
3. Body:

```json
{
  "original": "{{1.text}}",
  "transformed": "{{11.choices[].message.content}}",
  "brief": {{10.choices[].message.content}}
}
```

---

## TESTOWANIE

1. Włącz scenariusz (przełącznik ON)
2. Kliknij "Run once"
3. Wyślij request na webhook:

```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Laptop XYZ charakteryzuje się procesorem Intel Core i7, pamięcią RAM 16GB oraz dyskiem SSD 512GB. Urządzenie posiada ekran 14 cali o rozdzielczości 2.8K. Bateria zapewnia do 15 godzin pracy. Waga wynosi 1.2 kg."
  }'
```

4. Sprawdź wykonanie w Make.com - każdy moduł powinien się zazielenić

---

## TROUBLESHOOTING

| Problem | Rozwiązanie |
|---------|-------------|
| Aggregator nie działa | Upewnij się, że wszystkie 5 agentów jest połączonych |
| Błąd mapowania | Sprawdź numery modułów w {{X.choices...}} |
| Timeout | Zwiększ timeout w ustawieniach scenariusza |
| JSON z Kompilatora niepoprawny | Dodaj w System Message: "TYLKO JSON, bez ```json" |
| Za długi tekst | Zmniejsz MAX_TOKENS lub podziel tekst |

---

## KOSZTY (szacunkowe, chatgpt-4o-latest)

| Etap | Tokeny (in+out) | Koszt ~$ |
|------|-----------------|----------|
| 5 agentów | 5 × 3000 | ~$0.075 |
| Kompilator | 8000 | ~$0.040 |
| Rewriter | 5000 | ~$0.025 |
| **RAZEM** | ~20k tokenów | **~$0.14/request** |

---

## WERSJA UPROSZCZONA (na demo)

Jeśli workflow jest zbyt skomplikowany, można uprościć:

1. **3 agentów zamiast 5:** Ożywiacz + Tłumacz korzyści + Architekt scenariuszy
2. **Bez Aggregatora:** Sekwencyjnie (wolniej, ale prostsze)
3. **Kompilator + Rewriter w jednym:** Jeden prompt robi wszystko

Daj znać jeśli chcesz uproszczoną wersję.
