# -*- make -*-
BASE=..
SUBDIR=doc

# Bring in the default rules
include ../buildlib/defaults.mak

# Debian Doc SGML Documents
SOURCE = $(wildcard *.sgml)
DEBIANDOC_HTML_OPTIONS=-l en.UTF-8
include $(DEBIANDOC_H)

doc: manpages debiandoc

examples/sources.list: examples/sources.list.in apt-verbatim.ent
	sed -e 's#&stable-codename;#$(shell grep --max-count=1 '^<!ENTITY stable-codename "' apt-verbatim.ent | cut -d'"' -f 2)#g' examples/sources.list.in > examples/sources.list

# Examples
SOURCE = examples/apt.conf examples/sources.list examples/configure-index examples/apt-https-method-example.conf
TO = $(DOC)
TARGET = binary
include $(COPY_H)

.PHONY: clean clean/subdirs veryclean veryclean/subdirs manpages/subdirs debiandoc/subdirs all binary doc stats

clean: clean/subdirs
veryclean: veryclean/subdirs
manpages: manpages/subdirs
debiandoc: debiandoc/subdirs

DOCUMENTATIONPO = $(patsubst %.po,%,$(notdir $(wildcard po/*.po)))
DOCDIRLIST = $(addsuffix /makefile,$(DOCUMENTATIONPO))

dirs: $(DOCDIRLIST)
$(DOCDIRLIST) :: %/makefile : lang.makefile
	test -d $(dir $@) || mkdir $(dir $@)
	sed "s#@@LANG@@#$(subst /,,$(dir $@))#" $< > $@

debiandoc/subdirs manpages/subdirs clean/subdirs veryclean/subdirs:
	for dir in en $(dir $(DOCDIRLIST)); do \
		$(MAKE) -C $$dir $(patsubst %/subdirs,%,$@); \
	done

stats:
	for i in po/*.po; do echo -n "$$i: "; msgfmt --output-file=/dev/null --statistics $$i; done

ifdef PO4A
MANPAGEPOLIST = $(addprefix manpages-translation-,$(DOCUMENTATIONPO))
DEBIANDOCPOLIST = $(addprefix debiandoc-translation-,$(DOCUMENTATIONPO))

.PHONY: update-po po4a $(MANPAGEPOLIST) $(DEBIANDOCPOLIST) $(DOCDIRLIST)

po4a: manpages/subdirs debiandoc/subdirs

update-po:
	po4a --previous --no-backups --force --no-translations \
		--package-name='$(PACKAGE)-doc' --package-version='$(PACKAGE_VERSION)' \
		--msgid-bugs-address='$(PACKAGE_MAIL)' po4a.conf

manpages/subdirs: $(MANPAGEPOLIST)
$(MANPAGEPOLIST) :: manpages-translation-% : %/makefile po4a.conf
	# first line is for apt.8 (see Bug#696923)
	po4a --previous --no-backups --translate-only $(dir $<)apt.ent \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.8,%.$(subst /,,$(dir $<)).8,$(wildcard *.8))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.1.xml,%.$(subst /,,$(dir $<)).1.xml,$(wildcard *.1.xml))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.2.xml,%.$(subst /,,$(dir $<)).2.xml,$(wildcard *.2.xml))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.3.xml,%.$(subst /,,$(dir $<)).3.xml,$(wildcard *.3.xml))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.4.xml,%.$(subst /,,$(dir $<)).4.xml,$(wildcard *.4.xml))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.5.xml,%.$(subst /,,$(dir $<)).5.xml,$(wildcard *.5.xml))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.6.xml,%.$(subst /,,$(dir $<)).6.xml,$(wildcard *.6.xml))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.7.xml,%.$(subst /,,$(dir $<)).7.xml,$(wildcard *.7.xml))) \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.8.xml,%.$(subst /,,$(dir $<)).8.xml,$(wildcard *.8.xml))) \
		--package-name='$(PACKAGE)-doc' --package-version='$(PACKAGE_VERSION)' \
		--msgid-bugs-address='$(PACKAGE_MAIL)' po4a.conf

debiandoc/subdirs: $(DEBIANDOCPOLIST)
$(DEBIANDOCPOLIST) :: debiandoc-translation-% : %/makefile po4a.conf
	po4a --previous --no-backups --translate-only $(dir $<)apt.ent \
		$(patsubst %,--translate-only $(dir $<)%,$(patsubst %.sgml,%.$(subst /,,$(dir $<)).sgml,$(wildcard *.sgml))) \
		--package-name='$(PACKAGE)-doc' --package-version='$(PACKAGE_VERSION)' \
		--msgid-bugs-address='$(PACKAGE_MAIL)' po4a.conf
endif

ifdef DOXYGEN
DOXYGEN_SOURCES = $(shell find $(BASE)/apt-pkg -not -name .\\\#* -and \( -name \*.cc -or -name \*.h \) )

clean: doxygen-clean

doxygen-clean:
	rm -fr $(BUILD)/doc/doxygen
	rm -f $(BUILD)/doc/doxygen-stamp

$(BUILD)/doc/doxygen-stamp: $(DOXYGEN_SOURCES) $(BUILD)/doc/Doxyfile
	rm -fr $(BUILD)/doc/doxygen
	mkdir $(BUILD)/doc/doxygen  # some versions seem to not create this directory #628799
	$(DOXYGEN) $(BUILD)/doc/Doxyfile
	touch $(BUILD)/doc/doxygen-stamp

debiandoc: $(BUILD)/doc/doxygen-stamp
endif
