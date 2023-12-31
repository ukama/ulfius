#
# Public domain, no copyright. Use at your own risk.
#

ULFIUS_INCLUDE=../include
ULFIUS_LOCATION=../src
ULFIUS_LIBRARY=$(ULFIUS_LOCATION)/libulfius.so
ULFIUS_SCRUTINIZE=$(ULFIUS_INCLUDE)/ulfius.h $(ULFIUS_INCLUDE)/u_private.h $(ULFIUS_INCLUDE)/yuarel.h $(ULFIUS_LOCATION)/ulfius.c $(ULFIUS_LOCATION)/u_map.c $(ULFIUS_LOCATION)/u_request.c $(ULFIUS_LOCATION)/u_response.c $(ULFIUS_LOCATION)/u_send_request.c $(ULFIUS_LOCATION)/u_websocket.c $(ULFIUS_LOCATION)/yuarel.c

CFLAGS+=-Wall -D_REENTRANT -I$(ULFIUS_INCLUDE) -DDEBUG -g -O0 $(CPPFLAGS)
XLD_FLAGS=-L../../vendor/build/lib
LIBS=-lc -lorcania -lulfius -lyder -ljansson -lgnutls -lz $(XLD_FLAGS)
# Use this LIBS below if you don't have/need gnutls
#LIBS=-lc -lorcania -lyder -lulfius -lcheck -lpthread -lm -lrt -lsubunit -L$(ULFIUS_LOCATION)
# Use this LIBS below if you use yder logs
#LIBS=-lc -lorcania -lyder -lulfius -lgnutls -lcheck -lpthread -lm -lrt -lsubunit -L$(ULFIUS_LOCATION)
VALGRIND_COMMAND=valgrind --tool=memcheck --leak-check=full --show-leak-kinds=all
TARGET=u_map core framework websocket
VERBOSE=0
MEMCHECK=0

all: test

clean:
	rm -f *.o u_map core framework websocket valgrind-*.txt

$(ULFIUS_LIBRARY): $(ULFIUS_SCRUTINIZE)
	cd $(ULFIUS_LOCATION) && $(MAKE) debug

u_map: u_map.c
	$(CC) $(CFLAGS) u_map.c -o u_map $(LIBS)

core: core.c
	$(CC) $(CFLAGS) core.c -o core  $(LIBS)

framework: framework.c
	$(CC) $(CFLAGS) framework.c -o framework $(LIBS)

websocket: websocket.c
	$(CC) $(CFLAGS) websocket.c -o websocket $(LIBS)

test: $(ULFIUS_LIBRARY) $(TARGET) test_u_map test_core test_framework test_websocket

test_%: % $(ULFIUS_LIBRARY)
	@if [ "$(VERBOSE)" = "0" ] && [ "$(MEMCHECK)" = "0" ]; then \
		LD_LIBRARY_PATH=$(ULFIUS_LOCATION):${LD_LIBRARY_PATH} ./run_test.sh ./$^; \
	elif [ "$(MEMCHECK)" = "0" ]; then \
		LD_LIBRARY_PATH=$(ULFIUS_LOCATION):${LD_LIBRARY_PATH} ./$^ ; \
	else \
		LD_LIBRARY_PATH=$(ULFIUS_LOCATION):${LD_LIBRARY_PATH} $(VALGRIND_COMMAND) ./$^ 2>valgrind-$@.txt; \
	fi

check: test
