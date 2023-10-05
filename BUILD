load("@rules_python//python:pip.bzl", "compile_pip_requirements")
load("@rules_rust//rust:defs.bzl", "rust_shared_library")
load("//3rdparty/crates:defs.bzl", "all_crate_deps")

compile_pip_requirements(
    name = "requirements",
)

exports_files(
    [
        "Cargo.lock",
        "Cargo.toml",
    ],
    visibility = ["//3rdparty:__pkg__"],
)

rust_shared_library(
    name = "word_count_rs",
    srcs = glob(["src/**/*.rs"]),
    visibility = ["//word_count:__pkg__"],
    deps = all_crate_deps(normal = True),
)
