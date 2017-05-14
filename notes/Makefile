PANDOC=pandoc
RM=rm

PFLAGS=-s -S
PFLAGSHTML=$(PFLAGS) --mathjax

MDFILES=$(shell find . -name *.md)
HTMLFILES=$(subst .md,.html,$(MDFILES))

all: html

html: $(HTMLFILES)

%.html: %.md
	$(PANDOC) -o $@ $< $(PFLAGSHTML)


clean:
	$(RM) $(HTMLFILES)
