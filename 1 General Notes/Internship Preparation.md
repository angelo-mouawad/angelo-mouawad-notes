# General Information

Data structures 
Algorithms
Programing concepts
Data Analysis (precision, accuracy ...)

---

## Understanding Programming Languages 

| Language     | Compiler or Interpreter                                 | Installer or Package Manager                            | Popular Libraries & Frameworks                                           | Main Uses                                                             |
| ------------ | ------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------------------- |
| Python       | CPython (interpreter), PyPy                             | pip, conda, uv, Poetry                                  | NumPy, Pandas, scikit-learn, PyTorch, TensorFlow, Django, Flask, FastAPI | AI/ML, data science, scripting, automation, backend web               |
| JavaScript   | V8 (Node.js, Chrome), SpiderMonkey, Bun, Deno           | npm, yarn, pnpm                                         | React, Vue, Angular, Express, Next.js, Three.js                          | Frontend web, backend (Node.js), mobile (React Native)                |
| TypeScript   | tsc (compiles to JS), esbuild, swc                      | npm, yarn, pnpm                                         | Same as JavaScript, plus NestJS, tRPC, Zod                               | Large scale web apps, typed frontend and backend                      |
| Java         | javac (to bytecode), runs on the JVM                    | Maven, Gradle                                           | Spring Boot, Hibernate, JUnit, Apache Commons                            | Enterprise backends, Android, banking systems                         |
| C            | GCC, Clang, MSVC                                        | vcpkg, Conan, system package managers (No official one) | Standard C library, glibc, SDL                                           | Embedded systems, operating systems, drivers, firmware                |
| C++          | g++, Clang++, MSVC                                      | vcpkg, Conan                                            | STL, Boost, Qt, OpenCV, Unreal Engine                                    | Game engines, high performance apps, embedded, simulations            |
| C#           | Roslyn (csc), runs on .NET CLR                          | NuGet, dotnet CLI                                       | ASP.NET Core, Entity Framework, Unity, MAUI                              | Windows apps, games (Unity), enterprise web backends                  |
| Go           | gc (go build), gccgo                                    | go modules (go get)                                     | Gin, Echo, Cobra, gRPC                                                   | Cloud services, microservices, CLI tools, DevOps (Docker, Kubernetes) |
| Rust         | rustc                                                   | Cargo (crates.io), rustup                               | Tokio, Serde, Actix, Axum, Bevy                                          | Systems programming, safe high performance code, WebAssembly          |
| Kotlin       | kotlinc (JVM, JS, native)                               | Gradle, Maven                                           | Jetpack Compose, Ktor, Coroutines                                        | Android apps, backend on the JVM                                      |
| Swift        | swiftc (LLVM based)                                     | Swift Package Manager, CocoaPods                        | SwiftUI, UIKit, Combine, Vapor                                           | iOS, macOS, watchOS apps                                              |
| PHP          | Zend Engine (interpreter)                               | Composer                                                | Laravel, Symfony, WordPress                                              | Server side web, CMS sites                                            |
| Ruby         | MRI/CRuby, JRuby                                        | RubyGems, Bundler                                       | Ruby on Rails, Sinatra, RSpec                                            | Web apps, startups, scripting                                         |
| R            | R interpreter                                           | CRAN (install.packages), renv                           | ggplot2, dplyr, tidyverse, Shiny, caret                                  | Statistics, data analysis, research, visualization                    |
| Dart         | Dart VM, dart2js, AOT compiler                          | pub                                                     | Flutter                                                                  | Cross platform mobile, web and desktop apps                           |
| SQL          | Database engine (PostgreSQL, MySQL, SQLite, SQL Server) | Comes with the database                                 | Stored procedures, extensions like PostGIS                               | Querying and managing relational databases                            |
| Bash / Shell | bash, zsh, sh                                           | System package managers (apt, brew)                     | Core Unix utilities (grep, sed, awk)                                     | Automation, server admin, build scripts                               |
| Lua          | Lua interpreter, LuaJIT                                 | LuaRocks                                                | LÖVE, Roblox (Luau)                                                      | Game scripting, embedded configs, plugins                             |
| Scala        | scalac (JVM)                                            | sbt, Maven                                              | Apache Spark, Akka, Play                                                 | Big data processing, functional backend systems                       |
| Julia        | Julia JIT compiler (LLVM)                               | Pkg                                                     | Flux, DifferentialEquations.jl, Plots                                    | Scientific computing, numerical analysis                              |
| MATLAB       | MATLAB interpreter (proprietary)                        | Add On Explorer                                         | Simulink, toolboxes (Signal, Image)                                      | Engineering, signal processing, control systems                       |
| Haskell      | GHC                                                     | Cabal, Stack                                            | Yesod, Servant, Parsec                                                   | Functional programming, compilers, research                           |
| Elixir       | Runs on the BEAM (Erlang VM)                            | Mix, Hex                                                | Phoenix, Ecto, LiveView                                                  | Real time systems, chat apps, fault tolerant backends                 |
| Assembly     | NASM, MASM, GAS                                         | None                                                    | None (hardware specific)                                                 | Low level hardware control, reverse engineering, bootloaders          |

## How Code Actually Runs, Compiled, Bytecode and Interpreted Languages

A computer's CPU only understands **machine code**, raw binary instructions specific to that processor (x86, ARM). Every programming language needs some way to turn human readable source code into something the CPU can execute. There are three main strategies, and which one a language uses explains a lot about its speed, portability and typical use cases.

---

## Key Terms

| Term                | Meaning                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------- |
| **Source code**     | The code you write (`main.c`, `App.java`, `script.py`)                                          |
| **Machine code**    | Binary instructions the CPU runs directly. Specific to one CPU architecture                     |
| **Compiler**        | A program that translates the whole source code into another form _before_ running it           |
| **Interpreter**     | A program that reads and executes code _while_ the program runs                                 |
| **Bytecode**        | An in between format. Not human code, not machine code. Designed to be run by a virtual machine |
| **Virtual Machine** | A program that runs bytecode, e.g. the JVM (Java) or CLR (.NET)                                 |
| **AOT**             | Ahead Of Time compilation: everything is compiled before the program runs                       |
| **JIT**             | Just In Time compilation: code is compiled to machine code _during_ execution                   |
| **Runtime**         | The period while the program is running (as opposed to compile time)                            |

---

## 2. Strategy 1: Compiled to Machine Code (AOT)

**Languages:** C, C++, Rust, Go, Swift, Haskell

The compiler translates your entire program into a machine code executable _before_ it ever runs. The result is a file (`.exe` on Windows, a binary on Linux/macOS) that the CPU can run directly. No extra software is needed to run it.

```
source code  ->  COMPILER  ->  machine code (executable)  ->  CPU runs it
 main.c           gcc            main.exe / ./main
```

### The steps inside (using C as the example)

1. **Preprocessing**: handles `#include` and `#define`, pastes header files in
2. **Compiling**: turns C code into assembly language
3. **Assembling**: turns assembly into object code (`.o` files, binary but not yet complete)
4. **Linking**: combines all object files and libraries into one final executable

```bash
gcc main.c -o main   # does all 4 steps at once
./main               # run the result, no compiler needed anymore
```

### Pros

- **Fastest execution.** The CPU runs native instructions with no middleman
- **Errors caught early.** Type errors and syntax errors show up at compile time, not in production
- **No runtime needed.** You ship a single file; the user does not need to install anything
- **Full control over memory and hardware**, which is why embedded systems use C

### Cons

- **Not portable.** A binary compiled for Windows x86 does not run on a Mac with an ARM chip. You must recompile for every platform
- **Compile step slows down development.** Big C++ projects can take minutes (or much longer) to build
- **Changes need a rebuild** before you can test them

---

## 3. Strategy 2: Compiled to Bytecode + Virtual Machine

**Languages:** Java, Kotlin, Scala (JVM), C# (.NET CLR), Elixir (BEAM)

This is a middle ground. The compiler does _not_ produce machine code. It produces **bytecode**, a compact, platform independent instruction set. A **virtual machine** installed on the target computer then runs that bytecode.

```
source code  ->  COMPILER  ->  bytecode  ->  VIRTUAL MACHINE  ->  CPU
 App.java        javac        App.class        JVM
```

```bash
javac App.java   # produces App.class (bytecode)
java App         # the JVM loads and runs App.class
```

### Why bother with bytecode?

This is the idea behind Java's old slogan **"write once, run anywhere"**. You compile once, and the same `.class` file runs on Windows, Linux, macOS or anything else that has a JVM. The VM is the part that is platform specific, so you do not have to be.

### Modern VMs are not slow

The JVM and .NET CLR use **JIT compilation** (see section 5). They watch which parts of your code run often and compile those parts to real machine code while the program runs. That is why Java and C# are much faster than purely interpreted languages and often come close to C++.

### Pros

- **Portable.** One build runs on any platform with the VM
- **Fast** thanks to JIT, especially for long running programs like servers
- **Still catches errors at compile time** (Java, C#, Kotlin are statically typed)
- **Safety features** like garbage collection and memory safety built into the VM

### Cons

- **Needs the VM installed** (or bundled) on the target machine
- **Slower startup.** The VM has to boot and warm up before JIT kicks in
- **Uses more memory** than a native C/Rust program

---

## 4. Strategy 3: Interpreted and JIT Compiled

**Languages:** Python, JavaScript, PHP, Ruby, R, Lua, Bash

Here there is no separate build step that you run yourself. You hand the source file to the interpreter and it runs straight away.

```
source code  ->  INTERPRETER (reads + executes at runtime)  ->  CPU
 script.py       python
```

```bash
python script.py   # no compile step, just run
node app.js
```

### What really happens under the hood

"Interpreted" is a simplification. Most modern interpreters do a quick internal compile first:

- **Python (CPython)** compiles your `.py` file to Python bytecode (you sometimes see these as `.pyc` files in a `__pycache__` folder), then a bytecode interpreter loop runs it instruction by instruction. There is no big optimizing JIT in standard CPython, which is a main reason Python is slow for heavy loops
- **JavaScript (V8, used in Chrome and Node.js)** parses the code, starts running it in an interpreter (Ignition), then JIT compiles hot functions into optimized machine code (TurboFan). That is why modern JavaScript is surprisingly fast

So the real difference is: **the translation happens automatically at runtime**, hidden from you, instead of in a separate build step.

### Pros

- **Fast to develop.** Edit, save, run. No waiting for builds
- **Portable.** The same script runs anywhere the interpreter is installed
- **Interactive.** You can use a REPL (`python`, `node`) or notebooks (Jupyter) to test code line by line
- **Usually dynamically typed and flexible**, which makes prototyping quick

### Cons

- **Slower execution**, especially for CPU heavy work (Python can be 10 to 100 times slower than C for tight loops)
- **Errors show up at runtime.** A typo in a rarely used branch may only crash when that line actually runs
- **Needs the interpreter installed** on every machine that runs the code

### How Python gets around the speed problem

Libraries like NumPy, Pandas and PyTorch are written mostly in **C, C++ and CUDA**. Python is just the friendly control layer on top. When you call `np.dot(a, b)`, the actual math runs in compiled C code. This is the reason Python dominates AI/ML despite being "slow".

---

## 5. JIT Compilation Explained

JIT (Just In Time) is the trick that blurs the line between compiled and interpreted.

1. The program starts running in an interpreter (or from bytecode)
2. The runtime **profiles** the code: it counts how often each function or loop runs
3. Code that runs a lot is marked as **"hot"**
4. Hot code is compiled to **optimized machine code** while the program keeps running
5. Next time that code runs, the fast machine code version is used instead

```
start  ->  interpret everything  ->  detect hot code  ->  compile hot code  ->  run fast
           (slow but instant)                            (in the background)
```

**Bonus:** a JIT can sometimes optimize _better_ than an AOT compiler, because it sees how the program actually behaves with real data (for example, which types a function always receives).

**Downside:** the "warm up" period. Programs are slow for the first moments until the hot paths get compiled. This does not matter for a server that runs for weeks, but it matters for a small command line tool that runs for 50 milliseconds.

|Runtime|Uses JIT?|
|---|---|
|JVM (Java, Kotlin, Scala)|Yes (HotSpot)|
|.NET CLR (C#)|Yes (RyuJIT)|
|V8 (JavaScript)|Yes (TurboFan)|
|PyPy (alternative Python)|Yes, often several times faster than CPython|
|CPython (standard Python)|Only an experimental JIT in recent versions, not the default|
|Julia|Yes, compiles every function on first call|

---

## 6. Transpiling

**Transpiling** (source to source compiling) means translating one high level language into another high level language, instead of into machine code or bytecode.

```
TypeScript  ->  tsc  ->  JavaScript  ->  V8 runs it
```

Examples:

- **TypeScript -> JavaScript** (the browser cannot run TypeScript directly)
- **Modern JavaScript -> older JavaScript** with Babel, for old browsers
- **Sass/SCSS -> CSS**

The type checking in TypeScript only happens during transpiling. Once it becomes JavaScript, all types are gone. That is why TypeScript can catch bugs early but adds zero runtime cost.

---

## 7. Side by Side Comparison

||Compiled (AOT)|Bytecode + VM|Interpreted / JIT|
|---|---|---|---|
|**Examples**|C, C++, Rust, Go|Java, C#, Kotlin|Python, JavaScript, PHP|
|**Output**|Native executable|Bytecode (`.class`, `.dll`)|None you see (runs from source)|
|**Execution speed**|Fastest|Fast (after warm up)|Slowest (JS is fast thanks to JIT)|
|**Startup time**|Instant|Slower (VM boots)|Fast|
|**Portability**|Recompile per platform|Runs anywhere with the VM|Runs anywhere with the interpreter|
|**When errors appear**|Compile time|Mostly compile time|Mostly runtime|
|**Dev speed**|Slower (build step)|Medium|Fastest|
|**What the user needs**|Nothing|The VM (JRE / .NET)|The interpreter|
|**Memory control**|Manual or strict (Rust)|Garbage collected|Garbage collected|

---

## 8. Common Misconception

> "Python is an interpreted language" is technically about the **implementation**, not the language itself.

A language is just a set of rules. How it runs depends on the tool you use:

|Language|Usual way|Other ways|
|---|---|---|
|Python|CPython (bytecode interpreter)|PyPy (JIT), Cython (compiles to C)|
|Java|JVM with JIT|GraalVM Native Image (AOT to a native binary)|
|JavaScript|V8 with JIT|Can be compiled ahead of time in some tools|
|C|GCC / Clang (AOT)|Interpreters exist (e.g. for teaching)|
|Many languages|Their normal compiler|Compiled to **WebAssembly** to run in browsers|

So when someone says "compiled language" they mean "a language that is _normally_ compiled".

---

## 9. Why This Decides What a Language Is Used For

The execution model is a big reason each language ended up in its niche:

- **Embedded systems and IoT (C, C++, Rust):** microcontrollers like an Arduino have tiny memory and no room for a VM or interpreter. You need small native machine code with direct hardware access
- **Game engines (C++):** every millisecond per frame counts, so maximum native speed wins
- **Enterprise backends (Java, C#):** long running servers benefit fully from JIT, and the VM gives safety, portability and garbage collection
- **Cloud tools and CLIs (Go, Rust):** compile to a single native binary with fast startup and no dependencies, easy to put in a Docker container
- **AI, data science, scripting (Python, R):** development speed and interactivity (notebooks) matter more than raw speed, and the heavy math runs in compiled C/CUDA libraries underneath
- **Web frontend (JavaScript):** browsers ship with a JS engine built in, so it is the only language that runs everywhere on the web without installation (apart from WebAssembly)

---

## 10. Summary

- CPUs only understand **machine code**; every language needs a way to get there
- **Compiled (AOT):** translate everything first, run directly. Fastest, but must recompile per platform
- **Bytecode + VM:** compile to a portable middle format, a VM runs it and JIT compiles the hot parts. Portable and fast, needs the VM
- **Interpreted:** translate while running. Easiest to develop with, slowest to run (unless there is a strong JIT like in JavaScript)
- **JIT** compiles hot code at runtime and is the reason Java, C# and JavaScript are fast
- **Transpiling** turns one high level language into another (TypeScript to JavaScript)
- Compiled vs interpreted is a property of the **implementation**, not strictly of the language

---

## 11. Self Check Questions

1. Why can't you run a Windows `.exe` compiled from C on a Mac with an M chip?
2. What file does `javac` produce, and what program runs it?
3. Python does create bytecode. So why is it still called interpreted?
4. What is the "warm up" problem of a JIT, and when does it not matter?
5. Why is Python used for machine learning when it is one of the slowest languages?
6. Why does an Arduino program use C/C++ and not Java or Python?
7. What happens to TypeScript types after transpiling?

<details> <summary>Answers</summary>

1. Machine code is specific to a CPU architecture and OS. x86 Windows instructions and file formats do not match ARM macOS. You need to recompile for that platform.
2. `.class` files containing bytecode, run by the JVM.
3. Because the bytecode is not machine code. CPython's interpreter loop still executes it instruction by instruction at runtime, with no optimizing compile to native code by default.
4. The program is slow at the start until hot code gets compiled. It does not matter for long running programs like servers.
5. The heavy computation happens inside libraries (NumPy, PyTorch) written in C, C++ and CUDA. Python only coordinates the work.
6. Microcontrollers have very little memory and no OS to host a VM or interpreter. Native compiled C/C++ is small, fast and has direct hardware access.
7. They are removed. The output is plain JavaScript; types only exist for checking during development.

</details>