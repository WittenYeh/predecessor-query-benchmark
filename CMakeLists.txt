cmake_minimum_required(VERSION 3.20)

project(predecessor_query_benchmark
  VERSION 0.1.0
  DESCRIPTION "Predecessor-query benchmark for storage engines"
  LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

include(FetchContent)

set(BENCHMARK_ENABLE_TESTING OFF CACHE BOOL "Disable Google Benchmark tests" FORCE)
set(BENCHMARK_ENABLE_GTEST_TESTS OFF CACHE BOOL "Disable Google Benchmark gtest tests" FORCE)
set(BENCHMARK_ENABLE_INSTALL OFF CACHE BOOL "Disable Google Benchmark install rules" FORCE)

FetchContent_Declare(
  google_benchmark
  GIT_REPOSITORY https://github.com/google/benchmark.git
  GIT_TAG v1.9.4
)

FetchContent_MakeAvailable(google_benchmark)

set(PQB_THIRD_PARTY_DIR "${CMAKE_CURRENT_SOURCE_DIR}/third-party" CACHE PATH
  "Directory containing third-party source repositories"
)
set(PQB_ROCKSDB_SOURCE_DIR "${PQB_THIRD_PARTY_DIR}/rocksdb" CACHE PATH
  "RocksDB source directory"
)
set(PQB_LEANSTORE_SOURCE_DIR "${PQB_THIRD_PARTY_DIR}/leanstore" CACHE PATH
  "LeanStore source directory"
)

add_library(pqb_project_options INTERFACE)
target_compile_features(pqb_project_options INTERFACE cxx_std_20)

function(add_pqb_benchmark target_name)
  add_executable(${target_name} ${ARGN})
  target_link_libraries(${target_name}
    PRIVATE
      benchmark::benchmark_main
      pqb_project_options
  )
endfunction()

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/benchmarks/CMakeLists.txt")
  add_subdirectory(benchmarks)
endif()
