# Node.js Core Internals Skill

The `nodejs-core` skill provides deep Node.js internals expertise — V8 engine internals, libuv event loop, N-API/node-addon-api C++ bindings, native memory management, and build systems.

## Skill Reference

```text { .api }
Skill name:  nodejs-core
Tile name:   mcollina/nodejs-core
Version:     0.1.0
Path:        skills/nodejs-core/SKILL.md
Tile:        skills/nodejs-core/tile.json
Tags:        nodejs, v8, libuv, cpp, native-addons, performance, debugging, internals
```

## When to Use

- C++ addon development with N-API or node-addon-api
- V8 engine debugging and JIT optimization
- libuv event loop diagnosis
- Build system problems (node-gyp, binding.gyp)
- Segfaults and memory leaks in native code
- Performance optimization at the engine level

**Trigger terms**: C++ addons, native modules, binding.gyp, node-gyp errors, segfaults, memory leaks in native code, V8 optimization, libuv, N-API, NAN bindings, build failures

## Rule Sub-Documents

```text { .api }
skills/nodejs-core/rules/

V8 Engine:
  v8-garbage-collection.md   - Scavenger, Mark-Sweep, Mark-Compact, generational GC
  v8-hidden-classes.md       - Hidden classes, inline caching, monomorphic optimization
  v8-jit-compilation.md      - TurboFan, optimization/deoptimization, --trace-opt

libuv:
  libuv-event-loop.md        - Event loop phases: timers, pending I/O, idle, prepare, poll, check, close
  libuv-thread-pool.md       - Thread pool tuning, UV_THREADPOOL_SIZE, blocking operations
  libuv-async-io.md          - Async I/O patterns, uv_handle_t, uv_req_t

Native Addons:
  napi.md                    - N-API development, ABI stability across Node.js versions, async workers
  node-addon-api.md          - C++ wrapper patterns using node-addon-api (Napi::)
  native-memory.md           - Buffer handling, external memory, preventing leaks

Core Module Internals:
  streams-internals.md       - How Node.js streams work at the C++ level
  net-internals.md           - TCP/UDP socket implementation
  fs-internals.md            - libuv fs operations, sync vs async paths
  crypto-internals.md        - OpenSSL integration, performance considerations
  child-process-internals.md - IPC, spawn, fork implementation
  worker-threads-internals.md - SharedArrayBuffer, Atomics, MessageChannel

Build & Contributing:
  build-system.md            - node-gyp, binding.gyp, GYP variables, cross-compilation
  cli-options.md             - Node.js CLI flags (--inspect, --heap-prof, etc.)
  commit-messages.md         - Conventional commit format for Node.js core contributions
  contributing.md            - Contributing workflow for Node.js core
  debugging-native.md        - Debugging C++ addons: lldb, gdb, core dumps
  memory-debugging.md        - V8 heap snapshots, native memory debugging with Valgrind
  profiling-v8.md            - V8 CPU profiling (--prof), tick processor, flame graphs
```

## Key Capabilities

### V8 Garbage Collection

```text { .api }
Generations:
  New space (Young):  Scavenger (minor GC) — fast, small objects
  Old space (Old):    Mark-Sweep + Mark-Compact (major GC) — slower, long-lived objects

GC flags:
  --expose-gc                  expose global.gc() for manual GC triggering
  --trace-gc                   log GC events
  --max-old-space-size=<MB>    set old space limit
  --max-semi-space-size=<MB>   set new space size
```

### V8 Hidden Classes and Inline Caching

```text { .api }
Best practices for monomorphic code (V8 optimizes well):
- Always initialize object properties in the same order
- Don't add/delete properties after object creation
- Use TypedArrays for numeric data
- Avoid mixing types in arrays (polymorphic arrays deoptimize)

Anti-patterns (cause deoptimization):
- Adding properties outside constructor
- Changing property types after first assignment
- delete obj.prop (use obj.prop = undefined instead)
```

### libuv Event Loop Phases

```text { .api }
Phase order (per iteration):
1. Timers          - setTimeout, setInterval callbacks
2. Pending I/O     - I/O callbacks deferred from previous loop
3. Idle/Prepare    - internal use
4. Poll            - wait for I/O events, execute I/O callbacks
5. Check           - setImmediate callbacks
6. Close           - close event callbacks (e.g., socket.on('close'))

process.nextTick() runs between each phase (microtask queue)
Promise microtasks run after process.nextTick() callbacks
```

### Thread Pool Tuning

```text { .api }
Default thread pool size: 4 (UV_THREADPOOL_SIZE)
Operations that use the thread pool:
- fs.readFile / fs.writeFile (all async fs ops)
- dns.lookup()
- crypto (scrypt, pbkdf2, randomBytes, etc.)
- zlib (compress/decompress)
- Custom async workers via libuv

Tune:
  UV_THREADPOOL_SIZE=16 node app.js   # up to 128 threads max
```

### N-API Development

```c { .api }
// Basic N-API addon structure
#include <node_api.h>

napi_value Method(napi_env env, napi_callback_info info) {
  napi_value world;
  napi_create_string_utf8(env, "world", NAPI_AUTO_LENGTH, &world);
  return world;
}

napi_value Init(napi_env env, napi_value exports) {
  napi_value fn;
  napi_create_function(env, NULL, 0, Method, NULL, &fn);
  napi_set_named_property(env, exports, "hello", fn);
  return exports;
}

NAPI_MODULE(NODE_GYP_MODULE_NAME, Init)
```

### node-addon-api (C++ wrapper)

```cpp { .api }
#include <napi.h>

Napi::Value Method(const Napi::CallbackInfo& info) {
  Napi::Env env = info.Env();
  return Napi::String::New(env, "world");
}

Napi::Object Init(Napi::Env env, Napi::Object exports) {
  exports.Set("hello", Napi::Function::New(env, Method));
  return exports;
}

NODE_API_MODULE(addon, Init)
```

### binding.gyp Structure

```json { .api }
{
  "targets": [
    {
      "target_name": "addon",
      "sources": ["src/addon.cc"],
      "include_dirs": [
        "<!@(node -p \"require('node-addon-api').include\")"
      ],
      "dependencies": [
        "<!(node -p \"require('node-addon-api').gyp\")"
      ],
      "defines": ["NAPI_DISABLE_CPP_EXCEPTIONS"]
    }
  ]
}
```

## Tile Manifest

```json { .api }
// skills/nodejs-core/tile.json
{
  "name": "mcollina/nodejs-core",
  "version": "0.1.0",
  "private": false,
  "summary": "Debugs native module crashes, optimizes V8 performance, configures node-gyp builds, writes N-API/node-addon-api bindings, and diagnoses libuv event loop issues.",
  "skills": {
    "nodejs-core": {
      "path": "SKILL.md"
    }
  }
}
```
