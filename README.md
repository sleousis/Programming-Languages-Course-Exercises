# Programming Languages II

Exercises for the Programming Languages II course at NTUA (National Technical University of Athens), written in 2019-2020. They cover Haskell programming, property testing with QuickCheck, a bytecode stack machine with a garbage collector, type inference, denotational semantics and a small web client. Each folder holds one exercise with its code and the assignment or report PDF.

## Contents

| Folder | Topic | Language |
| --- | --- | --- |
| `Ex 1` | Count palindromic subsequences of a string (mod 20130401), pure and impure (ST array) versions | Haskell |
| `Ex 2-3` | Rose trees with `foldTree`, QuickCheck properties, the Bird tree of rationals | Haskell + QuickCheck |
| `Ex 4` | Stack-based virtual machine with threaded code, plus an assembler | C++ (GCC), Python 3 |
| `Ex 5` | The same VM extended with cons cells and a mark-and-sweep garbage collector | C++ (GCC) |
| `Ex 6` | Type inference for the simply typed lambda calculus (constraints and unification) | Haskell |
| `Ex 7` | Interpreter from denotational semantics for a small imperative language with lists | Haskell |
| `Ex 8` | Type systems (theory only, PDF) | none |
| `Ex 9` | Client that plays a "least deletions to get a palindrome" web quiz, plus a PHP server for it | Python 3, PHP |

## Download

The [Releases page](https://github.com/sleousis/Programming-Languages-Course-Exercises/releases) has prebuilt programs for Ex 1, Ex 2-3, Ex 4, Ex 5, Ex 6 and Ex 7:

- `pl-exercises-<version>-linux-x86_64.tar.gz` for 64-bit Linux. The Haskell programs are static and the C++ ones need glibc 2.28 or newer.
- `pl-exercises-<version>-windows-x86_64.zip` for 64-bit Windows.

Each archive has one folder per exercise with the program and its sample input. Unpack it and follow `HOW-TO-RUN.txt`. For example, run `./ex4 test.b` in the `ex4` folder, or `ask7.exe < test1` in the `ex7` folder on Windows. The sample inputs of Ex 1 and Ex 6 are also in the repository as `input.txt`.

## Tech stack

Tested versions (the latest stable releases in September 2026):

| Tool | Version |
| --- | --- |
| GHC | 9.14.1 |
| cabal-install | 3.18.1.0 |
| QuickCheck | 2.19.0.0 |
| GCC (g++) | 16.2.0, with `-std=c++23` |
| Python | 3.14.7 |
| requests / beautifulsoup4 | 2.34.2 / 4.15.0 (all pins in `Ex 9/requirements.txt`) |
| PHP | 8.5.11 |

`ex4.cpp` and `ex5.cpp` use the GCC "labels as values" extension, so they need GCC or Clang. MSVC will not compile them.

## Repository layout

```
Ex 1/     ask1_pure.hs, ask1_impure.hs, input.txt, haskell.pdf
Ex 2-3/   ask2-3.hs, haskell23.pdf
Ex 4/     ex4.cpp, assembler.py, test.asb, test.b, vm.pdf, README.txt
Ex 5/     ex5.cpp, pp.b, gc.pdf, README.txt
Ex 6/     ask6.hs, input.txt, read-typeinfer.hs (course-provided parser), typeinfer.pdf
Ex 7/     ask7.hs, test1, test2, densem.hs (course-provided), densem-syntax.hs (draft), PDFs, README.txt
Ex 8/     PDFs only
Ex 9/     client.py, palseq.php, requirements.txt, script.pdf, README.txt
```

The `README.txt` files inside `Ex 4`, `Ex 5`, `Ex 7` and `Ex 9` are the original per-exercise notes.

## Prerequisites

The Haskell and C++ parts were run on Linux (Ubuntu 20.04 under WSL). The Python parts and the PHP server were run on both Linux and Windows. Distribution packages on older systems are too old, so the tools came from these sources:

- GHC and cabal from [ghcup](https://www.haskell.org/ghcup/):

  ```
  ghcup install ghc 9.14.1 --set
  ghcup install cabal 3.18.1.0 --set
  cabal update
  ```

- GCC 16.2 from conda-forge, for example with micromamba:

  ```
  micromamba create -n gcc16 -c conda-forge gxx_linux-64=16.2.0
  ```

  The compiler is then called `x86_64-conda-linux-gnu-g++`. Any GCC 16 `g++` works the same way. The commands below just say `g++`.

- Python 3.14.7 from [uv](https://docs.astral.sh/uv/) (`uv python install 3.14.7`) or python.org.
- PHP 8.5.11. On Windows, use the official zip from windows.php.net. On Linux, it was built from the php.net source tarball with `./configure --disable-all`. The quiz page needs no extensions.

## Build and run

Run all commands from the folder of the exercise. Compiled programs and GHC environment files are ignored by git.

### Ex 1

The input is the length of the string on one line and the string on the next.

```
ghc -O ask1_pure.hs
ghc -O ask1_impure.hs
printf '5\nabcba\n' | ./ask1_pure
printf '5\nabcba\n' | ./ask1_impure
```

Both print `13`. GHC 9.14 prints `-Wx-partial` warnings about `head` and `tail` for `ask1_pure.hs`. They are only warnings.

### Ex 2-3

Install QuickCheck into a package environment in the folder first. GHC picks it up from there automatically.

```
cabal install --lib QuickCheck-2.19.0.0 --package-env .
ghc -O ask2-3.hs
./ask2-3
```

It runs QuickCheck on every tree and Bird tree property. All 12 checks print `+++ OK, passed 100 tests.`

### Ex 4

```
python3 assembler.py test.asb test.b
g++ -std=c++23 -O2 ex4.cpp -o ex4
./ex4 test.b
```

Output:

```
Hello world!
*****************
0.000181
```

The last line is the elapsed time, so it varies. The assembler also runs on Windows (`python assembler.py test.asb test.b`). It produces a file identical to the committed `test.b`.

### Ex 5

`pp.b` is the ping-pong test program from `Ex 5/README.txt` (decoded from its base64 block).

```
g++ -std=c++23 -O2 ex5.cpp -o ex5
./ex5 pp.b
```

It prints lines of dots, each ending in `$`, and finally the elapsed time. It took 32 to 48 seconds on the test machine. `Ex 5/README.txt` says `./ex5 test.b`, but the program file is `pp.b`.

### Ex 6

The input is the number of terms, then one closed lambda term per line, fully parenthesized.

```
ghc -O ask6.hs
printf '4\n(\\x. x)\n(\\x. (\\y. (x y)))\n(\\x. (x x))\n(\\f. (\\x. (f (f x))))\n' | ./ask6
```

Output:

```
@0 -> @0
(@0 -> @1) -> @0 -> @1
type error
(@0 -> @0) -> @0 -> @0
```

Terms with free variables are not supported. They stop the program with a `Map.!` error.

### Ex 7

```
runhaskell ask7.hs < test1
runhaskell ask7.hs < test2
```

`test1` prints `42`. `test2` prints `1 : 2 : 3 : 4 : 5 : 6 : 7 : false`.

`densem.hs` is the course-provided example and has no `main`. Run its examples with `ghc -e 'run ex1' densem.hs` (prints `42`).

### Ex 9

Start the server from the `Ex 9` folder with the PHP built-in server. It works in place of XAMPP:

```
php -d output_buffering=4096 -S 127.0.0.1:8099 -t .
```

`palseq.php` sets cookies after it has printed HTML. That only works with output buffering on. XAMPP and the recommended `php.ini` files turn it on (`output_buffering = 4096`). A bare PHP without a `php.ini` does not, and then every request prints "Cannot modify header information" and the quiz never advances.

In another terminal, set up the client in the `Ex 9` folder and run it.

Linux:

```
python3.14 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/python client.py http://127.0.0.1:8099/palseq.php
```

Windows (PowerShell), with `python` being Python 3.14:

```
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
.venv\Scripts\python client.py http://127.0.0.1:8099/palseq.php
```

The client answers ten rounds and ends with `Congratulations! You passed the quiz! :-)`. The server keeps its state in cookies of the client session, so every run starts from round 1.

## Notes and known limitations

- The code was first written for GHC 8.x, an older g++ and Python 3 of 2019. The only changes needed for the current versions were a Python 3 `print` fix in `Ex 4/assembler.py` and, in `Ex 5/ex5.cpp`, `using std::swap;` in place of `using namespace std;`. From C++17 on, `std::size` clashes with the global `size` variable.
- `Ex 7/densem-syntax.hs` is an unfinished draft and does not compile (the type `S` is not defined). It is kept as it was.
- The PDF files are the assignment texts and reports. Some of them are in Greek.
- The per-exercise notes run Ex 9 on XAMPP. That setup was not tested. The PHP built-in server was.

## Author

Savvas Leousis
