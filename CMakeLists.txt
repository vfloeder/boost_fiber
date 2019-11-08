cmake_minimum_required(VERSION 3.15)
project(boost_fiber_1)

set(CMAKE_CXX_STANDARD 17)

# #################################################################################################

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

message(Boost_LIBRARIES = ${Boost_LIBRARIES})
### include_directories(${Boost_INCLUDE_DIR})

message(STATUS "Boost version: ${Boost_VERSION}")

add_executable(work_sharing work_sharing/work_sharing.cpp ../barrier.hpp)
target_link_libraries(work_sharing ${Boost_LIBRARIES})

add_executable(adapt_callbacks adapt_callbacks/adapt_callbacks.cpp)
target_link_libraries(adapt_callbacks ${Boost_LIBRARIES})

add_executable(barrier barrier/barrier.cpp)
target_link_libraries(barrier ${Boost_LIBRARIES})

add_executable(adapt_nonblocking adapt_nonblocking/adapt_nonblocking.cpp)
target_link_libraries(adapt_nonblocking ${Boost_LIBRARIES})

add_executable(simple simple/simple.cpp)
target_link_libraries(simple ${Boost_LIBRARIES})

add_executable(ping_pong ping_pong/ping_pong.cpp)
target_link_libraries(ping_pong ${Boost_LIBRARIES})

add_executable(wait_stuff wait_stuff/wait_stuff.cpp)
target_link_libraries(wait_stuff ${Boost_LIBRARIES})

add_executable(priority priority/priority.cpp)
target_link_libraries(priority ${Boost_LIBRARIES})

add_executable(future future/future.cpp)
target_link_libraries(future ${Boost_LIBRARIES})

add_executable(segmented_stack segmented_stack/segmented_stack.cpp)
target_link_libraries(segmented_stack ${Boost_LIBRARIES})

add_executable(adapt_method_calls adapt_method_calls/adapt_method_calls.cpp)
target_link_libraries(adapt_method_calls ${Boost_LIBRARIES})

add_executable(work_stealing work_stealing/work_stealing.cpp ../barrier.hpp)
target_link_libraries(work_stealing ${Boost_LIBRARIES})

add_executable(join join/join.cpp)
target_link_libraries(join ${Boost_LIBRARIES})

## ---

add_executable(asio_exchange
  asio/exchange/exchange.cpp
  asio/round_robin.hpp
  asio/yield.hpp
  asio/detail/yield.hpp
  )
target_link_libraries(asio_exchange ${Boost_LIBRARIES})

add_executable(asio_subscriber
  asio/ps/subscriber.cpp
  )
target_link_libraries(asio_subscriber ${Boost_LIBRARIES})

add_executable(asio_publisher
  asio/ps/publisher.cpp
  )
target_link_libraries(asio_publisher ${Boost_LIBRARIES})

add_executable(asio_server
  asio/ps/server.cpp
  )
target_link_libraries(asio_server ${Boost_LIBRARIES})

add_executable(asio_autoecho
  asio/autoecho/autoecho.cpp
  asio/round_robin.hpp
  asio/yield.hpp
  asio/detail/yield.hpp
  )
target_link_libraries(asio_autoecho ${Boost_LIBRARIES})

## ---

add_executable(range_for range_for/range_for.cpp)
target_link_libraries(range_for ${Boost_LIBRARIES})

add_executable(numa numa/topology.cpp)
target_link_libraries(numa ${Boost_LIBRARIES})

if(0)
### HIP: Heterogenous-computing Interface for Portability
add_executable(hip_multiple_streams hip/multiple_streams.cpp)
target_link_libraries(hip_multiple_streams ${Boost_LIBRARIES})

add_executable(hip_single_stream hip/single_stream.cpp)
target_link_libraries(hip_single_stream ${Boost_LIBRARIES})
endif()

