#
# List of sources/headers
# NB: This is currently an exact copy of the variables specified in Makefile but
#     could probably be improved.
file(GLOB files_H maniac/*.hpp maniac/*.cpp image/*.hpp transform/*.hpp flif-enc.hpp flif-dec.hpp
	    	  common.hpp flif_config.h filio.hpp io.hpp io.cpp config.h compile-specific.hpp)
file(GLOB files_C maniac/chance.cpp maniac/symbol.cpp image/crc32k.cpp image/image.cpp image/image-png.cpp
	  	  image/image-pnm.cpp image/image-pam.cpp image/image-rggb.cpp image/color_range.cpp
		  transform/factory.cpp common.cpp flif-enc.cpp flif-dec.cpp io.cpp)

#
# flifenc: command-line encoder/decoder
option(FLIF_WITH_flifenc "Build command-line encoder/decoder" ON)
if(FLIF_WITH_flifenc)
    add_executable(flifenc flif.cpp ${files_H} ${files_C})
    set_target_properties(flifenc PROPERTIES OUTPUT_NAME flif CXX_STANDARD 11 CXX_STANDARD_REQUIRED ON)
    target_link_libraries(flifenc ${PNG_LIBRARIES})
    install(TARGETS flifenc RUNTIME DESTINATION bin)
endif(FLIF_WITH_flifenc)

#
# libflif_dec: decoder-only library
option(FLIF_WITH_libflif_dec "Build decoder-only library" ON)
if(FLIF_WITH_libflif_dec)
    add_library(flif_dec ${FLIF_LIB_TYPE} ${files_H} ${files_C} library/flif_dec.h library/flif-interface-private_dec.hpp
                                          library/flif-interface_dec.cpp library/flif_common.h)
    set_target_properties(flif_dec PROPERTIES CXX_STANDARD 11 CXX_STANDARD_REQUIRED ON)
    if(FLIF_BUILD_SHARED_LIBS)
        target_link_libraries(flif_dec ${PNG_LIBRARIES})
        if(WIN32)
            target_compile_definitions(flif_dec PRIVATE -DFLIF_BUILD_DLL)
        endif(WIN32)
    endif(FLIF_BUILD_SHARED_LIBS)
    install(TARGETS flif_dec RUNTIME DESTINATION bin LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
endif(FLIF_WITH_libflif_dec)

#
# libflif_enc: encoder-only library
option(FLIF_WITH_libflif_enc "Build encoder-only library" ON)
if(FLIF_WITH_libflif_enc)
    add_library(flif_enc ${FLIF_LIB_TYPE} ${files_H} ${files_C} library/flif_enc.h library/flif-interface-private_enc.hpp
                                          library/flif-interface_enc.cpp library/flif_common.h)
    set_target_properties(flif_enc PROPERTIES CXX_STANDARD 11 CXX_STANDARD_REQUIRED ON)
    if(FLIF_BUILD_SHARED_LIBS)
        target_link_libraries(flif_enc ${PNG_LIBRARIES})
        if(WIN32)
            target_compile_definitions(flif_enc PRIVATE -DFLIF_BUILD_DLL)
        endif(WIN32)
    endif(FLIF_BUILD_SHARED_LIBS)
    install(TARGETS flif_enc RUNTIME DESTINATION bin LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
endif(FLIF_WITH_libflif_enc)

#
# libflif: combined encoder/decoder library
option(FLIF_WITH_libflif "Build combined encoder/decoder library" ON)
if(FLIF_WITH_libflif)
    add_library(flif ${FLIF_LIB_TYPE} ${files_H} ${files_C}
                                      library/flif_dec.h library/flif-interface-private_dec.hpp
                                      library/flif_enc.h library/flif-interface-private_enc.hpp
                                      library/flif-interface.cpp
                                      library/flif_common.h)
    set_target_properties(flif PROPERTIES CXX_STANDARD 11 CXX_STANDARD_REQUIRED ON)
    if(FLIF_BUILD_SHARED_LIBS)
        target_link_libraries(flif ${PNG_LIBRARIES})
        if(WIN32)
            target_compile_definitions(flif PRIVATE -DFLIF_BUILD_DLL)
        endif(WIN32)
    endif(FLIF_BUILD_SHARED_LIBS)
    install(TARGETS flif RUNTIME DESTINATION bin LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
endif(FLIF_WITH_libflif)

#
# viewflif: SDL2-based FLIF viewer
option(FLIF_WITH_viewflif "Build viewflif viewer (requires libflif_dec)" ON)
if(FLIF_WITH_viewflif AND FLIF_WITH_libflif_dec)
    find_package(SDL2 REQUIRED)
    add_executable(viewflif viewflif.c)
    target_include_directories(viewflif PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/library PRIVATE ${SDL2_INCLUDE_DIRS})
    string(STRIP ${SDL2_LIBRARIES} FLIF_SDL2_LIBRARIES)
    target_link_libraries(viewflif flif_dec ${PNG_LIBRARIES} ${FLIF_SDL2_LIBRARIES})
    install(TARGETS viewflif RUNTIME DESTINATION bin)
endif(FLIF_WITH_viewflif AND FLIF_WITH_libflif_dec)

#
# Install target for headers
file(GLOB libflif_INSTALL_HEADERS library/*.h)
install(FILES ${libflif_INSTALL_HEADERS} DESTINATION include)
