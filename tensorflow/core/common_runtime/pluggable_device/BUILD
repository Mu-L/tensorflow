load("//tensorflow:tensorflow.bzl", "tf_cc_test", "tf_copts")
load("//tensorflow:tensorflow.default.bzl", "filegroup")
load("//tensorflow/core/platform:build_config_root.bzl", "if_static")
load("//tensorflow/core/platform:rules_cc.bzl", "cc_library")

package(
    # copybara:uncomment default_applicable_licenses = ["//tensorflow:license"],
    default_visibility = [
        "//tensorflow:internal",
    ],
    licenses = ["notice"],
)

filegroup(
    name = "pluggable_device_runtime_headers",
    srcs = [
        "pluggable_device.h",
        "pluggable_device_bfc_allocator.h",
        "pluggable_device_context.h",
        "pluggable_device_factory.h",
        "pluggable_device_init.h",
        "pluggable_device_process_state.h",
        "pluggable_device_simple_allocator.h",
        "pluggable_device_util.h",
        "//tensorflow/core/common_runtime/device:device_runtime_headers",
    ],
    visibility = ["//visibility:private"],
)

cc_library(
    name = "pluggable_device_runtime_impl",
    srcs = [
        "pluggable_device.cc",
        "pluggable_device_context.cc",
        "pluggable_device_factory.cc",
        "pluggable_device_process_state.cc",
        "pluggable_device_util.cc",
    ],
    hdrs = [":pluggable_device_runtime_headers"],
    copts = tf_copts(),
    deps = [
        ":pluggable_device_bfc_allocator",
        ":pluggable_device_init_impl",
        ":pluggable_device_simple_allocator",
        "//tensorflow/c/experimental/stream_executor",
        "//tensorflow/c/experimental/stream_executor:stream_executor_internal",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:core_cpu_lib",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/common_runtime:device_id_utils",
        "//tensorflow/core/common_runtime/device:device_event_mgr",
        "//tensorflow/core/platform:stream_executor",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@local_xla//xla/stream_executor:device_memory",
        "@local_xla//xla/stream_executor:stream_executor_h",
        "@local_xla//xla/stream_executor/integrations:device_mem_allocator",
        "@local_xla//xla/stream_executor/integrations:stream_executor_allocator",
        "@local_xla//xla/tsl/framework:device_id_utils",
    ],
    alwayslink = 1,
)

cc_library(
    name = "pluggable_device_plugin_init",
    srcs = [
        "pluggable_device_plugin_init.cc",
    ],
    hdrs = [
        "pluggable_device_plugin_init.h",
        ":pluggable_device_runtime_headers",
    ],
    copts = tf_copts(),
    visibility = ["//visibility:public"],
    deps = [
        ":pluggable_device_runtime_impl",
        "//tensorflow/c/experimental/grappler",
        "//tensorflow/c/experimental/pluggable_profiler",
        "//tensorflow/c/experimental/pluggable_profiler:pluggable_profiler_internal",
        "//tensorflow/c/experimental/stream_executor",
        "//tensorflow/c/experimental/stream_executor:stream_executor_internal",
        "//tensorflow/compiler/jit:flags_headers",
        "//tensorflow/compiler/jit:pjrt_device_context",
        "//tensorflow/compiler/jit:xla_device_no_jit_rewrite_registration",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/common_runtime:bfc_allocator",
        "//tensorflow/core/common_runtime:dma_helper",
        "//tensorflow/core/common_runtime:local_device",
        "//tensorflow/core/common_runtime:process_state",
        "//tensorflow/core/common_runtime:shared_counter",
        "//tensorflow/core/common_runtime/next_pluggable_device:next_pluggable_device_api",
        "//tensorflow/core/common_runtime/next_pluggable_device:next_pluggable_device_factory",
        "//tensorflow/core/platform:stream_executor",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@local_xla//xla/pjrt:pjrt_api",
        "@local_xla//xla/pjrt/c:pjrt_c_api_hdrs",
        "@local_xla//xla/stream_executor:stream_executor_h",
        "@local_xla//xla/stream_executor/integrations:device_mem_allocator",
    ] + if_static([
        "//tensorflow/core/common_runtime:copy_tensor",
    ]),
)

cc_library(
    name = "pluggable_device_runtime",
    hdrs = [":pluggable_device_runtime_headers"],
    linkstatic = 1,
    deps = [
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/common_runtime:core_cpu",
        "//tensorflow/core/common_runtime:dma_helper",
        "//tensorflow/core/common_runtime:shared_counter",
        "//tensorflow/core/lib/core:status",
        "//tensorflow/core/platform:stream_executor",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/status",
        "@local_xla//xla/stream_executor:stream_executor_h",
        "@local_xla//xla/stream_executor/integrations:device_mem_allocator",
    ] + if_static([
        # Temporary workaround for duplicated symbols issues.
        ":pluggable_device_runtime_impl",
        "//tensorflow/core/common_runtime:bfc_allocator",
        "//tensorflow/core/common_runtime:local_device",
        "//tensorflow/core/common_runtime:process_state",
    ]),
)

cc_library(
    name = "pluggable_device_bfc_allocator",
    srcs = [
        "pluggable_device_bfc_allocator.cc",
    ],
    hdrs = ["pluggable_device_bfc_allocator.h"],
    features = ["parse_headers"],
    visibility = ["//visibility:public"],
    deps = [
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/common_runtime:bfc_allocator",
        "//tensorflow/core/common_runtime/device:device_mem_allocator",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/memory",
        "@local_xla//xla/tsl/framework:allocator",
        "@local_xla//xla/tsl/framework:bfc_allocator",
    ],
)

cc_library(
    name = "pluggable_device_simple_allocator",
    srcs = [
        "pluggable_device_simple_allocator.cc",
    ],
    hdrs = ["pluggable_device_simple_allocator.h"],
    features = ["parse_headers"],
    visibility = ["//visibility:public"],
    deps = [
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/framework:allocator",
        "@local_xla//xla/tsl/framework:allocator",
    ],
)

cc_library(
    name = "pluggable_device_init",
    hdrs = [
        "pluggable_device_init.h",
    ],
    deps = [
        "pluggable_device_init_impl",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core/platform:stream_executor",
        "@com_google_absl//absl/status",
    ],
)

cc_library(
    name = "pluggable_device_init_impl",
    srcs = [
        "pluggable_device_init.cc",
    ],
    hdrs = [
        "pluggable_device_init.h",
    ],
    copts = tf_copts(),
    linkstatic = 1,
    deps = [
        "//tensorflow/core:core_cpu_lib",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core/platform:stream_executor",
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/status",
        "@local_xla//xla/stream_executor:platform_manager",
    ],
    alwayslink = 1,
)

tf_cc_test(
    name = "pluggable_device_plugin_init_test",
    size = "small",
    srcs = ["pluggable_device_plugin_init_test.cc"],
    deps = [
        ":pluggable_device_plugin_init",
        "//tensorflow/c:c_api",
        "//tensorflow/c/experimental/grappler:grappler_hdrs",
        "//tensorflow/c/experimental/pluggable_profiler:pluggable_profiler_hdrs",
        "//tensorflow/c/experimental/stream_executor:stream_executor_hdrs",
        "//tensorflow/c/experimental/stream_executor/test:test_pluggable_device",  # buildcleaner: keep
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core/common_runtime/next_pluggable_device/c:plugin_c_api_hdrs",
        "//tensorflow/core/common_runtime/next_pluggable_device/c:test_next_pluggable_device_plugin",  # buildcleaner: keep
        "@com_google_googletest//:gtest_main",
        "@local_xla//xla/pjrt/c:pjrt_c_api_hdrs",
    ],
)
