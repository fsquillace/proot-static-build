# How to build PRoot:
#
#     make proot
#
# To clean:
#
#     make clean

proot-version      = proot
glibc-version      = glibc-2.19
libtalloc-version  = talloc-2.1.1

glibc-url          = https://ftp.gnu.org/gnu/libc/$(glibc-version).tar.gz
libtalloc-url      = https://www.samba.org/ftp/talloc/$(libtalloc-version).tar.gz
proot-url          = https://github.com/proot-me/PRoot.git

glibc-license      = sh -c '$(pkg)/lib/libc.so.6; cat $(src)/$(glibc-version)/LICENSES; head -n 16 $(src)/$(glibc-version)/io/open.c'
libtalloc-license  = head -n 27 $(src)/$(libtalloc-version)/talloc.c

libc_a       = $(pkg)/lib/libc.a
libtalloc_a  = $(pkg)/lib/libtalloc.a

env = CFLAGS="-g -O2 -isystem $(pkg)/include" LDFLAGS="-L$(pkg)/lib"

src = $(PWD)/src
pkg = $(PWD)/pkg

mk_build_dirs:
	mkdir $(src) $(pkg)

$(libc_a): mk_build_dirs
	cd $(src)                                              && \
	wget $(glibc-url)                                      && \
	tar -xzf $(glibc-version).tar.gz                       && \
	mkdir $(glibc-version)/build                           && \
	cd $(glibc-version)/build                              && \
	  ../configure --enable-kernel=2.6.0 --prefix=$(pkg)   && \
	  $(MAKE)                                              && \
	  $(MAKE) install

$(libtalloc_a): mk_build_dirs $(libc_a)
	cd $(src)                                              && \
	wget $(libtalloc-url)                                  && \
	tar -xzf $(libtalloc-version).tar.gz                   && \
	cd $(libtalloc-version)                                && \
	  $(env) ./configure --prefix=$(pkg)                   && \
	  $(MAKE)                                              && \
	  $(MAKE) install                                      && \
	  ar qf $@ bin/default/talloc_3.o

all_libs_a = $(libc_a) $(libtalloc_a)

proot-licenses: mk_build_dirs $(libc_a) $(libtalloc_a)
	@echo "" >> $(src)/$@
	@echo "This version of PRoot is statically linked to the following software." >> $(src)/$@
	@echo "------------------------------------------------------------------------" >> $(src)/$@
	@echo "glibc:" >> $(src)/$@
	@echo "" >> $(src)/$@
	@$(glibc-license) >> $(src)/$@
	@echo "------------------------------------------------------------------------" >> $(src)/$@
	@echo "libtalloc:" >> $(src)/$@
	@echo "" >> $(src)/$@
	@$(libtalloc-license) >> $(src)/$@
	@echo "------------------------------------------------------------------------" >> $(src)/$@
	@echo "The build-system, sources and licences are available on:" >> $(src)/$@
	@echo "" >> $(src)/$@
	@echo "    https://github.com/proot-me/proot-static-build">> $(src)/$@

proot: mk_build_dirs $(libc_a) $(libtalloc_a) proot-licenses
	cd $(src)                                          && \
	git clone $(proot-url) $(proot-version)            && \
	cp proot-licenses $(proot-version)/src/licenses    && \
	env OBJECTS="cli/proot-licenses.o" LDFLAGS="-static -L$(pkg)/lib" CPPFLAGS="-isystem $(pkg)/include" $(MAKE) -C $(proot-version)/src/ GIT=false           && \
	cp $(proot-version)/src/$@ .

clean:
	rm -rf $(src) $(pkg)
