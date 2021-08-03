set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.20)

# specify cross compilers and tools
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER  arm-none-eabi-gcc)
set(CMAKE_AR arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

project(MSP432_Cmake C CXX ASM)

set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/msp432p401r.lds)

add_definitions(-D__MSP432P401R__ -Dgcc)
add_compile_options(-mcpu=cortex-m4 -march=armv7e-m -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -Og -g -gdwarf-3 -gstrict-dwarf -Wall -MMD -MP )

add_link_options(-Og -g -gdwarf-3 -gstrict-dwarf -Wall -march=armv7e-m -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -mthumb -g -gstrict-dwarf -Wall -Wl,--start-group -lgcc -lnosys -lc -Wl,--end-group )
add_link_options(-T ${LINKER_SCRIPT})

include_directories(D:\\softwares\\TI\\simplelink_msp432p4_sdk_3_40_01_02\\source\\ti
        D:\\softwares\\TI\\simplelink_msp432p4_sdk_3_40_01_02\\source
        D:\\softwares\\TI\\CCS\\ccs\\ccs_base\\arm\\include
        D:\\softwares\\TI\\CCS\\ccs\\ccs_base\\arm\\include\\CMSIS)

add_executable(${PROJECT_NAME}.elf main.c system_msp432p401r.c startup_msp432p401r_gcc.c)

target_link_libraries(${PROJECT_NAME}.elf "D:\\\\softwares\\\\TI\\\\simplelink_msp432p4_sdk_3_40_01_02\\\\source\\\\ti\\\\devices\\\\msp432p4xx\\\\driverlib\\\\gcc\\\\msp432p4xx_driverlib.a")

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")
