cmake_minimum_required(VERSION 3.15)
project(boost_fiber)

set(CMAKE_CXX_STANDARD 17)

# #################################################################################################

find_package(Threads)

## ################################################################################################
## ################################################################################################

## look for conan configuration file
set(use_conan OFF)

set(conan_in_file  "${CMAKE_SOURCE_DIR}/conanfile.txt")
set(conan_out_file "${CMAKE_BINARY_DIR}/conanbuildinfo.cmake")

if( EXISTS ${conan_in_file} )
  message(STATUS "conan project configuration found")

  ## look for conan in PATH
  set(os_path "$ENV{PATH}")

  ## convert into a cmake list - this is linux-specific
  string(REPLACE ":" ";" search_path ${os_path})

  ## look at each path
  foreach(current_path ${search_path})

    set(look_at "${current_path}/conan")

    find_program(conan_exe "${look_at}")

    if( conan_exe )
      message(STATUS "conan found: ${conan_exe}")
      set(use_conan ON)
      break()
    else()
      message(WARNING "conan not found!")
    endif()

  endforeach()

else()
  message(STATUS "no conan project configuration - not using conan")
endif()

## ################################################################################################
## ################################################################################################

if( use_conan )

  ## check if we need to call "install"
  if( (NOT EXISTS ${conan_out_file}) OR (${conan_in_file} IS_NEWER_THAN ${conan_out_file}) )

    file(REMOVE ${conan_out_file})

    message(STATUS "calling conan install")

    execute_process(
      COMMAND           ${conan_exe} install ..
      WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
      RESULT_VARIABLE   return_code
    )

    if( NOT ${return_code} EQUAL 0 )
      message(STATUS "conan install .. failed.")
      set(use_conan OFF)
    endif()

  endif()

endif()

## conan search boost --remote=conan-center

## either use conan package manager or cmake to prepare boost, in case of conan keep output directories as they
## are
if( use_conan )
  include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
  conan_basic_setup(NO_OUTPUT_DIRS)
else()
  find_package(Boost 1.67.0 COMPONENTS fiber asio)
endif()

#[=[
  add_library(boost::system INTERFACE IMPORTED)
  add_library(boost::fiber  INTERFACE IMPORTED)
  add_library(boost::asio   INTERFACE IMPORTED)

  set_target_properties(boost::system
        PROPERTIES
        INTERFACE_INCLUDE_DIRECTORIES   "${Boost_INCLUDE_DIRS}"
        INTERFACE_LINK_LIBRARIES        "${Boost_SYSTEM_LIBRARIES}"
        )

  set_target_properties(boost::fiber PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES   "${Boost_INCLUDE_DIRS}"
    INTERFACE_LINK_LIBRARIES        "${Boost_FIBER_LIBRARIES}"
    )

  set_target_properties(boost::asio PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES   "${Boost_INCLUDE_DIRS}"
    INTERFACE_LINK_LIBRARIES        "${Boost_ASIO_LIBRARIES}"
    )


get_cmake_property(var_names VARIABLES)
message(var_names="${var_names}")

message(STATUS conan_libs = ${CONAN_LIBS})
set(Boost_LIBRARIES "${CONAN_LIBS}")
#]=]

###
###
#[=[
find_package(Boost REQUIRED COMPONENTS fiber)

add_library(boost::fiber INTERFACE IMPORTED)

set_target_properties(boost::fiber PROPERTIES
    INTERFACE_INCLUDE_DIRECTORIES   "${Boost_INCLUDE_DIRS}"
    INTERFACE_LINK_LIBRARIES        "${Boost_FIBER_LIBRARIES}"
    )


#set_target_properties(boost::program_options PROPERTIES
#  INTERFACE_INCLUDE_DIRECTORIES   "${Boost_INCLUDE_DIRS}"
#  INTERFACE_LINK_LIBRARIES        "${Boost_PROGRAM_OPTIONS_LIBRARIES}"
#  )
#]=]

## ################################################################################################

add_executable(work_sharing work_sharing/work_sharing.cpp ../thread_barrier.hpp)
target_link_libraries(work_sharing ${CONAN_LIBS})

add_executable(adapt_callbacks adapt_callbacks/adapt_callbacks.cpp)
target_link_libraries(adapt_callbacks ${CONAN_LIBS})

add_executable(barrier barrier/thread_barrier.cpp)
target_link_libraries(barrier ${CONAN_LIBS})

add_executable(adapt_nonblocking adapt_nonblocking/adapt_nonblocking.cpp)
target_link_libraries(adapt_nonblocking ${CONAN_LIBS})

add_executable(simple simple/simple.cpp)
target_link_libraries(simple ${CONAN_LIBS})

add_executable(ping_pong ping_pong/ping_pong.cpp)
target_link_libraries(ping_pong ${CONAN_LIBS})

add_executable(wait_stuff wait_stuff/wait_stuff.cpp)
target_link_libraries(wait_stuff ${CONAN_LIBS})

add_executable(priority priority/priority.cpp)
target_link_libraries(priority ${CONAN_LIBS})

add_executable(future future/future.cpp)
target_link_libraries(future ${CONAN_LIBS})

add_executable(segmented_stack segmented_stack/segmented_stack.cpp)
target_link_libraries(segmented_stack ${CONAN_LIBS})

add_executable(adapt_method_calls adapt_method_calls/adapt_method_calls.cpp)
target_link_libraries(adapt_method_calls ${CONAN_LIBS})

add_executable(work_stealing work_stealing/work_stealing.cpp ../thread_barrier.hpp)
target_link_libraries(work_stealing ${CONAN_LIBS})

add_executable(join join/join.cpp)
target_link_libraries(join ${CONAN_LIBS})

## ---

add_executable(asio_exchange
  asio/exchange/exchange.cpp
  asio/round_robin.hpp
  asio/yield.hpp
  asio/detail/yield.hpp
  )
target_link_libraries(asio_exchange ${CONAN_LIBS})

add_executable(asio_subscriber
  asio/ps/subscriber.cpp
  )
target_link_libraries(asio_subscriber ${CONAN_LIBS})

add_executable(asio_publisher
  asio/ps/publisher.cpp
  )
target_link_libraries(asio_publisher ${CONAN_LIBS})

add_executable(asio_server
  asio/ps/server.cpp
  )
target_link_libraries(asio_server ${CONAN_LIBS})

add_executable(asio_autoecho
  asio/autoecho/autoecho.cpp
  asio/round_robin.hpp
  asio/yield.hpp
  asio/detail/yield.hpp
  )
target_link_libraries(asio_autoecho ${CONAN_LIBS})

## ---

add_executable(range_for range_for/range_for.cpp)
target_link_libraries(range_for ${CONAN_LIBS})

if(0)
add_executable(numa numa/topology.cpp)
target_link_libraries(numa ${CONAN_LIBS})


### HIP: Heterogenous-computing Interface for Portability
add_executable(hip_multiple_streams hip/multiple_streams.cpp)
target_link_libraries(hip_multiple_streams ${CONAN_LIBS})

add_executable(hip_single_stream hip/single_stream.cpp)
target_link_libraries(hip_single_stream ${CONAN_LIBS})
endif()

