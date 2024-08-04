# mdBook Source of Physics-Based Simulation

This online book is developed based on [mdBook](https://github.com/rust-lang/mdBook). The book is fully open-sourced, and we welcome your contributions!

md files of each chapter of the book is in `src/`.

[Python](https://github.com/phys-sim-book/solid-sim-tutorial) and [CUDA](https://github.com/phys-sim-book/solid-sim-tutorial-gpu) implementations of certain topics are included as submodules and discussed in the book. Thus, to clone the source with submodules:
```
git clone --recurse-submodules https://github.com/phys-sim-book/mdbook-src.git
```
To update submodules:
```
git submodule update --init --recursive
```

To generate the online book from the md files:

**MacOS**

1. Download mdBook binary from https://github.com/rust-lang/mdBook/releases and put it into the root directory of this repository
2. Install rust, which may take a while. On Mac: `brew install rust`
3. Install [mdbook-katex](https://github.com/lzanini/mdbook-katex) plugin via `cargo install mdbook-katex`
4. Install [mdbook-numeq](https://github.com/yannickseurin/mdbook-numeq) plugin via `cargo install mdbook-numeq`
5. Install [mdbook-numthm](https://github.com/yannickseurin/mdbook-numthm) plugin via `cargo install mdbook-numthm`
6. Install [mdbook-bib](https://github.com/francisco-perez-sorrosal/mdbook-bib) plugin via `cargo install mdbook-bib`
7. Add cargo path to PATH via `export PATH="/Users/your-user-name/.cargo/bin:$PATH"`
8. Init, build, and view the online book following https://rust-lang.github.io/mdBook/cli/index.html

**Windows**

1. Download mdBook binary from https://github.com/rust-lang/mdBook/releases and put `mdbook.exe` into the root directory of this repository
2. Install [rust](https://www.rust-lang.org/tools/install) using rustup binary installer
3. Install [mdbook-katex](https://github.com/lzanini/mdbook-katex) plugin following their README's Windows users section. Put `mdbook-katex.exe` into the root directory of this repository.
4. Install [mdbook-numeq](https://github.com/yannickseurin/mdbook-numeq) plugin via `cargo install mdbook-numeq`
5. Install [mdbook-numthm](https://github.com/yannickseurin/mdbook-numthm) plugin via `cargo install mdbook-numthm`
6. Install [mdbook-bib](https://github.com/francisco-perez-sorrosal/mdbook-bib) plugin via `cargo install mdbook-bib`
7. Init, build, and view the online book following https://rust-lang.github.io/mdBook/cli/index.html. For example, you can simply do in a windows cmd: `mdbook.exe serve --open`

More useful tools for enhancing the book: https://github.com/rust-lang/mdBook/wiki
