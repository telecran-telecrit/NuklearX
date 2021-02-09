# NuklearX
NuklearX (read as "Nuklear-X") is an overlay over Nuklear [Nuklear](https://github.com/vurtun/nuklear) GUI library.

Write one simple code, compile it for any supported frontend.

NuklearX is out-of-box configured by preprocessor variable NKCD=NKC_x (NKC_XLIB, NKC_SDL, NKC_GLFW, NKC_GDIP).
E.g.: gcc -DNKCD=NKC_GLFW main.c


## Why?
[Nuklear](https://github.com/vurtun/nuklear) itself requires a lot of additional code to create OS window etc. Nuklear+ hide it all from programmer. Just write your code, do not think about OS!


## Docs
You can use [Doxygen docs](https://nuklear-cross.dexp.in). Or just have a look at [nuklear_cross.h](nuklear_cross.h) for all function descriptions.

## Complete Example
It's the same example as in [original Nuklear](https://github.com/vurtun/nuklear#example). The only difference - you need to add code in original example. This one is working out of the box.

```c
#include "nuklear_cross.h"
#include <stdio.h>

enum radioOptions {
    EASY,
    HARD
};

struct my_nkc_app {
    struct nkc* nkcHandle;

    /* some user data */
    float value;
    enum radioOptions op;
};

void mainLoop(void* loopArg){
    struct my_nkc_app* myapp = (struct my_nkc_app*)loopArg;
    struct nk_context *ctx = nkc_get_ctx(myapp->nkcHandle);

    union nkc_event e = nkc_poll_events(myapp->nkcHandle);
    if( (e.type == NKC_EWINDOW) && (e.window.param == NKC_EQUIT) ){
        nkc_stop_main_loop(myapp->nkcHandle);
    }

    /* Nuklear GUI code */
    if (nk_begin(ctx, "Show", nk_rect(50, 50, 220, 220),
        NK_WINDOW_BORDER|NK_WINDOW_MOVABLE|NK_WINDOW_CLOSABLE)) {
        /* fixed widget pixel width */
        nk_layout_row_static(ctx, 30, 80, 1);
        if (nk_button_label(ctx, "button")) {
            /* event handling */
            printf("Button pressed\n");
        }

        /* fixed widget window ratio width */
        nk_layout_row_dynamic(ctx, 30, 2);
        if (nk_option_label(ctx, "easy", myapp->op == EASY)) myapp->op = EASY;
        if (nk_option_label(ctx, "hard", myapp->op == HARD)) myapp->op = HARD;

        /* custom widget pixel width */
        nk_layout_row_begin(ctx, NK_STATIC, 30, 2);
        {
            nk_layout_row_push(ctx, 50);
            nk_label(ctx, "Volume:", NK_TEXT_LEFT);
            nk_layout_row_push(ctx, 110);
            nk_slider_float(ctx, 0, &(myapp->value), 1.0f, 0.1f);
        }
        nk_layout_row_end(ctx);
    }
    nk_end(ctx);
    /* End Nuklear GUI */

    nkc_render(myapp->nkcHandle, nk_rgb(40,40,40) );
}

int main(){
    struct my_nkc_app myapp;
    struct nkc nkcx; /* Allocate memory for Nuklear+ handle */
    myapp.nkcHandle = &nkcx;
    /* init some user data */
    myapp.value = 0.4;
    myapp.op = HARD;

    if( nkc_init( myapp.nkcHandle, "Nuklear+ Example", 640, 480, NKC_WIN_NORMAL ) ){
        printf("Successfull init. Starting 'infinite' main loop...\n");
        nkc_set_main_loop(myapp.nkcHandle, mainLoop, (void*)&myapp );
    } else {
        printf("Can't init NKC\n");
    }
    printf("Value after exit = %f\n", myapp.value);
    nkc_shutdown( myapp.nkcHandle );
    return 0;
}
```

![example](https://cloud.githubusercontent.com/assets/8057201/10187981/584ecd68-675c-11e5-897c-822ef534a876.png)


## More examples

- dxBin2h: https://github.com/DeXP/dxBin2h
- Nuklear Web Demo: https://github.com/DeXP/nuklear-webdemo



## How to use

The best way is using Git submodules. In your project's directory:

```
git submodule add https://github.com/DeXP/nuklear_cross
```

It will create `nuklear_cross` subdirectory and will fetch all content. When you will want to actualize content:

```
cd nuklear_cross
git checkout master
git pull
```


## Applications and games, made on NuklearX

- Wordlase: [Steam](http://store.steampowered.com/app/602930/Wordlase/), [Web demo](https://wordlase.dexp.in/), [site](https://dexp.in/games/wordlase/)


## FAQ

- Are main loop function usage required?
- Only for Emscripten. You can organize main loop as you want if you do not plannig to make a web version.

## Updating Nuklear files
The library aims to use vanilla Nuklear.h and driver files from [demo](https://github.com/vurtun/nuklear/tree/master/demo) folder. So in general case you can just replace file by file from [Nuklear](https://github.com/vurtun/nuklear) repo. By the way, the API in demos can be changed. In that case frontend will stop work. Also, there can some functionality, not yet added to Nuklear's main repo. Also, drivers can need some small fixes. The path of `stb_image.h` in `nuklear_xlib.h` for example.

## License:
The authors want give you freedom wihtout warranty: you are granted a perpetual, irrevocable license to copy, modify, publish and distribute this file as you see fit.
Alternatively you can use original license model of [Nuklear](https://github.com/vurtun/nuklear):
```
------------------------------------------------------------------------------
This software is available under 2 licenses -- choose whichever you prefer.
------------------------------------------------------------------------------
ALTERNATIVE A - MIT License
Copyright (c) 2017 Micha Mettke
Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
------------------------------------------------------------------------------
ALTERNATIVE B - Public Domain (www.unlicense.org)
This is free and unencumbered software released into the public domain.
Anyone is free to copy, modify, publish, use, compile, sell, or distribute this
software, either in source code form or as a compiled binary, for any purpose,
commercial or non-commercial, and by any means.
In jurisdictions that recognize copyright laws, the author or authors of this
software dedicate any and all copyright interest in the software to the public
domain. We make this dedication for the benefit of the public at large and to
the detriment of our heirs and successors. We intend this dedication to be an
overt act of relinquishment in perpetuity of all present and future rights to
this software under copyright law.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
-----------------------------------------------------------------------------
```
