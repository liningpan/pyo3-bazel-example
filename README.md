# Example workspace for building python extensions with Rust's PyO3 package

This repository shows how to compile and run `word_count` example from PyO3 with Bazel.

## Compiling PyO3 with correct Python configuration

Using the default setting, PyO3 will discover the Python toolchain provided by the OS, even
with a conda environment (at least on MacOS). This repo shows a way to use a hermetic Python
toolchain through Bazel.

We register a hermetic Python 3.11 toolchain.

```starlark
python_register_toolchains(
    name = "python_3_11",
    python_version = "3.11",
)
```

When configuring the `pyo3` crate through `crate_universe`, we can tell the build script of
`pyo3-build-config` where to locate the Python interpreter by setting the `PYO3_PYTHON` env
variable as described here <https://pyo3.rs/v0.19.2/building_and_distribution#configuring-the-python-version>.
We can achieve this by adding the following annotation to provide the Python runtime to
the build script.

```starlark
annotations = {
    "pyo3-build-config": [crate.annotation(
        build_script_data = [
            "@python_3_11//:files",
            "@python_3_11//:python3",
        ],
        build_script_env = {
            "PYO3_PYTHON": "$(execpath @python_3_11//:python3)",
        },
    )],
},
```

We can verify that PyO3 detected the environment correctly by looking at the config file
produced by the build script in the `OUT_DIR`.
`bazel-out/darwin_arm64-opt-exec-(...)/bin/external/crates_vendor__pyo3-build-config-0.19.2/pyo3-build-config_build_script.out_dir/pyo3-build-config.txt`

```text
implementation=CPython
version=3.11
shared=true
abi3=false
lib_name=python3.11
lib_dir=/install/lib
executable=/private/var/tmp/_bazel_(...)/sandbox/darwin-sandbox/45/execroot/pyo3-bazel-example/external/python_3_11_aarch64-apple-darwin/bin/python3
pointer_width=64
build_flags=
suppress_build_script_link_lines=false
```

PyO3 should use the hermetic Python interpreter provided by Bazel, instead of an interpreter
provided by the OS.

## Using the shared library as a Python module

The shared library produced by Rust rules need to be renamed and potentially moved to a place
where Python would expect to find it. In the original example, the extension module is a
submodule under a python package with the same name. To achieve this, we rename the shared
library to `work_count.so`, place it inside `word_count`, and add as a data dependency for the 
python library.

```starlark
# word_count/BUILD
copy_file(
    name = "word_count_so",
    src = "//:word_count_rs",
    out = "word_count.so",
    allow_symlink = True,
)

py_library(
    name = "word_count",
    srcs = ["__init__.py"],
    data = ["word_count_so"],
    visibility = ["//visibility:public"],
)
```

The original example also has a pytest benchmark. We use this to make sure that everything is
working. The output can be found here `bazel-testlogs/tests/test_word_count/test.log`

```sh
bazel test -c opt //tests:test_word_count
```
