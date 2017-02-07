cmake_minimum_required(VERSION 3.1)

project(djinni)

#
# Setup directories
#
set(SUPPORT_LIB_DIR support-lib)
set(JNI_DIR ${SUPPORT_LIB_DIR}/jni)
set(OBJ_C_DIR ${SUPPORT_LIB_DIR}/objc)
set(INCLUDE_DIRS ${SUPPORT_LIB_DIR})

set(SRC_SHARED
	"${SUPPORT_LIB_DIR}/djinni_common.hpp"
	"${SUPPORT_LIB_DIR}/proxy_cache_interface.hpp"
	"${SUPPORT_LIB_DIR}/proxy_cache_impl.hpp"
)

set(JNI_HEADERS
	"${JNI_DIR}/djinni_support.hpp"
	"${JNI_DIR}/Marshal.hpp"
)

set(JNI_SRC
	"${JNI_DIR}/djinni_main.cpp"
	"${JNI_DIR}/djinni_support.cpp"
)

set(OBJC_HEADERS
	"${OBJ_C_DIR}/DJICppWrapperCache+Private.h"
	"${OBJ_C_DIR}/DJIError.h"
	"${OBJ_C_DIR}/DJIMarshal+Private.h"
	"${OBJ_C_DIR}/DJIObjcWrapperCache+Private.h"
)

set(OBJ_C_SRC
	"${OBJ_C_DIR}/DJIError.mm"
	"${OBJ_C_DIR}/DJIProxyCaches.mm"
)

# Setup the source files
set(SRC_FILES "")

# Setup shared cpp header group
source_group("cpp" FILES ${SRC_SHARED})

if (APPLE)
	list(APPEND SRC_FILES ${OBJ_C_SRC})
	source_group("objc" FILES ${OBJ_C_SRC} ${OBJC_HEADERS})
	list(APPEND INCLUDE_DIRS ${OBJ_C_DIR})
endif(APPLE)

if (ANDROID)
    list(APPEND SRC_FILES ${JNI_SRC})
    source_group("jni" FILES ${JNI_C_SRC} ${JNI_HEADERS})
    list(APPEND INCLUDE_DIRS ${JNI_DIR})
endif(ANDROID)

# Add the djinni support lib
add_library(djinni SHARED ${SRC_FILES})

# Add the include directories
include_directories(${INCLUDE_DIRS})

# Do extra stuff for apple framework
if (APPLE)
	target_link_libraries(djinni "-framework Foundation")
	target_compile_options(djinni PUBLIC "-fobjc-arc")
	set_target_properties(djinni PROPERTIES
		CXX_STANDARD 11
		CXX_STANDARD_REQUIRED true
		XCODE_ATTRIBUTE_ONLY_ACTIVE_ARCH NO
		MACOSX_FRAMEWORK_BUNDLE_VERSION "1.0.0"
		MACOSX_FRAMEWORK_IDENTIFIER "com.dropbox.djinni"
	)
endif(APPLE)
