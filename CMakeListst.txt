cmake_minimum_required(VERSION 3.10)
project(StreamProject LANGUAGES C Fortran)

# Set compile flags for both C and Fortran
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O2 -fopenmp")
set(CMAKE_Fortran_FLAGS "${CMAKE_Fortran_FLAGS} -O2 -fopenmp")

# Build the stream_f executable from stream.f and mysecond.c
# CMake will compile stream.f with the Fortran compiler and mysecond.c with the C compiler.
add_executable(stream_f stream.f mysecond.c)
# We force the linker to be Fortran by setting the LINKER_LANGUAGE property.
# set_target_properties(stream_f PROPERTIES LINKER_LANGUAGE Fortran)

# Build the stream_c executable from stream.c
add_executable(stream_c stream.c)

# Output names to include the .exe suffix on Windows:
set_target_properties(stream_f PROPERTIES OUTPUT_NAME "stream_f.exe")
set_target_properties(stream_c PROPERTIES OUTPUT_NAME "stream_c.exe")


# Custom target for building with Intel icc.
# This target is not built by default. To build it, invoke:
#   cmake --build . --target stream_icc
add_custom_target(stream_icc
    COMMAND icc -O3 -xCORE-AVX2 -ffreestanding -qopenmp -DSTREAM_ARRAY_SIZE=80000000 -DNTIMES=20 ${CMAKE_CURRENT_SOURCE_DIR}/stream.c -o stream.omp.AVX2.80M.20x.icc
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/stream.c
    COMMENT "Building stream using Intel icc"
)
