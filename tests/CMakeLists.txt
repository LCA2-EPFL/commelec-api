set_source_files_properties(${CAPNP_SRCS} PROPERTIES GENERATED TRUE)

add_executable(interpreter_test interpreter.cpp ${CAPNP_SRCS})
target_link_libraries (interpreter_test ${CAPNP_LIBRARIES} seidel hlapi cl_interpreter) 

add_executable(send_adv send-test-advertisement.cpp ${CAPNP_SRCS})
target_link_libraries (send_adv ${CAPNP_LIBRARIES} hlapi) 

add_executable(send_req send-test-request.cpp ${CAPNP_SRCS})
target_link_libraries (send_req ${CAPNP_LIBRARIES} hlapi) 
