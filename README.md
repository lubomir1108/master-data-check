# Master Data Check

A single-file browser tool that validates material master / product catalogue
data **before** it is imported into SAP. No installation, no server, no upload —
the spreadsheet is parsed in the browser and never leaves the machine.

**Live: https://lubomir1108.github.io/master-data-check/**

Interface and findings log available in **English, German and Slovak**.

---

## What it checks

| Check | Why it matters |
|---|---|
| Mandatory fields | Missing MATNR, MAKTX, MEINS or MTART blocks the import |
| Duplicate keys | Same material number posted twice |
| Near-identical descriptions | `Remenica 120 mm` vs `Remenica 120mm` — likely duplicate master records |
| Text length over 40 characters | SAP silently truncates MAKTX; the text is lost without warning |
| Unit of measure code list | `ks` / `KS` / `kus` / `pcs` all normalised to `ST` |
| Material type code list | ROH, HALB, FERT, HAWA, DIEN, VERP, HIBE, NLAG, UNBW, ERSA |
| EAN-13 check digit | Full GS1 checksum, plus length and embedded spaces |
| Net weight above gross weight | Classic transcription error no generic Excel tool catches |
| Numbers stored as text | `"0,02 kg"` converted to a number |
| Invalid VAT rates | Checked against the configured rate list |

Findings are split into **critical** (the row will not import) and
**warnings** (the tool corrected it automatically).

## Output

- **Clean import file** — only rows that pass, with normalised values
- **Findings log** — row, field, what was found, original value, action taken

Both as `.xlsx`.

## Column recognition

Reads SAP technical field names (`MATNR`, `MAKTX`, `MEINS`, `MTART`, `MATKL`,
`EAN11`, `BRGEW`, `NTGEW`, `GEWEI`, `WERKS`, `LIFNR`) — these are identical in
every SAP language — as well as descriptive headers in English, German and
Slovak (`Material Description`, `Materialkurztext`, `Názov`, …).

## Privacy

Everything runs client-side. Disconnect the network and the tool still works.
This matters when handling someone else's supplier data: nothing is transmitted,
so there is no processor relationship to arrange.

## Technical notes

No dependencies, no build step, no framework. One HTML file, ~43 kB.

`.xlsx` reading and writing are implemented from scratch: XLSX is a ZIP of XML,
unpacked with the browser's native `DecompressionStream("deflate-raw")` and
written back as a STORE-method ZIP with inline strings.

## Limitations

Format and consistency only. It cannot see open purchase orders, stock or
movements — that requires the SAP system itself. It is meant to do the
mechanical part so that a specialist reviews decisions, not rows.

Code lists are set to SAP standard values. Every system is customised, so they
may need adjusting.

---

## Slovensky

Nástroj na kontrolu kvality kmeňových dát pred importom do SAP. Jeden HTML
súbor, otvorí sa dvojklikom v prehliadači, nič sa neinštaluje a **dáta
z počítača neodchádzajú** — spracovanie prebieha celé lokálne.

Kontroluje povinné polia, duplicitné kľúče, takmer zhodné názvy, dĺžku MAKTX
nad limit SAP (40 znakov), číselníky MEINS a MTART, kontrolnú číslicu EAN-13
podľa GS1, čistú hmotnosť vyššiu než hrubú, čísla v textovom formáte
a neplatné sadzby DPH.

Vráti vyčistený import-ready súbor a protokol nálezov, oboje v `.xlsx`.
Rozhranie aj protokol sú v slovenčine, angličtine a nemčine.
