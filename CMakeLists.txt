#############################################################################################################################
# file:  CMakeLists.txt
# brief: Template "CMakeLists.txt" for building of executables and static libraries.
#
# usage: Edit "VARIABLES"-section to suit project requirements.
#        For debug build:
#          cmake -DCMAKE_TOOLCHAIN_FILE=cubeide-gcc.cmake  -S ./ -B Debug -G"Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug
#          make -C Debug VERBOSE=1
#        For release build:
#          cmake -DCMAKE_TOOLCHAIN_FILE=cubeide-gcc.cmake  -S ./ -B Release -G"Unix Makefiles" -DCMAKE_BUILD_TYPE=Release
#          make -C Release VERBOSE=1
#############################################################################################################################
cmake_minimum_required(VERSION 3.20)

set(CMAKE_VERBOSE_MAKEFILE 				ON)
set(CMAKE_TOOLCHAIN_FILE 				${CMAKE_SOURCE_DIR}/cubeide-gcc.cmake)
enable_language(C CXX ASM)
set(CMAKE_C_STANDARD 17)
set(CMAKE_CXX_STANDARD 17)
###################### CONSTANTS ######################################
set (PROJECT_TYPE_EXECUTABLE          	"exe")
set (PROJECT_TYPE_STATIC_LIBRARY      	"static-lib")
set (MCPU_CORTEX_M0				      	"-mcpu=cortex-m0")
set (MCPU_CORTEX_M0PLUS				  	"-mcpu=cortex-m0plus")
set (MCPU_CORTEX_M3				      	"-mcpu=cortex-m3")
set (MCPU_CORTEX_M4				      	"-mcpu=cortex-m4")
set (MCPU_CORTEX_M7				      	"-mcpu=cortex-m7")
set (MCPU_CORTEX_M33				  	"-mcpu=cortex-m33")
set (MCPU_CORTEX_M55				  	"-mcpu=cortex-m55")
set (MCPU_CORTEX_M85				  	"-mcpu=cortex-m85")
set (MFPU_FPV4_SP_D16                 	"-mfpu=fpv4-sp-d16")
set (MFPU_FPV5_D16                    	"-mfpu=fpv5-d16")
set (RUNTIME_LIBRARY_REDUCED_C        	"--specs=nano.specs")
set (RUNTIME_LIBRARY_STD_C            	"")
set (RUNTIME_LIBRARY_SYSCALLS_MINIMAL 	"--specs=nosys.specs")
set (RUNTIME_LIBRARY_SYSCALLS_NONE    	"")
set (MFLOAT_ABI_SOFTWARE              	"-mfloat-abi=soft")
set (MFLOAT_ABI_HARDWARE              	"-mfloat-abi=hard")
set (MFLOAT_ABI_MIX                   	"-mfloat-abi=softfp")
#######################################################################

###################### VARIABLES ######################################
set (PROJECT_NAME             			"l4")
set (PROJECT_TYPE             			"exe")
set (LINKER_SCRIPT            			"../STM32L4R5ZITX_FLASH.ld")
set (MCPU                     			"-mcpu=Cortex-M4")
set (MFPU                 				"-mfpu=fpv4-sp-d16")
set (MFLOAT_ABI               			"")
set (RUNTIME_LIBRARY          			${RUNTIME_LIBRARY_REDUCED_C})
set (RUNTIME_LIBRARY_SYSCALLS 			${RUNTIME_LIBRARY_SYSCALLS_MINIMAL})


set (PROJECT_SOURCES
	# LIST SOURCE FILES HERE
	${CMAKE_SOURCE_DIR}/Core/Startup/startup_stm32l4r5zitx.s
	${CMAKE_SOURCE_DIR}/Core/Src/main.c
	${CMAKE_SOURCE_DIR}/Core/Src/stm32l4xx_hal_msp.c
	${CMAKE_SOURCE_DIR}/Core/Src/stm32l4xx_it.c
	${CMAKE_SOURCE_DIR}/Core/Src/syscalls.c
	${CMAKE_SOURCE_DIR}/Core/Src/sysmem.c
	${CMAKE_SOURCE_DIR}/Core/Src/system_stm32l4xx.c
	${CMAKE_SOURCE_DIR}/Drivers/SEGGER_RTT/RTT/SEGGER_RTT.c
	${CMAKE_SOURCE_DIR}/Drivers/SEGGER_RTT/RTT/SEGGER_RTT_printf.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_cortex.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_dma_ex.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_dma.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_exti.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_flash_ex.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_flash_ramfunc.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_flash.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_gpio.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_i2c.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_pwr_ex.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_pwr.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_rcc_ex.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal_rcc.c
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Src/stm32l4xx_hal.c
	)

set (PROJECT_DEFINES
	# LIST COMPILER DEFINITIONS HERE
	STM32L4R5xx
	USE_HAL_DRIVER
	DEBUG
    )

set (PROJECT_INCLUDES
	# LIST INCLUDE DIRECTORIES HERE
	${CMAKE_SOURCE_DIR}/Core/Inc
	${CMAKE_SOURCE_DIR}/Drivers/SEGGER_RTT/RTT
	${CMAKE_SOURCE_DIR}/Drivers/SEGGER_RTT/Config
	${CMAKE_SOURCE_DIR}/Drivers/STM32L4xx_HAL_Driver/Inc
	${CMAKE_SOURCE_DIR}/Drivers/CMSIS/Include
	${CMAKE_SOURCE_DIR}/Drivers/CMSIS/Device/ST/STM32L4xx/Include
    )

############ MODIFY ACCORDING TO REQUIREMENTS) ########################

#######################################################################

################## PROJECT SETUP ######################################
project(${PROJECT_NAME})
enable_language(ASM)

if (${PROJECT_TYPE} MATCHES ${PROJECT_TYPE_EXECUTABLE})
  add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})
  add_custom_command(TARGET ${CMAKE_PROJECT_NAME} POST_BUILD COMMAND ${CMAKE_SIZE} $<TARGET_FILE:${CMAKE_PROJECT_NAME}>)
elseif (${PROJECT_TYPE} MATCHES ${PROJECT_TYPE_STATIC_LIBRARY})
  add_library(${PROJECT_NAME} ${PROJECT_SOURCES})
endif()

add_compile_definitions (${PROJECT_DEFINES})
include_directories (${PROJECT_INCLUDES})

set (CMAKE_EXECUTABLE_SUFFIX ".elf")
set (CMAKE_STATIC_LIBRARY_SUFFIX ".a")
set (CMAKE_C_FLAGS "${MCPU} ${MFPU} ${MFLOAT_ABI} ${RUNTIME_LIBRARY} -mthumb -Wall -Werror")
set (CMAKE_CXX_FLAGS "${MCPU} ${MFPU} ${MFLOAT_ABI} ${RUNTIME_LIBRARY} -mthumb -Wall -Werror")
set (CMAKE_EXE_LINKER_FLAGS "-T${LINKER_SCRIPT} ${RUNTIME_LIBRARY_SYSCALLS} -Wl,-Map=test.map -Wl,--gc-sections -static -Wl,--start-group -lc -lm -Wl,--end-group")
set (CMAKE_ASM_FLAGS "${CMAKE_C_FLAGS} -x assembler-with-cpp")
