load("@rules_rust//rust:defs.bzl", "rust_binary")

rust_binary(
    name = "server",
    srcs = ["src/main.rs"],
    deps = [
        # "//libs/hello_lib"
        "@crates//:axum",
        "@crates//:tokio",
        "@crates//:tower",
    ],
)
