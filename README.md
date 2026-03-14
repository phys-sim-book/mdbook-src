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

1. Install rust, which may take a while, via `brew install rust`
2. Install [mdbook](https://github.com/rust-lang/mdBook) via `cargo install mdbook --version 0.4.52 --force`
3. Install [mdbook-katex](https://github.com/lzanini/mdbook-katex) plugin via `cargo install mdbook-katex --version 0.9.4 --force`
4. Install [mdbook-numeq](https://github.com/yannickseurin/mdbook-numeq) plugin via `cargo install mdbook-numeq --version 0.4.0 --force`
5. Install [mdbook-numthm](https://github.com/yannickseurin/mdbook-numthm) plugin via `cargo install mdbook-numthm --version 0.2.0 --force`
6. Install [mdbook-bib](https://github.com/francisco-perez-sorrosal/mdbook-bib) plugin via `cargo install mdbook-bib --version 0.0.6 --force`
7. Add cargo path to PATH via `export PATH="/Users/your-user-name/.cargo/bin:$PATH"`
8. Init, build, and view the online book following https://rust-lang.github.io/mdBook/cli/index.html

**Windows**

1. Install Rust with rustup from https://www.rust-lang.org/tools/install. On Windows, rustup installs tools into `%USERPROFILE%\.cargo\bin`, which is also where `cargo install` puts binaries.
2. Open a new PowerShell or cmd window so the updated PATH takes effect.
3. Install mdBook via `cargo install mdbook --version 0.4.52 --force`
4. Install mdbook-katex v0.9.4 by downloading the Windows release binary (`x86_64-pc-windows-gnu.zip`) from the mdbook-katex Releases page, then put `mdbook-katex.exe` in a directory on your PATH. A simple choice is `%USERPROFILE%\.cargo\bin`.
5. Install mdbook-numeq via `cargo install mdbook-numeq --version 0.4.0 --force`
6. Install mdbook-numthm via `cargo install mdbook-numthm --version 0.2.0 --force`
7. Install mdbook-bib via `cargo install mdbook-bib --version 0.0.6 --force`
8. Init, build, and view the online book following https://rust-lang.github.io/mdBook/cli/index.html.

More useful tools for enhancing the book: https://github.com/rust-lang/mdBook/wiki
