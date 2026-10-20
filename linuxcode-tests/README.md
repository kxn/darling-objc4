# Custom deallocation initiation regression fixture

This standalone fixture accompanies the source port of Apple's objc4
`_class_setCustomDeallocInitiation` API. The API implementation, deallocation
message dispatch and autorelease exclusion follow Apple's published runtime
sources. The policy uses the previously unused class_rw_t flag bit 24 in this
older runtime; no object/class/cache layout is enlarged.

Compile as Objective-C without ARC, link libobjc and libSystem, then run with:

```
./test /path/suffix/of/the/source/libobjc.A.dylib 0 normal
OBJC_DISABLE_NONPOINTER_ISA=YES ./test /path/suffix/of/the/source/libobjc.A.dylib 1 normal
```

The suffix argument verifies the loaded API provider with dladdr. The second
argument requires an actual nonpointer (0) or raw (1) ISA. The final argument
may also be `wrong-count` (expected exit 15) or `weak-fatal` (expected runtime
abort when forming a new weak reference to a deallocating object).

Normal runs check preexisting and future static/dynamic subclasses, ordinary
unmarked destruction, last release on a worker thread with final destruction
on the caller thread, retained weak loads, repeated retain/release during
deferred destruction, and draining autorelease pools after actual destruction.
The Linux integration repository contains the headless Darling runner and
pinned build/environment reports. This fixture does not establish all AppKit
compatibility or a successful complete iOS application build.

Apple reference: https://github.com/apple-oss-distributions/objc4/tree/main/runtime
