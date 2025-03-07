load("@bazel_skylib//:bzl_library.bzl", "bzl_library")
load("//:version.bzl", "MAX_VERSION", "MIN_VERSION")
load("@bazel_gazelle//:def.bzl", "gazelle")
load("@hedron_compile_commands//:refresh_compile_commands.bzl", "refresh_compile_commands")
load("@io_bazel_rules_go//go:def.bzl", "nogo")

package(default_visibility = ["//visibility:private"])

exports_files(glob(["*"]))

filegroup(
    name = "nothing",
    visibility = ["//visibility:public"],
)

filegroup(
    name = "clang_tidy_config",
    srcs = [".clang-tidy"],
    visibility = ["//visibility:public"],
)

sh_test(
    name = "check_bazel_versions",
    srcs = ["//tools:check_bazel_versions.sh"],
    args = [
        MIN_VERSION,
        MAX_VERSION,
    ],
    data = [
        ".bazelminversion",
        ".bazelversion",
    ],
)

nogo(
    name = "my_nogo",
    vet = True,
    visibility = ["//visibility:public"],
)

# gazelle:build_file_name BUILD
# gazelle:go_naming_convention import
# gazelle:proto file
# gazelle:exclude **/examples/
# gazelle:exclude **/testdata/
# gazelle:exclude **/*_go_proto/
# gazelle:prefix kythe.io
# gazelle:map_kind go_binary go_binary //tools:build_rules/shims.bzl
# gazelle:map_kind go_library go_library //tools:build_rules/shims.bzl
# gazelle:map_kind go_test go_test //tools:build_rules/shims.bzl
gazelle(
    name = "gazelle",
    gazelle = "//tools/gazelle",
)

gazelle(
    name = "gazelle-update-repos",
    args = [
        "-from_file=go.mod",
        "-to_macro=external.bzl%_go_dependencies",
        "-prune",
    ],
    command = "update-repos",
)

bzl_library(
    name = "external_bzl",
    srcs = ["external.bzl"],
)

bzl_library(
    name = "version_bzl",
    srcs = ["version.bzl"],
)

bzl_library(
    name = "visibility_bzl",
    srcs = ["visibility.bzl"],
)

bzl_library(
    name = "setup_bzl",
    srcs = ["setup.bzl"],
)

refresh_compile_commands(
    name = "refresh_compile_commands",
    # Gathering header compile commands fails due to mismatched clang versions with RBE.
    exclude_headers = "all",
    # If clangd is complaining about missing headers (and all that goes along with it),
    # and you're using remote builds, rebuild with --remote_download_outputs=all
    # With layering_check enabled, clangd warns about missing dependencies on standard library headers.
    targets = {"//kythe/cxx/...": "--config=compile-commands"},
)

load("@npm//:defs.bzl", "npm_link_all_packages")

npm_link_all_packages(name = "node_modules")
