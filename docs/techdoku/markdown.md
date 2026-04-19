# Markdown

## Textformatierung

| Stil | formatiert |
| --- | --- |
| bold | **text** |
| italics | *italics* |
| bold & italics | ***italics*** |
| duchgestrichen | ~~durchgestrichen~~ |

## Überschriften

| | |
| --- | --- |
| Überschrift 1 | # H1 |
| Überschrift 2 | ## H2 |
| Überschrift 3 | ### H3 |

## Listen

unordered durch vorangestelltes `*` oder `-` oder `+`

ordered durch vorangestelltes `1.` (Ziffer kann konstant bleiben)

**nested mit Einrückungen:**

```""
1. item1
1. item2
  1. subitem
1. item3
  * unordered subitem
```

## Code

Einzelne Zeilen / Wörter zwischen Backticks stellen

Mehrzeiligen Code zwischen "fences" mit stellen (am besten mit Sprache); z.B.

```bash
blah code
```

## Tabellen

Benötigen mindestens 3 Zeilen (Kopfzeile, Trennlinie mit mind. 3 x "-", Datenzeile(n))

| Kopfzeile A | Kopfzeile B |
| ----------- | ----------- |
| Zelle 1     | Zelle 2     |
| Zelle 3     | Zelle 4     |

Linksbündig, zentriert, rechtsbündig werden über ":" in der Trennlinie gesteuert:

| Kopfzeile A | Kopfzeile B | Kopfzeile C |
| :--- | :---: | ---: |
| Zelle 11 | Zelle 12 | Zelle 13 |
| Zelle 21 | Zelle 22 | Zelle 23 |

## Quotes

Blockquotes durch vorangestelltes `>`. Verschachtelte Quotes durch `>>` usw.

>Dies ist ein Quote.

aus.

>**Hinweis:** Text mit einem Label

## Admonisions mit GibHub "Alerts"

Werden nicht von allen Renderen unterstützt... (glow: nein, render-markdown.)

>[!NOTE]
> Dies ist eine Note.

aus.

>[!TIP]
> Dies ist ein Tip.

aus.

>[!IMPORTANT]
> Dies ist eine wichtige Info.

aus.

>[!CAUTION]
> Dies ist eine Warnung zur Vorsicht..

aus.

>[!WARNING]
> Dies ist eine Warnung.

## Links

Dies ist ein [Link mit Beschreibung](https://google.at)

Dies ist ein Link auf ein Bild: ![Bildbeschreibung](https://www.cibex.net/web/image/website/1/logo/cibex?unique=decf7d4)

## Sektionen

mit 3 Bindestrichen trennen....

---

aus.
