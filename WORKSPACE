workspace(name = "com_github_Xjs_bazel_grpc_java_win_repro")

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "io_grpc_grpc_java",
    sha256 = "6b6fadbcbb6c907c0aa05d3c71b12935f8f2d905842cc05709fee82e141f1a33",
    strip_prefix = "grpc-java-1.22.0",
    urls = ["https://github.com/grpc/grpc-java/archive/v1.22.0.zip"],
)

load("@io_grpc_grpc_java//:repositories.bzl", "grpc_java_repositories")

grpc_java_repositories()
