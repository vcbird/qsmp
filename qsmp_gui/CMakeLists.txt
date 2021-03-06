project(qsmp_gui)

set(Boost_USE_STATIC_LIBS ON)
find_package(Boost REQUIRED filesystem system thread date_time iostreams regex)

find_package(ID3 REQUIRED)

set(TBB_ROOT E:/SCM/tbb-win32 CACHE FILEPATH "tbb root")
set(TBB_INCLUDE_DIR ${TBB_ROOT}/include)
set(TBB_LIBRARY_DIR ${TBB_ROOT}/ia32/vc8/lib CACHE FILEPATH "tbb lib dir")
set(TBB_LIBRARIES debug ${TBB_LIBRARY_DIR}/tbb_debug.lib
                  debug ${TBB_LIBRARY_DIR}/tbbmalloc_debug.lib
                  optimized ${TBB_LIBRARY_DIR}/tbb.lib
                  optimized ${TBB_LIBRARY_DIR}/tbbmalloc.lib)


include_directories(${Boost_INCLUDE_DIR} 
                    ${MMShellHook_SOURCE_DIR}
                    ${id3lib_INCLUDE_DIR}
		                ${ID3_INCLUDE_DIR}
                    ${lua_INCLUDE_DIR}
                    #${TBB_INCLUDE_DIR}
                    ${TCL_INCLUDE_DIR}
                    ${boost_singleton_INCLUDE_DIR}
                    include
                    ${CMAKE_CURRENT_BINARY_DIR}/moc)

add_definitions(-DID3LIB_LINKOPTION=1
                -DQT_NO_KEYWORDS)

set(QT_USE_PHONON 1)
set(QT_USE_QTNETWORK 1)
find_package(Qt4)
include(${QT_USE_FILE})

set(headers 
            common.h
            utilities.h
            #LuaTcpConsole.h
            Player.h
            ViewSelector.h
            PlaylistView.h
            Cache.h
            Process.h
            CacheModel.h
            TreeModel.h
            TreeModel.inl
            stdafx.h
   )
set(sources 
            qsmp_gui.cpp
            #LuaTcpConsole.cpp
            Player.cpp
            PlaylistView.cpp
            ViewSelector.cpp
            Cache.cpp
            Process.cpp
            CacheModel.cpp
    )

if(WIN32)
  set(sources ${sources} HotkeyWindow.cpp stdafx.cpp)
  set(headers ${headers} HotkeyWindow.h stdafx.h)
endif(WIN32)

qsmp_qt4_automoc(${sources})
add_executable(qsmp_gui ${sources} ${headers} ${ui})


target_link_libraries(qsmp_gui 
                      qsmp_lib
                      ${QT_LIBRARIES}
                      ${Boost_LIBRARIES}
		                  ${ID3_LIBRARY}
                      #${TBB_LIBRARIES}
                      lua
                     )
if(USE_BUILTIN_ID3LIB)
  target_link_libraries(qsmp_gui id3lib zlib)
endif(USE_BUILTIN_ID3LIB)
if(WIN32)
  set_precompiled_header(qsmp_gui stdafx.h stdafx.cpp)
  target_link_libraries(qsmp_gui MMShellHook)
endif(WIN32)
