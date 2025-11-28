# Solver code-generation

Currently only **Fatrop** code-generation is supported. This is what was used in the paper implementation.

## Setup

Install the following libraries from source:
- **Fatrop**: Tested with `v0.0.4`.
- **Blasfeo**: Tested with `0.1.4.1`.

Make sure the local **Blasfeo** include directory is correct in `CMakeLists.txt`.

## Usage

1. **Generate C code**: In `main.py`, set `compile_solver=True`. This will create `solver_function.c` in the project root.
2. **Compile the solver**: Move `solver_function.c` into this directory, and compile it from the `/build` folder (this will take a while):
      ```bash
      mkdir build && cd build
      cmake ..
      make
      ```
      After compilation, the shared library will be generated: `libsolver_function.so`.
3. **Run with the compiled solver**: Move the shared library to the `/lib` folder. Run `main.py`, setting `load_compiled_solver="libsolver_function.so"` or whatever you renamed it to.
4. **Hardware deployment**: The shared library can be loaded in C++ with `casadi::external`. The input parameters need to be concatenated using the same order as defined here in the OCP.
