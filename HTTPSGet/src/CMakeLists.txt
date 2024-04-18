add_executable(${NAME}
        main.cpp
        WifiHelper.cpp
        Agent.cpp
        Transport.cpp
        TCPTransport.cpp
        TLSTransBlock.cpp
        TestTrans.cpp
        )

# Pull in our pico_stdlib which pulls in commonly used features
target_link_libraries(${NAME} 
	 pico_stdlib
     FreeRTOS-Kernel-Heap4 # FreeRTOS kernel and dynamic heap
     FREERTOS_PORT
     LWIP_PORT
     pico_cyw43_arch_lwip_sys_freertos
     wolfssl
	)
	
target_include_directories(${NAME} PRIVATE
    ${CMAKE_CURRENT_LIST_DIR}
     )

file(READ "${CMAKE_CURRENT_SOURCE_DIR}/../../c_ssid.txt" SSID)
file(READ "${CMAKE_CURRENT_SOURCE_DIR}/../../c_password.txt" PASSWORD)

# Remove trailing newline characters if present
string(STRIP ${SSID} SSID)
string(STRIP ${PASSWORD} PASSWORD)

# Set the WIFI_SSID and WIFI_PASSWORD environment variables
set(ENV{WIFI_SSID} ${SSID})
set(ENV{WIFI_PASSWORD} ${PASSWORD})

message("WIFI_SSID: $ENV{WIFI_SSID}")
message("WIFI_PASSWORD: $ENV{WIFI_PASSWORD}")

if (NOT DEFINED ENV{WIFI_SSID})
	message(FATAL_ERROR "WIFI_SSID Environment variable not defined")
endif()
if (NOT DEFINED ENV{WIFI_PASSWORD})
	message(FATAL_ERROR "WIFI_PASSWORD Environment variable not defined")
endif()


target_compile_definitions(${NAME} PRIVATE
    WIFI_SSID=\"$ENV{WIFI_SSID}\"
    WIFI_PASSWORD=\"$ENV{WIFI_PASSWORD}\"
    CYW43_HOST_NAME="DrJonEA"
)

# create map/bin/hex file etc.
pico_add_extra_outputs(${NAME})

# enable usb output, disable uart output
pico_enable_stdio_usb(${NAME} 0)
pico_enable_stdio_uart(${NAME} 1)
target_compile_definitions(${NAME} PRIVATE
    PICO_DEFAULT_UART_RX_PIN=16
    PICO_DEFAULT_UART_TX_PIN=17
)