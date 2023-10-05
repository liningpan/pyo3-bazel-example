load("@rules_rust//crate_universe:defs.bzl", "crate", "crates_vendor")

crates_vendor(
    name = "crates_vendor",
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
    cargo_lockfile = "//:Cargo.lock",
    manifests = [
        "//:Cargo.toml",
    ],
    mode = "remote",
    supported_platform_triples = [
        "aarch64-unknown-linux-gnu",
        "x86_64-unknown-linux-gnu",
        "x86_64-apple-darwin",
        "aarch64-apple-darwin",
    ],
    tags = ["manual"],
    vendor_path = "crates",
)
