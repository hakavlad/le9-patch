NAME = mem2log
DESTDIR ?=
PREFIX ?= /usr/local
BINDIR ?= $(PREFIX)/bin

all:
	@ echo "Use: make install, make uninstall"

install:
	install -p -d $(DESTDIR)$(BINDIR)
	install -p -m0755 $(NAME) $(DESTDIR)$(BINDIR)/$(NAME)

uninstall:
	rm -fv $(DESTDIR)$(BINDIR)/$(NAME)
