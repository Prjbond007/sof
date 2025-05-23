# SPDX-License-Identifier: BSD-3-Clause

is_zephyr(zephyr)
if(zephyr) ###  Zephyr ###

  if(CONFIG_DTS_CODEC STREQUAL "m")
    add_subdirectory(dts/llext ${PROJECT_BINARY_DIR}/dts_llext)
    add_dependencies(app dts)

  else(CONFIG_DTS_CODEC)
    zephyr_library_sources(dts/dts.c)
    if (CONFIG_DTS_CODEC_STUB)
      zephyr_library_sources(dts/dts_stub.c)
    else()
      zephyr_library_import(DtsCodec
        ${sof_top_dir}/third_party/lib/libdts-sof-interface-i32.a)
    endif()
  endif()

  return()
endif()

### XTOS ###
if(CONFIG_DTS_CODEC)
  add_local_sources(sof dts/dts.c)
  target_compile_definitions(sof PRIVATE -DDTS_MATH_INT32 -DDTS_XTENSA)

  if (CONFIG_DTS_CODEC_STUB)
    add_local_sources(sof dts/dts_stub.c)
  else()
    sof_add_static_library(DtsCodec
      ${SOF_ROOT_SOURCE_DIRECTORY}/third_party/lib/libdts-sof-interface-i32.a)
    target_include_directories(sof PRIVATE ${CMAKE_SOURCE_DIR}/third_party/include)
  endif()

endif()
