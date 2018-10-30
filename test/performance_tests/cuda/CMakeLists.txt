set(LIB_DIR ${CMAKE_SOURCE_DIR}/src)

set(PERFORMANCE_TEST_CUDA_LIB_SRCS
    ${LIB_DIR}/cuda/cuda_device.cu
    ${LIB_DIR}/cuda/cuda_fft.cu
    ${LIB_DIR}/cuda/cuda_helper.cu
    ${LIB_DIR}/cuda/filtering_cuda.cu
    ${LIB_DIR}/cuda/image_function_cuda.cu)

set(SRCS 
    performance_test_helper_cuda.cu
    performance_test_image_function_cuda.cpp 
    performance_tests_cuda.cpp)

find_library(CUDART_LIBRARY cudart ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
find_library(CUFFT_LIBRARY cufft ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
add_library(performance_tests_cuda_lib ${PERFORMANCE_TEST_CUDA_LIB_SRCS})
target_compile_features(performance_tests_cuda_lib PUBLIC cxx_std_11)
set_property(TARGET performance_tests_cuda_lib PROPERTY CUDA_STANDARD 11)
set_target_properties(performance_tests_cuda_lib
                      PROPERTIES CUDA_SEPARABLE_COMPILATION ON)

add_library(performance_tests_cuda_warnings INTERFACE)
target_compile_options(performance_tests_cuda_warnings
                       INTERFACE -Wno-deprecated-gpu-targets)

add_executable(performance_tests_cuda ${SRCS})
target_include_directories(performance_tests_cuda PRIVATE ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES})
target_link_libraries(performance_tests_cuda performance_tests_cuda_lib
                                             ${CUDART_LIBRARY}
                                             performance_tests_cuda_warnings
                                             ${CUFFT_LIBRARY})
target_compile_features(performance_tests_cuda PUBLIC cxx_std_11)
set_property(TARGET performance_tests_cuda PROPERTY CUDA_STANDARD 11)
set_target_properties(performance_tests_cuda
                      PROPERTIES CUDA_SEPARABLE_COMPILATION ON)
add_test(NAME perf_test_cuda COMMAND performance_tests_cuda)