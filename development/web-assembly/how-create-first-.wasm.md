# How create first .wasm

## Please be patient 😂

Hi, if you as well as I decided to understand this topic, then you will need to be a little patient.

I did not succeed the first time.

So, first, let's install `emcc` 

Install:

```text
git clone https://github.com/emscripten-core/emsdk.git
```

further

```text
./emsdk install latest
```

and further

```text
./emsdk activate latest
```

and further...

```text
source ./emsdk_env.sh --build=Release
```

Nice, you install `emcc` and now you can use compiler C/C++ to `wasm` , but you need install `Python 2.7` 

Yeah, finish!

```text
echo "source $(pwd)/emsdk_env.sh --build=Release > /dev/null" >> ~/.bashrc
```

Okay, let's go check.

Create `index.html` 

```text
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>WASM Demo</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
    
    <h1>WASM Demo</h1>

    <script src="script.js"></script>
    <div id="container"></div>    
</body>
</html>
```

Create `squarer.c` 

```text
#include <emscripten.h>

EMSCRIPTEN_KEEPALIVE
int fib(int n) {
  int i, t, a = 0, b = 1;
  for (i = 0; i < n; i++) {
    t = a + b;
    a = b;
    b = t;
  }
  return b;
}

EMSCRIPTEN_KEEPALIVE
int squarer(int num) {
    return num * num;
}
```

And create `script.js` 

```text
fetch('squarer.wasm').then(response =>
  response.arrayBuffer()
).then(bytes => WebAssembly.instantiate(bytes)).then(results => {
  instance = results.instance;
  document.getElementById("container").textContent = instance.exports._squarer(2) + instance.exports._fib(2);
}).catch(console.error);
```

And now go to compile file `squarer.c` 

```text
emcc -O3 -s WASM=1 squarer.c -o squarer.wasm 
```

Wooohoo! We compile file `squarer.wasm` 

And now, we check our site with `Web Server Chrome` , he need a `fetch`

