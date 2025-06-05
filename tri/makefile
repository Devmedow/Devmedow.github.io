CC=emcc
CFLAGS= -s USE_GLFW=3 -s LEGACY_GL_EMULATION=1 --shell-file shell.html #-s WASM=1 -s ALLOW_MEMORY_GROWTH=1
tri:
	$(CC) tri.c -o tri.html $(CFLAGS)
run:
	emrun tri.html
clean:
	rm -f tri.html
