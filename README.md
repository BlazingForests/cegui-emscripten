CEGUI Samples for Emscripten / WebGL 
====================================

This is a port of CEGUI v0-8 branch (v0-8.4 iirc).  
The most significant code change was porting the OpenGL3 core calls to OpenGLES2.  
Some changes needed to be made to the samples framework so that it is all linked at compile time as well.  
There was also a complete replacement of the build system. This was in order to make it easier to build on emscripten without compromising the ability to make native builds.  

**Note**: The targetted OpenGL version is, OpenGLES 2.0, with Vertex Array Object extension.
Not all devices have this, but it seems that many (most?) of them do. The extension was made core in WebGL 2.0. (https://www.khronos.org/registry/webgl/extensions/OES_vertex_array_object/)

# External dependencies (for emscripten build)  

- CMake version >= 2.8.12	
      http://www.cmake.org/
- Emscripten (master branch)  
      http://www.emscripten.org/
- Node.js (needed by Emscripten. v0.10 suffices, but a more recent version like v0.12.4 will allow the js optimizer portion of Emscripten to run faster)

- That's it!

For a detailed installation guide please see here: http://developer.humblebundle.com/post/112252930481/developing-for-asm-js-using-sdl2

# External dependencies (for native build)

- CMake version >= 2.8.12  
      http://www.cmake.org/
- A C++11 compiler (tested with gcc 4.8.4 and clang 3.6)  
- OpenGLES2 development libraries
- SDL2 development libraries
- libpng development libraries
- zlib development libraries      

**Note:** There is a decent chance that you will have to reconfigure the CMake scripts / config headers appropriately for your system, in the folders `lib/expat2.1.0`, `lib/freetype2.5.5`, `lib/SILLY`, and/or `lib/pcre8.37`.  
If you have problems it may be easier just to skip the native build on your machine and only try to use the emscripten build, which should be totally portable.  

At a **minimum**, to link on windows / OS X you will most likely have to get rid of the define "GL_GLEXT_PROTOTYPES" here: 

https://github.com/cbeck88/cegui-emscripten/blob/ed06988b6047d33ea5ec312fc5a1a4062295d0ea/lib/cegui/src/RendererModules/OpenGLES2/GLES2_headers.hpp#L4

which links to exported gl extension symbols, rather than use the more standard (and portable) function fetch mechanism.

(See the effect of the #define here:  https://github.com/cbeck88/cegui-emscripten/blob/ed06988b6047d33ea5ec312fc5a1a4062295d0ea/lib/SDL2/include/SDL2/SDL_opengles2_gl2ext.h#L1182)

# How to build

Currently you must build using CMake at command line. I placed appropriate commands in the shell scripts at repo root "clean_build.sh", "clean_build-clang.sh", "clean_build_em.sh" etc. (These work for me on linux mint 17 after installing appropriate libraries using apt-get.)  
  
In principle it should be possible to build for windows / OS X, but it is likely that it will require some amount of work. I brought all the CEGUI external dependencies in tree -- this is because emscripten is a bit finnicky and can have problems if libraries are not compiled using the same optimization settings and such, and it ensures consistency between the native build and em build. Sometimes this means that I deleted an "autotools" configuration system and tried to rewrite it in my cmake framework. If your build is broken on windows / OS X, I suggest to go into /lib/ folder and have a look at CMakeLists.txt files which I added, and also any "config.h" inclusion files for possible configuration issues.


# Notes

The build system is *exactly* the one used by the humble developers in their sample project described here:  
  http://developer.humblebundle.com/post/112252930481/developing-for-asm-js-using-sdl2  

In fact you can think of this project as a template for using CEGUI within games targetted at the humble bundle store / humble widget.  
  
The advantages of that build system are that it has some nice cmake macros for declaring libraries and executables, and it automatically handles the process of copying everything in "assets" into a virtual filesystem blob that gets loaded when your javascript app starts up. I did not want to have to reengineer that into the cegui cmake.  

# Reorg / renames

| CEGUI v0-8 branch             |  here        |
|-------------------------------|--------------|
| datafiles/ 			| assets/      |
| samples_framework/include 	| include/     |
| samples_framework/src		| src/         |
| samples/			| lib/samples/ |
| CEGUI/			| lib/CEGUI/   |

# Caveat Emptor

My GLES2 port is not really finished, some functions I simply commented out and replaced with `assert(false && "Not implemented yet!");` 
