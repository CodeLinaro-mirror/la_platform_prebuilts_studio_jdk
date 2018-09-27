package(default_visibility = ["//visibility:public"])

filegroup(
    name = "jdk",
    srcs = glob(["**"]),
)

filegroup(
    name = "langtools",
    srcs = select({
        "//tools/base/bazel:darwin": [
            "mac/Contents/Home/jre/lib/jce.jar",
            "mac/Contents/Home/lib/tools.jar",
        ],
        "//tools/base/bazel:windows": [
            "win64/jre/lib/jce.jar",
            "win64/lib/tools.jar",
        ],
        "//conditions:default": [
            "linux/jre/lib/jce.jar",
            "linux/lib/tools.jar",
        ],
    }),
)

filegroup(
    name = "bootclasspath",
    srcs = select({
        "//tools/base/bazel:darwin": glob([
            "mac/Contents/Home/jre/lib/*.jar",
            "mac/Contents/Home/jre/lib/ext/*.jar",
        ]),
        "//tools/base/bazel:windows": glob([
            "win32/jre/lib/*.jar",
            "win32/jre/lib/ext/*.jar",
        ]),
        "//conditions:default": glob([
            "linux/jre/lib/*.jar",
            "linux/jre/lib/ext/*.jar",
        ]),
    }),
)

# Nothing should depend on this library on windows.
cc_library(
    name = "jni_headers",
    srcs = [],
    hdrs = select({
        "//tools/base/bazel:darwin": glob(["mac/Contents/Home/include/**/*.h"]),
        "//tools/base/bazel:android_cpu_x86": [],
        "//tools/base/bazel:android_cpu_x86_64": [],
        "//tools/base/bazel:android_cpu_arm": [],
        "//tools/base/bazel:android_cpu_arm_64": [],
        "//conditions:default": glob(["linux/include/**/*.h"]),
    }),
    includes = select({
        "//tools/base/bazel:darwin": [
            "mac/Contents/Home/include",
            "mac/Contents/Home/include/darwin",
        ],
        "//tools/base/bazel:android_cpu_x86": [],
        "//tools/base/bazel:android_cpu_x86_64": [],
        "//tools/base/bazel:android_cpu_arm": [],
        "//tools/base/bazel:android_cpu_arm_64": [],
        "//conditions:default": [
            "linux/include",
            "linux/include/linux",
        ],
    }),
    deps = select({
        "//tools/base/bazel:android_cpu_x86": ["//tools/vendor/google/android-ndk:jvmti"],
        "//tools/base/bazel:android_cpu_x86_64": ["//tools/vendor/google/android-ndk:jvmti"],
        "//tools/base/bazel:android_cpu_arm": ["//tools/vendor/google/android-ndk:jvmti"],
        "//tools/base/bazel:android_cpu_arm_64": ["//tools/vendor/google/android-ndk:jvmti"],
        "//conditions:default": [],
    }),
)

java_runtime(
    name = "jdk_runtime",
    srcs = select({
        "//tools/base/bazel:darwin": glob(["mac/**"]),
        "//tools/base/bazel:windows": glob(["win64/**"]),
        "//conditions:default": glob(["linux/**"]),
    }),
    java_home = select({
        "//tools/base/bazel:darwin": "mac/Contents/Home",
        "//tools/base/bazel:windows": "win64",
        "//conditions:default": "linux",
    }),
)
