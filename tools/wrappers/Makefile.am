############################################
# Install wrappers for various compilers   #
# to facilitate compiling them with Byfl   #
#					   #
# By Scott Pakin <pakin@lanl.gov>	   #
############################################

nodist_bin_SCRIPTS = \
	$(BF_GCC_SCRIPT) \
	bf-clang \
	bf-inst

EXTRA_DIST = \
	bf-clang.in \
	bf-inst.in \
	ParseCompilerOpts.pm \
	make-bf-mpi

CLEANFILES = $(nodist_bin_SCRIPTS)

AM_DEFAULT_VERBOSITY ?= 1
SILENT_MAKE = $(SILENT_MAKE_$(V))
SILENT_MAKE_ = $(SILENT_MAKE_$(AM_DEFAULT_VERBOSITY))
SILENT_MAKE_0 = -s
SILENT_MAKE_1 = NOECHO=""

pkgconfigdir=$(libdir)/pkgconfig
pkgconfig_DATA = byfl-clang.pc

install-parser: $(srcdir)/ParseCompilerOpts.pm
	$(INSTALL_DATA) -m 0755 -d "$(DESTDIR)$(perlsitelibdir)"
	$(INSTALL_DATA) "$(srcdir)/ParseCompilerOpts.pm" "$(DESTDIR)$(perlsitelibdir)/ParseCompilerOpts.pm"

install-compiler-scripts: bf-clang
	$(LN_S) -f bf-clang $(DESTDIR)$(bindir)/bf-clang++
	$(LN_S) -f bf-clang $(DESTDIR)$(bindir)/bf-flang

install-mpi-scripts: $(srcdir)/make-bf-mpi
	$(AM_V_GEN) $(PERL) $(srcdir)/make-bf-mpi mpicc bf-clang OMPI_CC MPICH_CC $(bindir) > $(DESTDIR)$(bindir)/bf-mpicc
	$(AM_V_GEN) $(PERL) $(srcdir)/make-bf-mpi mpicxx bf-clang++ OMPI_CXX MPICH_CXX $(bindir) > $(DESTDIR)$(bindir)/bf-mpicxx
	$(AM_V_GEN) $(PERL) $(srcdir)/make-bf-mpi mpifort bf-flang OMPI_FC MPICH_F90 $(bindir) > $(DESTDIR)$(bindir)/bf-mpifort
	$(AM_V_GEN) $(PERL) $(srcdir)/make-bf-mpi mpif90 bf-flang OMPI_FC MPICH_F90 $(bindir) > $(DESTDIR)$(bindir)/bf-mpif90
	$(AM_V_GEN) $(PERL) $(srcdir)/make-bf-mpi mpif77 bf-flang OMPI_F77 MPICH_F77 $(bindir) > $(DESTDIR)$(bindir)/bf-mpif77
	chmod 0755 $(DESTDIR)$(bindir)/bf-mpi*

install-man1-pages: bf-clang bf-inst
	$(INSTALL) -d "$(DESTDIR)$(mandir)/man1"
	$(RM) "$(DESTDIR)$(mandir)/man1/bf-clang.1" \
	      "$(DESTDIR)$(mandir)/man1/bf-clang++.1" \
	      "$(DESTDIR)$(mandir)/man1/bf-flang.1"
	$(AM_V_GEN) $(POD2MAN) --center="$(MAN_CATEGORY)" --release="$(MAN_RELEASE)" bf-clang > \
	  "$(DESTDIR)$(mandir)/man1/bf-clang.1"
	$(AM_V_GEN) cat "$(DESTDIR)$(mandir)/man1/bf-clang.1" | \
	  $(PERL) -ne 's/clang/clang++/g; s/\bC (compiler|code)/C++ $$1/g; s/myprog\.c/myprog.cpp/g; s/BF_CLANG/BF_CLANGXX/g; print' > \
	    "$(DESTDIR)$(mandir)/man1/bf-clang++.1"
	$(AM_V_GEN) cat "$(DESTDIR)$(mandir)/man1/bf-clang.1" | \
	  $(PERL) -ne 's/clang/flang/g; s/\bC (compiler|code)/Fortran $$1/g; s/myprog\.c/myprog.f90/g; s/BF_CLANG/BF_FLANG/g; print' > \
	    "$(DESTDIR)$(mandir)/man1/bf-flang.1"
	$(AM_V_GEN) $(POD2MAN) --center="$(MAN_CATEGORY)" --release="$(MAN_RELEASE)" bf-inst > \
	  "$(DESTDIR)$(mandir)/man1/bf-inst.1"

install-man3-pages: $(srcdir)/ParseCompilerOpts.pm
	$(INSTALL) -d "$(DESTDIR)$(mandir)/man3"
	$(RM) "$(DESTDIR)$(mandir)/man3/ParseCompilerOpts.3pm"
	$(AM_V_GEN) $(POD2MAN) --name=ParseCompilerOpts --section=3pm --center="$(MAN_CATEGORY)" --release="$(MAN_RELEASE)" "$(srcdir)/ParseCompilerOpts.pm" > "$(DESTDIR)$(mandir)/man3/ParseCompilerOpts.3pm"

install-exec-local: install-parser install-compiler-scripts install-mpi-scripts install-man1-pages install-man3-pages

uninstall-local:
	for script in bf-clang bf-clang++ bf-flang bf-mpicc bf-mpicxx bf-mpifort bf-mpif90 bf-mpif77 bf-inst ; do \
	  $(RM) "$(DESTDIR)$(bindir)/$${script}" ; \
	  $(RM) "$(DESTDIR)$(mandir)/man1/$${script}.1" ; \
	done
	$(RM) "$(DESTDIR)$(mandir)/man3/ParseCompilerOpts.3pm"
	$(RM) "$(DESTDIR)$(perlsitelibdir)/ParseCompilerOpts.pm"

.PHONY: install-parser install-compiler-scripts install-mpi-scripts install-man1-pages install-man3-pages
