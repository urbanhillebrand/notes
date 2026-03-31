# make

## Bsp.: asciidoctor-toolchain

```make
AD=`which asciidoctor`
ADPDF=`which asciidoctor-pdf`
SRC=$(wildcard *.adoc)

all: html pdf

# html
html: $(SRC:.adoc=.html)
%.html: %.adoc
	$(AD) -o $@ $<

pdf: $(SRC:.adoc=.pdf)
%.pdf: %.adoc
	$(ADPDF) $<

clean:
	@rm -f *.html *.pdf
```

```make
### VARIABLES
ADOC=Demo-Handbuch.adoc
AD=/usr/local/bin/asciidoctor
FP=/opt/asciidoctor-fopub/bin/fopub
A2X=/usr/bin/a2x

usage:
        @echo "usage: make (all|html|xml|pdf|fopub|a2x|clean)"

all: html xml pdf fopub a2x

# html (asciidoctor)
html: $(ADOC:.adoc=-ad.html)
$(ADOC:.adoc=-ad.html): $(ADOC)
        $(AD) -d book -o $@ $<

# xml (asciidoctor)
xml: $(ADOC:.adoc=-ad.xml)
$(ADOC:.adoc=-ad.xml): $(ADOC)
        $(AD) -b docbook -a data-uri! -o $@ $<

# pdf (asciidoctor-pdf)
pdf: $(ADOC:.adoc=-ad.pdf)
$(ADOC:.adoc=-ad.pdf): $(ADOC)
        $(AD) -r asciidoctor-pdf -b pdf -d book -a data-uri! -o $@ $<

# pdf (fopub)
fopub fopub-pdf fp.pdf: $(ADOC:.adoc=-fp.pdf)
$(ADOC:.adoc=-fp.pdf): $(ADOC:.adoc=-ad.xml)
        cp $< $(patsubst %-ad.xml,%-fp.xml,$<)
        $(FP) $(patsubst %-ad.xml,%-fp.xml,$<)
        rm $(patsubst %-ad.xml,%-fp.xml,$<)

a2x a2x.pdf a2x-pdf: $(ADOC:.adoc=-a2x.pdf)
$(ADOC:.adoc=-a2x.pdf): $(ADOC)
        cp $< $(patsubst %.adoc,%-a2x.adoc,$<)
        -$(A2X) --verbose -f pdf -d book $(patsubst %.adoc,%-a2x.adoc,$<)
        rm $(patsubst %.adoc,%-a2x.adoc,$<)

.PHONY: clean
clean:
        -rm -f *-ad.html *-ad.xml *-ad.pdf *-fp.xml *-fp.pdf *-a2x.xml *-a2x.adoc *-ad.pdfmarks
```

## Bsp.: wildcards

```make
### VARIABLES
AD=/usr/bin/asciidoctor
FP=/opt/asciidoctor-fopub/bin/fopub
A2X=/usr/bin/a2x
SRC=$(wildcard *.adoc)

usage:
        @echo "usage: make (all|html|ad.pdf|ad.xml|fp.pdf|a2x.pdf|clean"

### RULES
#all: $(SRC:.adoc=.ad.html) $(SRC:.adoc=.ad.pdf) $(SRC:.adoc=.fp.pdf) $(SRC:.adoc=.a2x.pdf)
all: $(SRC:.adoc=.ad.html) $(SRC:.adoc=.fp.pdf) $(SRC:.adoc=.a2x.pdf)

# html (asciidoctor)
ad.html html: $(SRC:.adoc=.ad.html)
$(SRC:.adoc=.ad.html): $(SRC)
        $(AD) -d book -o $@ $<

# pdf (asciidoctor-pdf)
ad.pdf pdf: $(SRC:.adoc=.ad.pdf)
$(SRC:.adoc=.ad.pdf): $(SRC)
        $(AD) -r asciidoctor-pdf -b pdf -d book -a data-uri! -o $@ $<

# xml (asciidoctor)
ad.xml xml: $(SRC:.adoc=.ad.xml)
$(SRC:.adoc=.ad.xml): $(SRC)
        $(AD) -b docbook -d book -a data-uri! -o $@ $<

# pdf (fopub)
fp.pdf fopub: ad.xml $(SRC:.adoc=.fp.pdf)
$(SRC:.adoc=.fp.pdf): $(SRC:.adoc=.ad.xml)
        cp $< $(patsubst %.ad.xml,%.fp.xml,$<)
        $(FP) $(patsubst %.ad.xml,%.fp.xml,$<)
        rm $(patsubst %.ad.xml,%.fp.xml,$<)

# pdf (a2x)
a2x a2x.pdf: $(SRC:.adoc=.a2x.pdf)
$(SRC:.adoc=.a2x.pdf): $(SRC)
        cp $< $(patsubst %.adoc,%.a2x.adoc,$<)
        $(A2X) --verbose -f pdf -d book $(patsubst %.adoc,%.a2x.adoc,$<)
        rm $(patsubst %.adoc,%.a2x.adoc,$<)

clean:
        rm *.ad.xml *.ad.pdf *.a2x.adoc *.a2x.pdf *.fp.pdf *.pdfmarks *.ad.html
```
