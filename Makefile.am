###############################################################################
## Makefile for Icelandic--Russian
###############################################################################

LANG1=isl
LANG2=rus
PREFIX1=$(LANG1)-$(LANG2)
PREFIX2=$(LANG2)-$(LANG1)

BASENAME=apertium-$(PREFIX1)

TARGETS_COMMON = $(PREFIX1).autobil.bin $(PREFIX2).autobil.bin \
	$(PREFIX1).automorf.bin $(PREFIX1).autopgen.bin $(PREFIX1).autogen.bin $(PREFIX1).t1x.bin $(PREFIX1).t2x.bin $(PREFIX1).t3x.bin \
	$(PREFIX2).automorf.bin $(PREFIX2).autogen.bin $(PREFIX2).t1x.bin $(PREFIX2).t2x.bin $(PREFIX2).t3x.bin \
        $(PREFIX1).rlx.bin $(PREFIX2).rlx.bin \
        $(PREFIX1).autolex.bin $(PREFIX2).autolex.bin

@ap_include@

# Use this goal for creating .deps, otherwise make -j2 will give problems:
.deps/.d:
	if [ ! -d .deps ]; then $(MKDIR_P) .deps; fi
	touch $@

# Prevent the .d file from being removed automatically:
.PRECIOUS: .deps/.d

###############################################################################
## Icelandic→Russian monolingual transducers
###############################################################################

$(PREFIX1).automorf.bin: $(AP_SRC1)/$(LANG1).automorf.bin $(PREFIX1).autobil.bin .deps/.d
	lt-trim $(AP_SRC1)/$(LANG1).automorf.bin $(PREFIX1).autobil.bin $@

$(PREFIX1).autogen.bin: $(AP_SRC2)/$(LANG2).autogen.bin
	cp $< $@

###############################################################################
## Russian→Icelandic monolingual transducers
###############################################################################

$(PREFIX2).automorf.bin: $(AP_SRC2)/$(LANG2).automorf.bin $(PREFIX2).autobil.bin .deps/.d
	lt-trim $(AP_SRC2)/$(LANG2).automorf.bin $(PREFIX2).autobil.bin $@

$(PREFIX2).autogen.bin: $(AP_SRC1)/$(LANG1).autogen.bin
	cp $< $@

###############################################################################
## Bilingual dictionaries
###############################################################################

$(PREFIX1).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp lr $< $@

$(PREFIX2).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $< 
	lt-comp rl $< $@

###############################################################################
## Disambiguation rules 
###############################################################################

$(PREFIX1).rlx.bin: $(AP_SRC1)/apertium-$(LANG1).$(LANG1).rlx
	cg-comp $< $@

$(PREFIX2).rlx.bin: $(AP_SRC2)/apertium-$(LANG2).$(LANG2).rlx
	cg-comp $< $@


###############################################################################
## Lexical selection rules
###############################################################################

$(PREFIX1).autolex.bin: $(BASENAME).$(PREFIX1).lrx
	lrx-comp $< $@

$(PREFIX2).autolex.bin: $(BASENAME).$(PREFIX2).lrx
	lrx-comp $< $@

###############################################################################
## Structural transfer rules
###############################################################################

$(PREFIX1).t1x.bin: $(BASENAME).$(PREFIX1).t1x
	apertium-validate-transfer $(BASENAME).$(PREFIX1).t1x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t1x $@

$(PREFIX1).t2x.bin: $(BASENAME).$(PREFIX1).t2x
	apertium-validate-interchunk $(BASENAME).$(PREFIX1).t2x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t2x $@

$(PREFIX1).t3x.bin: $(BASENAME).$(PREFIX1).t3x
	apertium-validate-postchunk $(BASENAME).$(PREFIX1).t3x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t3x $@

$(PREFIX2).t1x.bin: $(BASENAME).$(PREFIX2).t1x
	apertium-validate-transfer $(BASENAME).$(PREFIX2).t1x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t1x $@

$(PREFIX2).t2x.bin: $(BASENAME).$(PREFIX2).t2x
	apertium-validate-interchunk $(BASENAME).$(PREFIX2).t2x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t2x $@

$(PREFIX2).t3x.bin: $(BASENAME).$(PREFIX2).t3x
	apertium-validate-postchunk $(BASENAME).$(PREFIX2).t3x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t3x $@

###############################################################################
## Postgeneration dictionaries
###############################################################################

$(PREFIX1).autopgen.bin: $(AP_SRC2)/apertium-$(LANG2).post-$(LANG2).dix
	apertium-validate-dictionary $<
	lt-comp lr $< $@

#$(PREFIX2).autopgen.bin: $(BASENAME).post-$(LANG1).dix
#	apertium-validate-dictionary $< 
#	lt-comp lr $< $@

###############################################################################
## Prob files
###############################################################################

$(PREFIX1).prob: $(AP_SRC1)/$(LANG1).prob
	cp $< $@

$(PREFIX2).prob: $(AP_SRC2)/$(LANG2).prob
	cp $< $@

EXTRA_DIST=$(PREFIX1).prob $(PREFIX2).prob \
           $(BASENAME).$(PREFIX1).lrx \
           $(BASENAME).$(PREFIX1).dix \
           $(BASENAME).$(PREFIX2).lrx \
           $(BASENAME).post-$(LANG2).dix \
           $(BASENAME).$(PREFIX1).t1x \
           $(BASENAME).$(PREFIX1).t2x \
           $(BASENAME).$(PREFIX1).t3x \
           $(BASENAME).$(PREFIX2).t1x \
           $(BASENAME).$(PREFIX2).t2x \
           $(BASENAME).$(PREFIX2).t3x \
           modes.xml

#           $(BASENAME).post-$(LANG1).dix 

apertium_isl_rusdir=$(prefix)/share/apertium/$(BASENAME)/

apertium_isl_rus_DATA=$(TARGETS_COMMON) \
                    $(PREFIX1).prob $(PREFIX2).prob

# All modes are created by the same goal, listing several will just give problems with make -j2
noinst_DATA=modes/$(PREFIX1).mode


install-data-local: install-modes
	$(INSTALL_DATA) $(BASENAME).$(PREFIX1).t1x $(DESTDIR)$(apertium_isl_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX1).t2x $(DESTDIR)$(apertium_isl_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX2).t1x $(DESTDIR)$(apertium_isl_rusdir)
	$(INSTALL_DATA) $(BASENAME).$(PREFIX2).t2x $(DESTDIR)$(apertium_isl_rusdir)

CLEANFILES = $(TARGETS_COMMON)
clean-local: 
	-rm -rf .deps

test: all
	apertium-regtest test
