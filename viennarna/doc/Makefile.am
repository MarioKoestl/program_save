doxygen_verbose = $(doxygen_verbose_@AM_V@)
doxygen_verbose_ = $(doxygen_verbose_@AM_DEFAULT_V@)
doxygen_verbose_0 = @echo "  DOC      $@";

pkgdata_DATA = viennarna.bib

clean_latex = \
{ \
  cd latex && \
  rm -f *.ps *.dvi *.aux *.toc *.idx *.ind *.ilg *.log *.out *.brf *.blg *.bbl refman.pdf doxyref.log && \
  cd .. ; \
}

if WITH_REFERENCE_MANUAL

##--------------------------------------------------##
## prepare variables in case HTML reference manual  ##
## is going to be installed                         ##
##--------------------------------------------------##
if WITH_REFERENCE_MANUAL_HTML
    REFERENCE_MANUAL_FILES_HTML = html/* 
endif WITH_REFERENCE_MANUAL_HTML

##--------------------------------------------------##
## prepare variables in case PDF reference manual   ##
## is going to be installed                         ##
##--------------------------------------------------##
if WITH_REFERENCE_MANUAL_PDF
    REFERENCE_MANUAL_FILES_PDF = @REFERENCE_MANUAL_PDF_NAME@
endif WITH_REFERENCE_MANUAL_PDF

##--------------------------------------------------##
## prepare all rules in case reference manuals are  ##
## going to be (re)generated                        ##
##--------------------------------------------------##
if WITH_REFERENCE_MANUAL_BUILD

dox: $(pkginclude_HEADERS)
	$(doxygen_verbose)@doxygen@ @DOXYGEN_CONF@; touch dox

##--------------------------------------------------##
## In case the HTML manual should be created, here  ##
## is the rule how to do so                         ##
##--------------------------------------------------##
if WITH_REFERENCE_MANUAL_HTML

$(REFERENCE_MANUAL_FILES_HTML): dox

endif WITH_REFERENCE_MANUAL_HTML

##--------------------------------------------------##
## In case the PDF manual should be created, here   ##
## is the rule how to do so                         ##
##--------------------------------------------------##
if WITH_REFERENCE_MANUAL_PDF

$(REFERENCE_MANUAL_FILES_PDF): dox
	$(doxygen_verbose)$(clean_latex); \
  cd latex; pdflatex refman >>doxyref.log 2>&1; \
  /usr/bin/makeindex refman.idx >>doxyref.log 2>&1; \
  bibtex refman >>doxyref.log 2>&1; \
  pdflatex refman >>doxyref.log 2>&1; \
  pdflatex refman >>doxyref.log 2>&1; \
  latex_count=8 ; \
  while egrep -s 'Rerun (LaTeX|to get cross-references right)' refman.log && [ $$latex_count -gt 0 ] ;\
      do \
        echo "Rerunning latex...." ;\
        pdflatex refman >>doxyref.log 2>&1;\
        latex_count=`expr $$latex_count - 1` ;\
      done; \
  /usr/bin/makeindex refman.idx >>doxyref.log 2>&1; \
  pdflatex refman >>doxyref.log 2>&1; \
  mv refman.pdf "../$(REFERENCE_MANUAL_FILES_PDF)"

endif WITH_REFERENCE_MANUAL_PDF

endif WITH_REFERENCE_MANUAL_BUILD

##--------------------------------------------------##
## Tell autoconf/automake to include the necessary  ##
## files in the distribution archive as well as in  ##
## the installation routine                         ##
##--------------------------------------------------##
  html_DATA = $(REFERENCE_MANUAL_FILES_HTML)
  pdf_DATA = $(REFERENCE_MANUAL_FILES_PDF)

endif WITH_REFERENCE_MANUAL


if WITH_REFERENCE_MANUAL_HTML

vrna_htmlrefman_inst: installdirs html
	@$(NORMAL_INSTALL)
	cp -r html/* "$(DESTDIR)$(htmldir)"

vrna_htmlrefman_uninst:
	@$(NORMAL_UNINSTALL)
	rm -rf "$(DESTDIR)$(htmldir)"

else

vrna_htmlrefman_inst:
vrna_htmlrefman_uninst:

endif

install-htmlDATA: vrna_htmlrefman_inst
uninstall-htmlDATA: vrna_htmlrefman_uninst

EXTRA_DIST =  $(pkgdata_DATA) \
              refman.include \
              refman.images \
              html \
              latex \
              @REFERENCE_MANUAL_PDF_NAME@

clean-local:
	$(clean_latex)

# remove all generated content
maintainer-clean-local:
	-rm -rf *.pdf html/ latex/ doxygen.warnings dox

distclean-local:
	-rm -rf *.pdf html/ latex/ doxygen.warnings dox
