# CLAUDE.md

Průvodce pro Claude Code při práci na tomto projektu.

## O projektu
**Impariamo l'italiano** – interaktivní webová aplikace pro výuku italštiny pro české uživatele.
Uživatel si vybere pokročilost (A1–B2) ručně nebo vstupním testem (12 otázek) a dostane obsah odpovídající úrovni.

## Architektura
- Aplikace je v jediném souboru **`index.html`** (HTML + CSS + vanilla JS), bez build kroku a bez závislostí.
- Fonty (Playfair Display, Nunito – SIL OFL) jsou ve složce **`fonts/`** a načítají se přes `@font-face`; žádné externí požadavky.
- Hostuje se jako statická stránka (GitHub Pages z větve `main`, kořen repozitáře).
- Stav se ukládá do `localStorage` pod klíčem `italiano-v1`. Při načtení prochází funkcí `sanitize()` – poškozená data nikdy nesmí aplikaci shodit.
- Výslovnost: Web Speech API (`it-IT`). Pokud prohlížeč nemá italský hlas, tlačítka 🔊 se skryjí (`body.nosound`).

## Struktura `index.html`
Skript je rozdělen komentáři `/* ====== */`:
1. **DATA** – `LEVELS`, `WORDS`, `PHRASES`, `GRAMMAR`, `PLACEMENT` (klíče úrovní `A1|A2|B1|B2`).
2. **STATE** – objekt `S`, `sanitize()`, `touch()` (série dnů v **místním** čase), `setMastery()/bump()` (XP za nově naučené slovo), `lessonDone()`.
3. **HELPERS** – `esc()` (vždy escapovat vložený text!), `IT()` (obalí italský text `lang="it"`), `shuffle`, `norm`, `wordsOf`, `mcq`, `pick`, `speak`.
4. **SHELL** – hlavička, navigace, `go(view)`.
5. **VIEWS** – `VIEWS.home|cards|quiz|write|grammar|phrases`.
6. **EVENTS** – jediný delegovaný `click` listener přes `data-act`, dále `keydown` a `submit`.

## Datové konvence (důležité)
- Slovíčko: `[italsky, česky, příklad, člen?]`. Člen (`il, lo, la, l', le…`) jen u podstatných jmen. ID slovíčka = `úroveň-index` → **nepřeřazovat ani nemazat** existující slova (rozbilo by uložený pokrok), nová přidávat na konec.
- Víc významů v české části oddělit čárkou. Dvě slova se sdíleným významem se v kvízu nikdy nenabízejí jako možnosti a ve cvičení psaní se druhé z nich uzná jako „také správně“.
- Kvízová otázka (gramatika): `[otázka, SPRÁVNÁ, špatná1, špatná2]` – správná je vždy první, pořadí se míchá při zobrazení. Vždy zkontrolovat, že **žádná špatná odpověď není také správně** (např. přítomný čas × budoucí v otázce o budoucnosti).
- Lekce: 4 otázky; lekce je splněná při max. jedné chybě. Klíč dokončení lekce je `úroveň:index`.
- Vstupní test: po 3 otázkách na úroveň; doporučená úroveň je první, kde jsou správně méně než 2 ze 3.

## Pravidla chování
- XP jen za nově naučené slovo (5, jen jednou za slovo) a první splnění lekce (5 × správné). Opakování XP nedává.
- Slovo je „naučené“, když má `mastery >= 2` (tlačítko „Znám“ nebo dvě správné odpovědi v kvízu/psaní).
- UI texty **česky**, italský obsah správně včetně akcentů. Italský text v DOM obalovat `IT()`.
- Žádné inline `onclick`; nová akce = nový `data-act` + `case` v click handleru.
- Styl: italská paleta (zelená `#008c45`, krémová, červená `#cd212a`, terakota, zlatá) v CSS proměnných `:root`, karta slovíčka ve tvaru oblouku, trikolórní pruh.
- Přístupnost: ovladatelné klávesnicí (tlačítka, ne `span`), po akcích přesouvat fokus, viditelný `:focus-visible`. Mobil první (navigace je na úzkých displejích mřížka 3×2).

## Spuštění a test
Aplikaci stačí otevřít v prohlížeči, ale **fonty a plné chování ověřovat přes http** (např. jednoduchý lokální server), protože relativní soubory přes `file://`/snímek nemusí fungovat. Na stroji nemusí být Node ani Python; lze použít PowerShell `HttpListener`.
Po každé změně dat ověřit v konzoli prohlížeče: unikátnost slov, počet otázek (4), 3 různé možnosti v každé otázce, a projít všechny lekce a režimy.

## Nápady do budoucna
- Úroveň C1, tematické kategorie slovíček, další lekce (trapassato, imperativ, předložky místa).
- Spaced repetition (Leitner) místo jednoduché váhy `mastery`.
- Poslechová cvičení a doplňování vět, časování sloves.
- Export/import pokroku, PWA (offline), tmavý režim.
- Rozdělení dat do samostatných souborů, jakmile obsah naroste.
- Obsah projít rodilým mluvčím.
