# CLAUDE.md

Průvodce pro Claude Code při práci na tomto projektu.

## O projektu
**Impariamo l'italiano** – interaktivní webová aplikace pro výuku italštiny pro české uživatele.
Uživatel si vybere pokročilost (A1–B2) ručně nebo krátkým vstupním testem a dostane obsah odpovídající úrovni.

## Architektura
- Celá aplikace je v jediném souboru **`index.html`** (HTML + CSS + vanilla JS), bez build kroku a bez závislostí.
  Jediný externí zdroj jsou Google Fonts (Playfair Display, Nunito) – aplikace funguje i bez nich (fallback fonty).
- Hostuje se jako statická stránka (GitHub Pages z větve `main`, kořen repozitáře).
- Stav (úroveň, XP, série dnů, naučená slovíčka, dokončené lekce) se ukládá do `localStorage` pod klíčem `italiano-v1`.
- Výslovnost: Web Speech API (`speechSynthesis`, `it-IT`).

## Struktura `index.html`
Skript je rozdělen komentáři `/* ====== */`:
1. **DATA** – `LEVELS`, `WORDS`, `PHRASES`, `GRAMMAR`, `PLACEMENT` (indexované klíčem úrovně `A1|A2|B1|B2`).
2. **STATE** – objekt `S`, `save()`, `addXP()` (řeší i sérii dnů).
3. **HELPERS** – `esc()` (vždy escapovat vložený text!), `shuffle`, `norm`, `speak`.
4. **SHELL** – hlavička, navigace, `go(view)`.
5. **VIEWS** – `VIEWS.home|cards|quiz|write|grammar|phrases`.
6. **EVENTS** – jediný delegovaný `click` listener přes atributy `data-act`.

## Konvence
- UI texty **česky**, italský obsah správně včetně diakritiky/akcentů.
- Nový obsah přidávat jen do datových struktur; slovíčko = `[italsky, česky, příklad]`.
- ID slovíčka se odvozuje z `úroveň-index` – **nepřeřazovat ani nemazat** existující slova (rozbilo by uložený pokrok); nová přidávat na konec.
- Nová akce = nový `data-act` + `case` v click handleru. Žádné inline `onclick`.
- Styl: italská paleta (zelená `#008c45`, bílá/krémová, červená `#cd212a`, terakota, zlatá), serifový nadpis, trikolórní pruh, karta slovíčka ve tvaru oblouku. Barvy jsou v CSS proměnných `:root`.
- Zachovat přístupnost (aria atributy, klávesnice, kontrast) a responzivitu (mobil první).

## Spuštění a test
Stačí otevřít `index.html` v prohlížeči (nebo `python -m http.server`). Kontrola syntaxe skriptu: vyjmout `<script>` do souboru a spustit `node --check`.

## Nápady do budoucna
- Více slovíček a lekcí, úroveň C1, tematické kategorie slovíček.
- Spaced repetition (Leitner) místo jednoduchého „znám / neznám“.
- Cvičení poslechu a doplňování vět, časování sloves.
- Export/import pokroku, PWA (offline), tmavý režim.
- Rozdělení dat do samostatných JSON/JS souborů, jakmile obsah naroste.
