cmake_minimum_required(VERSION 3.20)

# 设置项目名称和版本
project(ImageKitDemo VERSION 1.0)

cmake_minimum_required(VERSION 3.20)

# 设置项目名称和版本
project(ImageKitDemo VERSION 1.0)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_CXX_FLAGS "-x objective-c++")

### Emulate toolchain
set(CMAKE_OSX_SYSROOT "iphoneos")
set(CMAKE_XCODE_EFFECTIVE_PLATFORMS "-iphoneos;-iphonesimulator")
set(CMAKE_DEBUG_POSTFIX d)##生成的mtImageKit库文件会为libmtImageKitd.a
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")


# 设置部署目标（根据需要）
set(CMAKE_OSX_DEPLOYMENT_TARGET "12.0")
# 设置目标架构，例如 arm64
set(CMAKE_OSX_ARCHITECTURES "arm64")

if(NOT XCODE_VERSION)
  message(FATAL_ERROR "Xcode only")
endif()
if(IOS_DEPLOYMENT_TARGET VERSION_LESS 12.0)
  set(IOS_DEPLOYMENT_TARGET 12.0)
endif()


set(dirDemo ${PROJECT_SOURCE_DIR}/Test0822)

# 配置并读取源代码文件
if (TRUE) 
    set(DEMO_SOURCES
        ${dirDemo}/*.h
        ${dirDemo}/*.m
        ${dirDemo}/*.mm
    )

    file(GLOB SOURCES ${DEMO_SOURCES})
endif()


##  start : 布局文件
if (TRUE)
    ## 处理 storyboard
    ## 递归查找 dirDemo下所有子目录的　storyboard 布局文件
    file(GLOB_RECURSE DEMO_STORYBOARD "${dirDemo}/*.storyboard")

    ## 处理 xib
    ## 递归查找 dirDemo下所有子目录的　xib 布局文件
    file(GLOB_RECURSE DEMO_XIB "${dirDemo}/*.xib")
endif()
##  end : 布局文件


# --- start 根据需要编译的源码，自动将相应的目录include ---
if (TRUE) 
    set(DEMO_HEADER_DIRs ${dirDemo})
    foreach(item ${SOURCES})
        get_filename_component(subDir ${item} DIRECTORY)
        set(DEMO_HEADER_DIRs ${DEMO_HEADER_DIRs} ${subDir})
    endforeach()
    ## 移除重复的目录
    list(REMOVE_DUPLICATES DEMO_HEADER_DIRs)
    include_directories(${DEMO_HEADER_DIRs}
        ${demoControl_INCLUDE_DIRs}
    )
endif()
# --- end 根据需要编译的源码，自动将相应的目录include ---

if (TRUE) 
    # 添加 Assets.xcassets 资源
    set(dirDemoAssets "${dirDemo}/Assets.xcassets")
endif()

add_executable(${PROJECT_NAME} MACOSX_BUNDLE ${SOURCES} ${DEMO_STORYBOARD} ${DEMO_XIB} ${dirDemoAssets} ${DEMO_BUNDLES})


target_link_libraries(${PROJECT_NAME}
    "-framework UIKit"
    "-framework Foundation"
    "-framework CoreGraphics"
    "-framework Accelerate"
    ${demoControl_LIBS}
)


# 设置 Xcode 属性
set_target_properties(${PROJECT_NAME} PROPERTIES
    MACOSX_BUNDLE_INFO_PLIST ${dirDemo}/Info.plist
    XCODE_ATTRIBUTE_DEVELOPMENT_TEAM ""                             ## 例：VVR83VJ649
    XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "iPhone Developer"
    XCODE_ATTRIBUTE_PRODUCT_BUNDLE_IDENTIFIER "com.meitu.MTIKDemo"   ## 例：com.meitu.mtimagekit
    XCODE_ATTRIBUTE_ASSETCATALOG_COMPILER_APPICON_NAME "AppIcon"
    # XCODE_ATTRIBUTE_ASSETCATALOG_COMPILER_LAUNCHIMAGE_NAME "LaunchImage" ## demo不需要配置LaunchImage
    XCODE_ATTRIBUTE_CLANG_ENABLE_OBJC_ARC YES
    XCODE_ATTRIBUTE_OTHER_LDFLAGS "-Wl,-ld_classic"
)
