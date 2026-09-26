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

## Tech stack

- GHC with the `QuickCheck` package
- g++ (GCC). `ex4.cpp` and `ex5.cpp` use the GCC "labels as values" extension, so they need GCC or Clang. MSVC will not compile them.
- Python 3 (`requests` and `beautifulsoup4` for Ex 9)
- PHP for the Ex 9 server

## Repository layout

```
Ex 1/     ask1_pure.hs, ask1_impure.hs, haskell.pdf
Ex 2-3/   ask2-3.hs, haskell23.pdf
Ex 4/     ex4.cpp, assembler.py, test.asb, test.b, vm.pdf, README.txt
Ex 5/     ex5.cpp, pp.b, gc.pdf, README.txt
Ex 6/     ask6.hs, read-typeinfer.hs (course-provided parser), typeinfer.pdf
Ex 7/     ask7.hs, test1, test2, densem.hs (course-provided), densem-syntax.hs (draft), PDFs, README.txt
Ex 8/     PDFs only
Ex 9/     client.py, palseq.php, requirements.txt, script.pdf, README.txt
```

The `README.txt` files inside `Ex 4`, `Ex 5`, `Ex 7` and `Ex 9` are the original per-exercise notes.

## Prerequisites

Linux (tested on Ubuntu 20.04 under WSL):

```
sudo apt-get install ghc libghc-quickcheck2-dev g++ python3 python3-venv php-cli
```

Windows: the Haskell and C++ parts are easiest to run inside WSL with the packages above. The Ex 4 assembler and the Ex 9 client also run on Windows Python 3.

## Build and run

Run all commands from the folder of the exercise. Compiled programs are ignored by git.

### Ex 1

The input is the length of the string on one line and the string on the next.

```
ghc -O ask1_pure.hs
ghc -O ask1_impure.hs
printf '5\nabcba\n' | ./ask1_pure
printf '5\nabcba\n' | ./ask1_impure
```

Both print `13`.

### Ex 2-3

```
ghc -O ask2-3.hs
./ask2-3
```

It runs QuickCheck on every tree and Bird tree property. Every check prints `+++ OK, passed 100 tests.`

### Ex 4

```
python3 assembler.py test.asb test.b
g++ ex4.cpp -o ex4
./ex4 test.b
```

Output:

```
Hello world!
*****************
0.000238
```

The last line is the elapsed time, so it varies. The assembler also runs on Windows (`py assembler.py test.asb test.b`). It produces a file identical to the committed `test.b`.

### Ex 5

`pp.b` is the ping-pong test program from `Ex 5/README.txt` (decoded from its base64 block).

```
g++ ex5.cpp -o ex5
./ex5 pp.b
```

It prints lines of dots, each ending in `$`, and finally the elapsed time. It took 36 to 48 seconds on the test machine. `Ex 5/README.txt` says `./ex5 test.b`, but the program file is `pp.b`.

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

Start the server from the `Ex 9` folder. The PHP built-in server works in place of XAMPP:

```
php -S 127.0.0.1:8099 -t .
```

In another terminal, set up the client in the `Ex 9` folder and run it.

Linux:

```
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/python client.py http://127.0.0.1:8099/palseq.php
```

Windows (PowerShell):

```
py -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
.venv\Scripts\python client.py http://127.0.0.1:8099/palseq.php
```

The client answers ten rounds and ends with `Congratulations! You passed the quiz! :-)`. The server keeps its state in cookies of the client session, so every run starts from round 1.

## Notes and known limitations

- Tested with GHC 8.6.5, g++ 9.4, Python 3.8 (WSL) and 3.9 (Windows) and PHP 7.4. Newer versions were not tested.
- `Ex 7/densem-syntax.hs` is an unfinished draft and does not compile (the type `S` is not defined). It is kept as it was.
- The PDF files are the assignment texts and reports. Some of them are in Greek.
- The per-exercise notes run Ex 9 on XAMPP. That setup was not tested. The PHP built-in server was.

## Author

Savvas Leousis
