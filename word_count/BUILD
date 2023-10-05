load("@aspect_bazel_lib//lib:copy_file.bzl", "copy_file")
load("@rules_python//python:defs.bzl", "py_library")

# rename and copy/link the dynamic library to the place where
# maturin would place it
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
