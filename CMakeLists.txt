cmake_minimum_required(VERSION 2.6)

project(AccidentalNoiseLibrary)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_definitions(-DUSETHREAD)

include_directories(ThirdParty/lua-5.1.4/src/ ThirdParty/toluapp/include/)

file(GLOB lua_sources RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ThirdParty/lua-5.1.4/src/*.c)
list(REMOVE_ITEM lua_sources ThirdParty/lua-5.1.4/src/lua.c ThirdParty/lua-5.1.4/src/luac.c)
file(GLOB toluapp_sources RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} ThirdParty/toluapp/src/lib/*.c)
set(additional_sources Framework/main.cpp Bindings/bind_anl.cpp)
set(sources ${lua_sources} ${toluapp_sources} ${additional_sources})

add_executable(anl ${sources})

include(GNUInstallDirs)

set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "/usr/local" CACHE PATH "Install path prefix, prepended onto install directories" FORCE)
endif()

set(DEST_DIR ${CMAKE_INSTALL_INCLUDEDIR}/anl)

function(install_files_with_extension src_dir dest_dir extension)
    file(GLOB_RECURSE FILES "${src_dir}/*.${extension}")
    foreach(file ${FILES})
        if(NOT file MATCHES "/ThirdParty/") # skip files in the ThirdParty directory
            file(RELATIVE_PATH rel_file ${src_dir} ${file})
            get_filename_component(dest_dir_path ${dest_dir}/${rel_file} DIRECTORY)
            install(FILES ${file} DESTINATION ${dest_dir_path})
        endif()
    endforeach()
endfunction()

install_files_with_extension(${SOURCE_DIR} ${DEST_DIR} "h")
install_files_with_extension(${SOURCE_DIR} ${DEST_DIR} "inl")
