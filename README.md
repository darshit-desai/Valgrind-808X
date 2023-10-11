# Valgrind Exercise

## Standard install via command-line
```bash
# Configure the project and generate a native build system:
  # Must re-run this command whenever any CMakeLists.txt file has been changed.
  cmake -S ./ -B build/
# Compile and build the project:
  # rebuild only files that are modified since the last build
  cmake --build build/
  # or rebuild everything from scracth
  cmake --build build/ --clean-first
  # to see verbose output, do:
  cmake --build build/ --verbose
# Run program:
  ./build/app/shell-app
# Clean
  cmake --build build/ --target clean
# Clean and start over:
  rm -rf build/
```

## To refer the valgrind outputs

Navigate to the root after cloning the repo

```
cd ValgrindOutputs
```
owner:darshit-desai 
## Corrected bugs

* The `main.cpp` file has a boolean variable which was declared but was not initialized, hence it was giving an error
* The `AnalogSensor.cpp` file has a class method called `Read()` which initializes a raw point using the `new` method but never deletes it resulting in that pointer becoming a dangling pointer. To avoid this I changed the pointer type to a smart point variable, specifically a `unique_ptr` which is part of the `#include <memory>` header file and which doesn't require explicit deletion at the end of the code.

## Valgrind run instructions

The valgrind can be run after building the code as shown above:

```
valgrind --leak-check=full --track-origins=yes ./build/app/shell-app > output_correctedCode_staticCommented.txt 2>&1
```

## Extra Credit

* What happens when the executable is linked statically?  Does Valgrind still detect those same bugs?
  * The answer to the first and second part of the question is that, No it doesn't detect those same bugs, the same can be checked in the `ValgrindOutputs/output_UncorrectedCode_staticNotCommented.txt`. It does detect errors which are irrelevant to our code but at the end it shows that the heap blocks were freed. Whereas this is not actually the case, the Heap was still occupied by the dangling raw pointer. The reason why this happens is because of the static linking of the executable.

* Why this happens?
  * When building under a `--static` flag the CMake build tries to incorporate all the code from the libraries you use, which leads to larger executable files and would occupy greater memory and the runtime behavior of the executable might also change as it doesn't depend on shared libraries. This increase in size of the executable doesn't go in favor with the valgrind. This is because Valgrind is specifically designed to work with **dynamically-linked** programs, and when you feed a statically linked executable for analysing the memory behavior it might not capture those memory leaks and errors.
  Secondly if you got to the [Valgrind documentation FAQs](https://valgrind.org/docs/manual/faq.html#faq.hiddenbug) the  website itself says that some of the conventional functions which are statically linked like `malloc` won't be detected by Valgrind tools.

