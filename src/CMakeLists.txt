ADD_DEFINITIONS(-D_GNU_SOURCE)

list(APPEND CMAKE_REQUIRED_DEFINITIONS '-D_GNU_SOURCE')

include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${SNAPCAST_BINARY_DIR} ${CMAKE_CURRENT_BINARY_DIR})


# TODO the clientmgr should only be linked into the server. since intercom includes it, it currently must be in both
add_executable(snapcast-server server.c util.c taskqueue.c util.c timespec.c
	vector.c inputpipe.c clientmgr.c intercom.c syscallwrappers.c pcmchunk.c opuscodec.c)
add_executable(snapcast-client client.c util.c taskqueue.c util.c timespec.c
	vector.c alsaplayer.c intercom.c syscallwrappers.c clientmgr.c pcmchunk.c opuscodec.c)


target_link_libraries(snapcast-server ${JSON_C_LIBRARIES})
target_link_libraries(snapcast-server ${ALSA_LIBRARIES})      
target_link_libraries(snapcast-client ${JSON_C_LIBRARIES})
target_link_libraries(snapcast-client ${ALSA_LIBRARIES})      

#set_target_properties(snapcastc PROPERTIES COMPILE_FLAGS "-std=gnu11 -Wall -fsanitize=address" LINK_FLAGS " -fno-omit-frame-pointer -fsanitize=address -static-libasan")
set_target_properties(snapcast-client PROPERTIES COMPILE_FLAGS "-std=gnu11 -fno-omit-frame-pointer -Wall" LINK_FLAGS "")
set_target_properties(snapcast-server PROPERTIES COMPILE_FLAGS "-std=gnu11 -Wall" LINK_FLAGS "")

# install(TARGETS snapcast-client RUNTIME DESTINATION bin)
install(TARGETS snapcast-server RUNTIME DESTINATION bin)

# Get the current working branch
execute_process(
  COMMAND git rev-parse --abbrev-ref HEAD
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_BRANCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Get the latest abbreviated commit hash of the working branch
execute_process(
  COMMAND git log -1 --format=%h
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_COMMIT_HASH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

configure_file(
  ${CMAKE_SOURCE_DIR}/src/version.h.in
  ${CMAKE_BINARY_DIR}/src/version.h
)
