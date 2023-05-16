set(STM32F1_HAL_SRC
    Src/stm32f1xx_hal.c
    Src/stm32f1xx_hal_cortex.c
    Src/stm32f1xx_hal_dma.c
    Src/stm32f1xx_hal_exti.c
    Src/stm32f1xx_hal_flash.c
    Src/stm32f1xx_hal_flash_ex.c
    Src/stm32f1xx_hal_gpio.c
    Src/stm32f1xx_hal_gpio_ex.c
    Src/stm32f1xx_hal_pwr.c
    Src/stm32f1xx_hal_rcc.c
    Src/stm32f1xx_hal_rcc_ex.c
    Src/stm32f1xx_hal_spi.c
    Src/stm32f1xx_hal_tim.c
    Src/stm32f1xx_hal_tim_ex.c
    Src/stm32f1xx_hal_uart.c
)

if(USE_HAL_LIB)
set(STM32F1_LIB_SRC ${STM32F1_HAL_SRC})
else()
set(STM32F1_LIB_SRC ${STM32F1_LL_SRC})
endif()

add_library(stm32f1xx_hal ${STM32F1_LIB_SRC})
if(USE_LL_LIB)
    target_compile_definitions(stm32f1xx_hal PUBLIC -DUSE_FULL_LL_DRIVER)
endif()
if(USE_HAL_LIB)
    target_compile_definitions(stm32f1xx_hal PUBLIC -DUSE_HAL_DRIVER)
endif()
target_link_libraries(stm32f1xx_hal PUBLIC cmsis)
target_include_directories(stm32f1xx_hal PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/Inc>
  PRIVATE Src)
