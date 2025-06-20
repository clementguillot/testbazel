load("@aspect_rules_js//js:defs.bzl", "js_library", "js_run_binary", "js_run_devserver", "js_test")
load("@aspect_rules_ts//ts:defs.bzl", "ts_config", "ts_project")
load("@npm//:defs.bzl", "npm_link_all_packages")
load("@npm//apps/frontend:tsconfig-to-swcconfig/package_json.bzl", tsconfig_to_swcconfig = "bin")
load("@npm//apps/frontend:vite/package_json.bzl", vite_bin = "bin")
# load("//:vitest.bzl", "vitest")
load("//apps/frontend:defs.bzl", "ASSET_PATTERNS", "SRC_PATTERNS", "TEST_PATTERNS", "TRANSPILER")

package(default_visibility = ["//visibility:public"])

npm_link_all_packages(name = "node_modules")

RUNTIME_DEPS = [
    ":assets",
    ":srcs",
    "//apps/frontend/public",
    "package.json",
    "postcss.config.mjs",
    "tailwind.config.mjs",
    "vite.config.ts",
    "tsconfig.json",
]

NPM_DEPS = [
    ":node_modules/@tanstack/react-router",
    ":node_modules/@tanstack/react-router-devtools",
    ":node_modules/@tanstack/react-start",
    ":node_modules/autoprefixer",
    ":node_modules/react",
    ":node_modules/react-dom",
    ":node_modules/tailwindcss",
    ":node_modules/vite",
    ":node_modules/vite-tsconfig-paths",
]

ts_config(
    name = "tsconfig",
    src = "tsconfig.json",
    visibility = ["//visibility:public"],
)

tsconfig_to_swcconfig.t2s(
    name = "write_swcrc",
    srcs = ["tsconfig.json"],
    args = [
        "--filename",
        "$(location tsconfig.json)",
    ],
    stdout = ".swcrc",
    visibility = ["//apps/frontend:__subpackages__"],
)

js_library(
    name = "vite.config",
    srcs = [
        "package.json",
        "postcss.config.mjs",
        "tailwind.config.mjs",
        "tsconfig.json",
        "vite.config.ts",
    ],
    data = [
        ":node_modules/@tanstack/react-start",
        ":node_modules/autoprefixer",
        ":node_modules/tailwindcss",
        ":node_modules/vite",
        ":node_modules/vite-tsconfig-paths",
        # "//:node_modules/vitest",
    ],
    visibility = ["//apps/frontend:__subpackages__"],
)

# react_router_bin.react_router_binary(
#     name = "react_router",
# )
vite_bin.vite_binary(
    name = "vite",
    # chdir = package_name(),
    # data = RUNTIME_DEPS + NPM_DEPS
)

# Runs a development server. Run this target under ibazel to use hot reloading.
js_run_devserver(
    name = "dev",
    args = ["dev"],
    chdir = package_name(),
    data = RUNTIME_DEPS + NPM_DEPS,
    tool = ":vite",
)

js_run_binary(
    name = "build",
    srcs = RUNTIME_DEPS + NPM_DEPS,
    args = ["build"],
    chdir = package_name(),
    out_dirs = [
        ".output",
        ".tanstack",
    ],
    tool = ":vite",
)

# Runs the production server.
# react_router_serve_bin.react_router_serve_binary(
#     name = "start",
#     args = ["build/server/index.js"],
#     chdir = package_name(),
#     data = NPM_DEPS + [
#         "package.json",
#         ":build",
#     ],
# )

# Just verify that the bundle produced "something reasonable" but doesn't verify it functions in a browser.
# TODO: use something like Cypress for a true end-to-end test
# js_test(
#     name = "build_smoke_test",
#     timeout = "short",
#     data = [":build"],
#     entry_point = "build_smoke_test.js",
# )

# Produce `+types/<route file>.d.ts` files, see
# https://reactrouter.com/explanation/type-safety#typegen-command
js_run_binary(
    name = "typegen",
    srcs = RUNTIME_DEPS + NPM_DEPS,
    args = ["typegen"],
    chdir = package_name(),
    out_dirs = [
        ".vite",
    ],
    tool = ":vite",
)

js_library(
    name = "assets",
    srcs = glob(
        include = ASSET_PATTERNS,
        allow_empty = True,
    ),
    visibility = ["//apps/frontend:__subpackages__"],
)

js_library(
    name = "srcs",
    srcs = glob(
        include = SRC_PATTERNS,
        allow_empty = True,
        exclude = TEST_PATTERNS,
    ),
    visibility = ["//apps/frontend:__subpackages__"],
)

ts_project(
    name = "src",
    srcs = glob(
        include = SRC_PATTERNS,
        allow_empty = True,
        exclude = TEST_PATTERNS,
    ) + [":typegen"],
    data = [
        ":assets",
    ],
    declaration = True,
    resolve_json_module = True,
    transpiler = TRANSPILER,
    tsconfig = "//apps/frontend:tsconfig",
    visibility = ["//apps/frontend:__subpackages__"],
    deps = [
        ":node_modules/@tanstack/react-router",
        ":node_modules/@tanstack/react-router-devtools",
        ":node_modules/@tanstack/react-start",
        ":node_modules/@types/node",
        ":node_modules/@types/react",
        ":node_modules/react",
        ":node_modules/react-dom",
        ":node_modules/tailwindcss",
        ":node_modules/vite",
    ],
)

# ts_project(
#     name = "test_lib",
#     srcs = glob(
#         TEST_PATTERNS,
#         allow_empty = True,
#     ),
#     declaration = True,
#     resolve_json_module = True,
#     transpiler = TRANSPILER,
#     tsconfig = "//apps/frontend:tsconfig",
#     deps = [
#         ":src",
#         ":node_modules/@testing-library/jest-dom",
#         ":node_modules/@testing-library/react",
#         ":node_modules/@types/jest",
#         "//:node_modules/vitest",
#     ],
# )

# vitest(
#     name = "test",
#     config = "//apps/frontend:vite.config",
#     deps = [":test_lib"],
# )
