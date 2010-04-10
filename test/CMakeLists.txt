cmake_minimum_required(VERSION 2.6)

# Generate regress.gen.h/c
add_custom_command(
    OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/regress.gen.c ${CMAKE_CURRENT_SOURCE_DIR}/regress.gen.h
    COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/../event_rpcgen.py ${CMAKE_CURRENT_SOURCE_DIR}/regress.rpc ${CMAKE_CURRENT_SOURCE_DIR}/regress.gen.h ${CMAKE_CURRENT_SOURCE_DIR}/regress.gen.c
)

set(test_regress_src
    regress.c 
    regress_buffer.c 
    regress_bufferevent.c 
    regress_dns.c 
    regress_et.c
    regress_http.c 
    regress_iocp.c
    regress_listener.c
    regress_main.c
    regress_minheap.c
    regress_rpc.c
    regress_testutils.c
    regress_util.c 
    regress.gen.c 
    tinytest.c 
)

set(test_libs
    libevent
)

if(WIN32)
    set(test_libs
        ${test_libs}
        Ws2_32
    )
endif()

add_executable(regress ${test_regress_src})
target_link_libraries(regress ${test_libs})

add_executable(test-init test-init.c)
target_link_libraries(test-init ${test_libs})

add_executable(test-eof test-eof.c)
target_link_libraries(test-eof ${test_libs})

add_executable(test-weof test-weof.c)
target_link_libraries(test-weof ${test_libs})

add_executable(test-time test-time.c)
target_link_libraries(test-time ${test_libs})

#add_executable(bench bench.c)
#target_link_libraries(bench ${test_libs})

#add_executable(bench_cascade bench_cascade.c)
#target_link_libraries(bench_cascade ${test_libs})

#add_executable(bench_http bench_http.c)
#target_link_libraries(bench_http ${test_libs})

#add_executable(bench_httpclient bench_httpclient.c)
#target_link_libraries(bench_httpclient ${test_libs})
