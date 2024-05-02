## Ubuntu 24.04 Noble Numbat

#### Patches Required

- Python has been updated to version 3.12.
  `distutils` is no longer available and is causing the build to fail.
  `llvm-project/lldb/bindings/python/prepare_binding_python.py` file needs patching.
