# bazel + java-grpc on Windows bug

Run ```bazel build a_java_grpc --verbose_failures``` on Windows. Output on my machine:

```
Starting local Bazel server and connecting to it...
(14:47:11) INFO: Invocation ID: bcfe7f50-e3d1-4516-b6fb-cf982f971274
(14:47:11) INFO: Current date is 2019-08-12
(14:47:13) INFO: Analyzed target //:a_java_grpc (38 packages loaded, 1135 targets configured).
(14:47:13) INFO: Found 1 target...
(14:47:13) INFO: Deleting stale sandbox base C:/tmp/_bazel_jannis.schnitzer/yt6e6gxo/sandbox
(14:47:15) ERROR: C:/users/jannis.schnitzer/src/github.com/xjs/bazel-grpc-java-win-repro/BUILD.bazel:47:1: Couldn't build file a_java_grpc-proto-gensrc.jar: SkylarkAction a_java_grpc-proto-gensrc.jar failed (Exit 1): protoc.exe failed: error executing command
  cd C:/tmp/_bazel_jannis.schnitzer/yt6e6gxo/execroot/com_github_Xjs_bazel_grpc_java_win_repro
bazel-out/host/bin/external/com_google_protobuf/protoc.exe --plugin=protoc-gen-rpc-plugin=bazel-out/host/bin/external/io_grpc_grpc_java/compiler/grpc_java_plugin.exe --rpc-plugin_out=:bazel-out/x64_windows-fastbuild/bin/a_java_grpc-proto-gensrc.jar --descriptor_set_in bazel-out/x64_windows-fastbuild/bin/b_proto-descriptor-set.proto.bin:bazel-out/x64_windows-fastbuild/bin/c_proto-descriptor-set.proto.bin:bazel-out/x64_windows-fastbuild/bin/a_proto-descriptor-set.proto.bin a.proto
Execution platform: @bazel_tools//platforms:host_platform
bazel-out/x64_windows-fastbuild/bin/b_proto-descriptor-set.proto.bin:bazel-out/x64_windows-fastbuild/bin/c_proto-descriptor-set.proto.bin:bazel-out/x64_windows-fastbuild/bin/a_proto-descriptor-set.proto.bin: No such file or directory
Target //:a_java_grpc failed to build
(14:47:16) INFO: Elapsed time: 6.895s, Critical Path: 1.19s
(14:47:16) INFO: 0 processes.
(14:47:16) FAILED: Build did NOT complete successfully
```

It can be observed that the parameter `--descriptor_set_in bazel-out/x64_windows-fastbuild/bin/b_proto-descriptor-set.proto.bin:bazel-out/x64_windows-fastbuild/bin/c_proto-descriptor-set.proto.bin:bazel-out/x64_windows-fastbuild/bin/a_proto-descriptor-set.proto.bin` is separated by colons. Running `protoc.exe --descriptor_set_in foo --descriptor_set_in bar` from the sandbox (path can be extracted from the `--verbose_failures` output) causes protoc.exe to report which path separator it was built with, which is in this case `;`. The path separator can be traced to https://github.com/protocolbuffers/protobuf/blob/master/src/google/protobuf/compiler/command_line_interface.cc#L767 -- however, [java_grpc_library.bzl](https://github.com/grpc/grpc-java/blob/master/java_grpc_library.bzl#L96) always joins paths with `:`.

