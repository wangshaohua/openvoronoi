SET(test_name "cpptest_ttt_glyph" )

MESSAGE(STATUS "configuring c++ test: " ${test_name})
set(SOURCE_FILES ttt_glyph.cpp)
add_executable(${test_name} ${SOURCE_FILES})
add_dependencies(${test_name}  libopenvoronoi)

unset(Boost_LIBRARIES) # required because this contains boost-python when we come here
find_package( Boost COMPONENTS program_options REQUIRED)

# the ttt lib
find_library(TTT_LIBRARY 
            NAMES truetypetracer
            PATHS /usr/local/lib/truetypetracer
            DOC "ttt"
            REQUIRED
)
include_directories( /usr/local/include/truetypetracer )

# freetype
find_package(Freetype REQUIRED)
if( FREETYPE_FOUND )
   MESSAGE(STATUS "FREETYPE_LIBRARY is: " ${FREETYPE_LIBRARY})
   MESSAGE(STATUS "FREETYPE_INCLUDE_DIRS is: " ${FREETYPE_INCLUDE_DIRS})
endif()
include_directories(${FREETYPE_INCLUDE_DIRS})

target_link_libraries(${test_name} libopenvoronoi ${TTT_LIBRARY} ${Boost_LIBRARIES})

ADD_TEST(${test_name} ${test_name})

ADD_TEST(${test_name}_help ${test_name} --help)
set_property(
    TEST ${test_name}_help
    PROPERTY WILL_FAIL TRUE
)

ADD_TEST(${test_name}_fail ${test_name} --c 53) # illegal char index
set_property(
    TEST ${test_name}_fail
    PROPERTY WILL_FAIL TRUE
)


set( TTT_CASES   0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25) #  
set( TTT2_CASES 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51) # 35
foreach( CASE ${TTT_CASES} )
    ADD_TEST(${test_name}_big_${CASE} ${test_name} --c ${CASE})
endforeach()
foreach( CASE ${TTT2_CASES} )
    ADD_TEST(${test_name}_small_${CASE} ${test_name} --c ${CASE})
endforeach()
