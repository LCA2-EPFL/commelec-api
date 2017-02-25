set_source_files_properties(${CAPNP_SRCS} PROPERTIES GENERATED TRUE)

# Below was needed as interpreter is compiled before 
# commelec api and it did find the appropriate generated schema files
# TODO: schema.capnp file needed to be copied from commelec-api directory 
# to commelec-interpreter directory, thus, making a duplicate of it. As 
# it is not good to have duplicates, we need to build it without having 
# a duplicate
if(CMAKE_SYSTEM_NAME STREQUAL Windows)
  set(CAPNPC_OUTPUT_DIR ${PROJECT_SOURCE_DIR}/commelec-api)
  capnp_generate_cpp(CAPNP_SRCS CAPNP_HDRS schema.capnp)
endif()

add_library(cl_interpreter 
  adv-interpreter-eval.cpp  adv-interpreter-proj.cpp  adv-interpreter.cpp
  adv-interpreter-membership.cpp  adv-interpreter-recthull.cpp  boundingbox-convexpolygon.cpp
  ../commelec-api/mathfunctions.cpp ${CAPNP_SRCS})
  
target_link_libraries(cl_interpreter ${CAPNP_LIBRARIES} seidel)

#install (TARGETS messaging DESTINATION lib)
#install (FILES AdvFunc.hpp PolynomialConvenience.hpp DESTINATION include)

