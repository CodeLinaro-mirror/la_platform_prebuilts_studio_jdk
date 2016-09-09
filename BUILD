package(default_visibility = ["//visibility:public"])

filegroup(
    name = "jdk",
    srcs = glob(["**"]),
)

# Nothing should depend on this library on windows.
cc_library(
    name = "jni_headers",
    srcs = [],
    hdrs = select({
        "//tools/base/bazel:darwin": glob(["mac/Contents/Home/include/**/*.h"]),
        "//conditions:default": glob(["linux/include/**/*.h"]),
    }),
    includes = select({
        "//tools/base/bazel:darwin": [
            "mac/Contents/Home/include",
            "mac/Contents/Home/include/darwin",
        ],
        "//conditions:default": [
            "linux/include",
            "linux/include/linux",
        ]
    })
)