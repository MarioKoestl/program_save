include ../generic.mk

PERL_MODULE = $(SWIG_module_name).pm

perlextdir = $(prefix)$(PERL_ARCH_RELATIVE_INSTALL_DIR)/auto/RNA
perlext_LTLIBRARIES = RNA.la

INTERFACE_FILES = $(SWIG_src) \
                  tmaps.i \
                  globals-md.i \
                  callbacks-fc.i \
                  callbacks-sc.i

RNA_la_SOURCES =  $(INTERFACE_FILES) \
                  $(SWIG_wrapper)

perlmoddir = $(prefix)$(PERL_LIB_RELATIVE_INSTALL_DIR)
nobase_dist_perlmod_DATA = \
  $(PERL_MODULE) \
  RNA/Benchmark.pm \
  RNA/Design.pm \
  RNA/Files.pm \
  RNA/Reliability.pm \
  RNA/Utils.pm \
  RNA/Params.pm

RNA_la_CPPFLAGS = -I$(top_srcdir)/src $(PERL_EXT_CPPFLAGS) $(SWIG_PERL_CPPFLAGS) $(PERLXS_CFLAGS) @FLOAT_PF_FLAG@ @DEPRECATION_WARNING@

RNA_la_LIBADD = $(top_builddir)/src/ViennaRNA/libRNA_conv.la $(PERLXS_LIBS)
if WITH_LIBSVM
RNA_la_LIBADD += -lstdc++
endif

RNA_la_LDFLAGS = -avoid-version -module -shared -export-dynamic -shrext $(PERL_SO) $(PERLXS_LDFLAGS)

SOURCES = RNA.pod

BUILT_SOURCES = auto/RNA/RNA$(PERL_SO)

EXTRA_DIST = \
  $(SWIG_wrapper) \
  $(PERL_MODULE) \
  RNA/t/Design.t

##
## SWIG interface wrapper code generation
##

$(SWIG_wrapper) $(PERL_MODULE): $(INTERFACE_FILES)
	$(AM_V_GEN)$(SWIG) -I$(top_srcdir)/src @FLOAT_PF_FLAG@ \
                  -perl5 -proxy -const -c++ \
                  -module RNA -o $(SWIG_wrapper) \
                  $(SWIG_main_src)

##
## copy shared library for perl-tests
##

auto/RNA/RNA$(PERL_SO): RNA.la
	$(MKDIR_P) auto/RNA
	$(LIBTOOL) --config > libtoolconfig.tmp
## ksh requires a path on the sourced file.
	. ./libtoolconfig.tmp; cp "$$objdir/RNA$(PERL_SO)" auto/RNA
	rm -f libtoolconfig.tmp

clean-local:
	-rm -rf auto

## Remove the .la file - RNA.la is never linked against (it's a module)
## and Perl doesn't use libltdl.  Note that the library gets installed by
## install-data, so that's where we need to hook.
install-data-hook:
	rm -f $(DESTDIR)$(perlextdir)/RNA.la

## Because we don't install the .la file, "make uninstall" doesn't work and
## we need to remove the file ourselves.
uninstall-local:
	eval `grep '^dlname=' $(perlext_LTLIBRARIES)` ; \
	rm -f $(DESTDIR)$(perlextdir)/"$$dlname"

## remove all generated content
maintainer-clean-local:
	-rm -f $(SWIG_wrapper) $(PERL_MODULE)

distclean-local:
	-rm -f $(SWIG_wrapper) $(PERL_MODULE)
