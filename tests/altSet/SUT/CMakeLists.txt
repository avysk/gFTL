include_directories (${GFTL_BINARY_DIR}/tests/shared)

set (instantiations

  "integer\;free"
  "real\;free"
  "real32\;free"
  "real64\;free"
  "complex\;free"
  "complex64\;free"
  "complex128\;free"

  "deferredLengthString\;free"
  "unlimitedPolyPtr\;free"

  "integer1d\;free"
  "integer2d\;free"
  "integer2d_fixedExtents\;free"

  "integerAlloc\;free"
  "integerPtr\;free"
  "integer2dPtr\;free"

  "integer\;fixed"
 
  "Foo\;free"
  "FooPtr\;free"
  "FooPoly\;free"
  "FooPolyPtr\;free"
    
  "integer1d\;free"
  "integer2d\;free"
  "integer2d_fixedExtents\;free"
  )

if (SUPPORTS_POINTERS_TO_FIXED_LENGTH_STRINGS)
  list (APPEND instantiations "character17\;free")
endif ()

set (SRCS)
foreach (instantiation ${instantiations})
  list (GET instantiation 0 type)
  list (GET instantiation 1 format)

  set (infile ${CMAKE_CURRENT_SOURCE_DIR}/altSet.m4)
  if (format STREQUAL "free")
     set (outfile ${type}altSet.F90)
  else ()
     set (outfile ${type}altSet.F)
  endif ()


  # Use relative path for outfile so that CMake correctly
  # detects the need to generate include files.

  add_custom_command (
    OUTPUT ${outfile}
    DEPENDS ${infile}
    COMMAND m4 -Dtype=${type} -Dformat=${format} ${infile} > ${outfile}
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
    )

  list (APPEND SRCS ${outfile})

endforeach ()

list (APPEND SRCS MultiModule.F90)

add_library(altsetSUT STATIC EXCLUDE_FROM_ALL ${SRCS})
target_include_directories (altsetSUT PUBLIC ${CMAKE_CURRENT_BINARY_DIR})
target_include_directories (altsetSUT PRIVATE ${PFUNIT_TOP_DIR}/include)
target_link_libraries(altsetSUT type_test_values shared gftl)
