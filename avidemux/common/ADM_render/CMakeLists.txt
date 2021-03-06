include(admOpenGl)

IF(QT_LIBRARY_EXTENSION)
        SET(ADM_LIB ADM_render6_${QT_LIBRARY_EXTENSION})
        SET(SIMPLE_RENDER GUI_simpleRender.cpp)
ELSE(QT_LIBRARY_EXTENSION)
        SET(ADM_LIB ADM_render6_${UI_SUFFIX})
        SET(SIMPLE_RENDER GUI_simpleRenderStub.cpp)
ENDIF(QT_LIBRARY_EXTENSION)

SET(${ADM_LIB}_SRCS
GUI_render.cpp
${SIMPLE_RENDER}
)
IF(USE_DXVA2)# AND NOT MSVC)
# TEMPORARY	
        ADD_SUBDIRECTORY(ADM_coreDxva2)
        INCLUDE_DIRECTORIES(ADM_coreDxva2/include)
ENDIF(USE_DXVA2)# AND NOT MSVC)


IF(NOT ${UI_SUFFIX} STREQUAL "cli")
        IF (USE_OPENGL)
                INCLUDE_DIRECTORIES(${CMAKE_SOURCE_DIR}/ADM_openGL/include/)
	        SET(${ADM_LIB}_SRCS ${${ADM_LIB}_SRCS} GUI_qtGlRender.cpp)
        ENDIF (USE_OPENGL)
        
        IF (APPLE)
	        IF(USE_SDL)
		        SET(${ADM_LIB}_SRCS ${${ADM_LIB}_SRCS} GUI_sdlRenderHelper.m)
	        ENDIF(USE_SDL)
        ENDIF (APPLE)
        IF (USE_XV)
	        SET(${ADM_LIB}_SRCS ${${ADM_LIB}_SRCS} GUI_xvRender.cpp)
        ENDIF (USE_XV)
        IF (USE_SDL)
	        SET(${ADM_LIB}_SRCS ${${ADM_LIB}_SRCS} GUI_sdlRender.cpp)
        ENDIF (USE_SDL)
        IF (USE_VDPAU)
                ADD_CORE_INCLUDE(ADM_coreVdpau)
	        SET(${ADM_LIB}_SRCS ${${ADM_LIB}_SRCS} GUI_vdpauRender.cpp)
        ENDIF (USE_VDPAU)
        IF (USE_DXVA2)
                ADD_CORE_INCLUDE(ADM_coreDxva2)
	        SET(${ADM_LIB}_SRCS ${${ADM_LIB}_SRCS} GUI_dxva2Render.cpp)
        ENDIF (USE_DXVA2)
        IF (USE_LIBVA)
                ADD_CORE_INCLUDE(ADM_coreLibVA)
	        SET(${ADM_LIB}_SRCS ${${ADM_LIB}_SRCS} GUI_libvaRender.cpp)
        ENDIF (USE_LIBVA)
ENDIF() # Not cli

add_compiler_export_flags()
ADD_LIBRARY(${ADM_LIB} SHARED ${${ADM_LIB}_SRCS})
TARGET_LINK_LIBRARIES( ${ADM_LIB} ADM_core6 ADM_coreUI6 ADM_coreImage6)

ADM_OPEN_GL(${ADM_LIB})

SDLify(GUI_sdlRender.cpp)

IF (GETTEXT_FOUND)
        TARGET_LINK_LIBRARIES(${ADM_LIB}  ${GETTEXT_LIBRARY_DIR})
ENDIF (GETTEXT_FOUND)

IF (USE_SDL)
       TARGET_LINK_LIBRARIES(${ADM_LIB}  ${SDL2_LIBRARY})
ENDIF (USE_SDL)

IF (USE_VDPAU)
       TARGET_LINK_LIBRARIES(${ADM_LIB}  ADM_coreVDPAU6)
ENDIF (USE_VDPAU)
IF (USE_DXVA2)
       TARGET_LINK_LIBRARIES(${ADM_LIB}  ADM_coreDxva26)
ENDIF (USE_DXVA2)

IF (USE_XV)
	TARGET_LINK_LIBRARIES(${ADM_LIB} ${XVIDEO_LIBRARY_DIR})
ENDIF (USE_XV)
TARGET_LINK_LIBRARIES(${ADM_LIB}  ADM_coreUtils6)

IF(QT_LIBRARY_EXTENSION)
        TARGET_LINK_LIBRARIES(${ADM_LIB} ${QT_QTGUI_LIBRARY})
ENDIF(QT_LIBRARY_EXTENSION)

ADM_INSTALL_LIB(${ADM_LIB})
