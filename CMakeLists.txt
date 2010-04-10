#
# Currently only 32-bit builds are supported, 64-bit will require changes to
# config.h, this should all be handled by CMake at some point.
#

cmake_minimum_required(VERSION 2.6)

include(FindPythonInterp)
if(NOT PYTHONINTERP_FOUND)
    message(WARNING "Python not found, regress test will not build.")
endif()

project(libevent)

# Simplify the build directory structure. 
SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)
SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)
SET(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)

if(MSVC)
    add_definitions(
        -D_CRT_SECURE_NO_WARNINGS 
        -D_CRT_SECURE_NO_DEPRECATE
        -D_CRT_NONSTDC_NO_WARNINGS
        -DHAVE_CONFIG_H
    )
endif()

include_directories(
    include 
    compat 
    WIN32-Code 
    .
)

set(include_event2_src
    include/event2/buffer.h
    include/event2/buffer_compat.h
    include/event2/bufferevent.h
    include/event2/bufferevent_compat.h
    include/event2/bufferevent_ssl.h
    include/event2/bufferevent_struct.h
    include/event2/dns.h
    include/event2/dns_compat.h
    include/event2/dns_struct.h
    include/event2/event.h
    include/event2/event_compat.h
    include/event2/event_struct.h
    include/event2/http.h
    include/event2/http_compat.h
    include/event2/http_struct.h
    include/event2/listener.h
    include/event2/rpc.h
    include/event2/rpc_compat.h
    include/event2/rpc_struct.h
    include/event2/tag.h
    include/event2/tag_compat.h
    include/event2/thread.h
    include/event2/util.h
)

set(include_internal_src
    bufferevent-internal.h
    changelist-internal.h
    defer-internal.h
    evbuffer-internal.h
    evdns.h
    event-internal.h
    event.h
    evhttp.h
    evmap-internal.h
    evrpc-internal.h
    evrpc.h
    evsignal-internal.h
    evthread-internal.h
    evutil.h
    ht-internal.h
    http-internal.h
    iocp-internal.h
    ipv6-internal.h
    log-internal.h
    minheap-internal.h
    mm-internal.h
    ratelim-internal.h
    strlcpy-internal.h
    util-internal.h
)

set(core_src
    buffer.c 
    bufferevent.c 
    bufferevent_filter.c 
    bufferevent_pair.c 
    bufferevent_ratelim.c 
    bufferevent_sock.c
    event.c  
    evmap.c 
    evthread.c 
    evutil.c
    evutil_rand.c
    listener.c 
    log.c 
    signal.c
    strlcpy.c 
)
    
set(windows_src
    buffer_iocp.c 
    bufferevent_async.c
    event_iocp.c
    evthread_win32.c 
    win32select.c 
)
    
set(extra_src
    evdns.c 
    event_tagging.c
    evrpc.c 
    http.c 
)

set(libevent_core_SRCS
    ${include_event2_src}
    ${include_internal_src}
)

if(WIN32)
    set (libevent_core_SRCS
        ${libevent_core_SRCS}
        ${core_src}
        ${windows_src}
    )
endif()

add_library(libevent_core STATIC ${libevent_core_SRCS})

add_library(libevent_extras STATIC ${extra_src})

add_library(libevent STATIC
    ${libevent_core_SRCS}
    ${extra_src}
)

source_group(include FILES ${include_internal_src})
source_group(include\\event2  FILES ${include_event2_src})
source_group(src\\core FILES ${core_src})
source_group(src\\windows FILES ${windows_src})
source_group(src\\extra FILES ${extra_src})

add_subdirectory(test)
