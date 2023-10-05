load("@pip_deps//:requirements.bzl", "requirement")
load("@rules_python//python:defs.bzl", "py_test")

py_test(
    name = "test_word_count",
    srcs = [":test_word_count.py"],
    deps = [
        "//word_count",
        requirement("pytest"),
        requirement("pytest-benchmark"),
    ],
)
