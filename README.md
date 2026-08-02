## libc++

LLVM libc++, specifically for Android, removing exception and RTTI support.

Source code is extracted from both the AOSP `llvm-project` repository.

### Minimal Header-Only Architecture

Magisk uses a minimal build setup for libc++ to keep native binary sizes small and compilation fast:

- **Header-Only STL:** Standard template features (`std::vector`, `std::map`, `std::span`, `std::unique_ptr`, `std::atomic`, `std::function`, etc.) are included header-only from `include/`.
- **`abi.cpp`:** A single 100% self-contained ABI source file that replaces `libc++abi` and `libc++` runtime libraries, providing:
  - Weak global `operator new` and `operator delete` overloads
  - Itanium C++ ABI runtime stubs (`__cxa_guard_*`, `__cxa_pure_virtual`, `__cxa_thread_atexit`)
  - Core STL abort/throw helpers (`std::terminate()`, `std::__throw_runtime_error()`, `std::__throw_system_error()`, etc.)
  - Explicit template instantiations for `std::basic_string<char>` methods (required by libc++'s `extern template` declarations) and string conversion functions (`std::to_string`)
- **Exported Flags:** `Android.mk` automatically exports `-fno-exceptions` and `-fno-rtti` to all dependent modules via `LOCAL_EXPORT_CFLAGS` and `LOCAL_EXPORT_CPPFLAGS`.
