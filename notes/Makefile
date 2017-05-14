PANDOC=pandoc

PFLAGS=-s -S

MDFILES=$(shell find . -name *.md)
HTMLFILES=$(subst .md,.html,$(MDFILES))

all: html

html: $(HTMLFILES)

%.html: %.md
	$(PANDOC) -o $@ $< $(PFLAGS)
