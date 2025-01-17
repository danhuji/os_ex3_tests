# Operating Systems - Ex3 - Basic test suite
This test suite was originally created by Mattan two years ago.
This test repo is forked to enable changes if needed.
## Instructions:

1. Clone this repository under the `tests` directory by using the following command:
   
   ```shell
   cd YOUR_PROJECT_ROOT_PATH_FOLDER
   git clone https://github.com/danhuji/os_ex3_tests tests
   ```
   
2. Use the following `CMakeLists.txt` in your project root. Add your own source files as needed at row #26

    ``` cmake
    cmake_minimum_required(VERSION 3.1)

    # NOTE: You can't have both ThreadSanitizer and AddressSanitizer enabled at the same time.

    # Uncomment the following to enable ThreadSanitizer.
    #set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=thread")
    #set (CMAKE_LINKER_FLAGS_DEBUG "${CMAKE_LINKER_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=thread")

    # Uncomment the following to enable AddressSanitizer.
    #set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=address")
    #set (CMAKE_LINKER_FLAGS_DEBUG "${CMAKE_LINKER_FLAGS_DEBUG} -fno-omit-frame-pointer -fsanitize=address")


    # Project configuration
    project(ex3 VERSION 1.0 LANGUAGES C CXX)


    # Ensure system has pthreads
    set(THREADS_PREFER_PTHREAD_FLAG ON)
    find_package(Threads REQUIRED)

    add_library(MapReduceFramework
            MapReduceClient.h
            MapReduceFramework.cpp MapReduceFramework.h
    # ------------- Add your own .h/.cpp files here -------------------
    )


    set_property(TARGET MapReduceFramework PROPERTY CXX_STANDARD 11)
    target_compile_options(MapReduceFramework PUBLIC -Wall -Wextra)
    target_include_directories(MapReduceFramework PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    # link pthreads to your framework
    target_link_libraries(MapReduceFramework PUBLIC Threads::Threads)

    # Add tests
    add_subdirectory(tests)
    ```
    
    **Note**, do not change 'MapReduceFramework' anywhere in the above template.
    
    **You can enable Address Sanitizer or Thread Sanitizer (only one of them), but you will
      fail the test `errorMessageTest`. Other tests should pass with NO MEMORY LEAKS.**
      
    **Valgrind is not recommended, prefer using ASan as it is much faser.**
    
    It is recommended that you also test your program with high optimization levels, by adding `-O2` to `target_compile_options`, and to delete print statements in your code (if you have any), as well as asserts (by using `-DNDEBUG` flag), as these things affect the program's behavior and can uncover multi-threading bugs.

3. If using *CLion*, reload the cmake project (under `File`) after doing the above, then build (`Build | Build 'All'`) the project.
   Now, you can run individual tests by going to the tests source code (`YOUR_PROJECT_ROOT_PATH/tests/SampleClient.cpp`), and there will appear a green arrow near each test (`TEST(...)`), that allows you to run/debug it.
   Alternatively, right click on the entire file and you can do `Run 'All in SampleClient...'` to run all tests at once.
   
   If using terminal - do the following to compile and run:
   **Note**: It is important to delete the `cmake-build-debug` whenever you migrate the project into a different system, it's not needed otherwise.
   
   * Compile and run:
   ```shell
   cd YOUR_PROJECT_ROOT_PATH
   rm -rf cmake-build-debug
   mkdir cmake-build-debug
   cd cmake-build-debug
   cmake ..
   make -j 4
   ```
   
   And run via executing `YOUR_PROJECT_ROOT_PATH/cmake-build-debug/tests/testRunner --gtest_filter="-*error*"` (without 'errorMessageTest', supports ASan/TSan), omit the *gtest_filter* to run the *errorMessageTest* too, but note that it will fail if you have a *sanitizer* enabled in your *CMakeLists*.
