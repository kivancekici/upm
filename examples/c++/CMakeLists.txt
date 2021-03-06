# Selectively disable certain warnings for the examples
# nrf examples flag -Wtautological-compare
set (CXX_DISABLED_WARNINGS -Wno-tautological-compare)

# Extract module name from non-standard example name
macro(get_module_name example_name module_name)
  string(LENGTH ${example_name} length)
  string(FIND ${example_name} "-" index)
  if (${index} GREATER 1)
    string(SUBSTRING ${example_name} 0 ${index} substr)
    set(${module_name} ${substr})
  elseif (${example_name} MATCHES "^grove")
    set (${module_name} "grove")
  elseif ((${example_name} MATCHES "^mq" AND ${length} EQUAL 3) OR ${example_name} STREQUAL "tp401")
    set (${module_name} "gas")
  else()
    set(${module_name} ${example_name})
  endif()
endmacro()

# Set source file, include and linker settings for an example
# If example cannot be built, example_bin is cleared
macro(add_custom_example example_bin example_src example_module_list)
  set(found_all_modules TRUE)
  foreach (module ${example_module_list})
    if (NOT EXISTS "${PROJECT_SOURCE_DIR}/src/${module}")
      set(found_all_modules FALSE)
    endif()
    if (MODULE_LIST)
      list(FIND MODULE_LIST ${module} index)
      if (${index} EQUAL -1)
        set(found_all_modules FALSE)
      endif()
    endif()
  endforeach()
  if (found_all_modules)
    add_executable (${example_bin} ${example_src})
    target_link_libraries (${example_bin} ${CMAKE_THREAD_LIBS_INIT})

    # Disable warnings from CXX_DISABLED_WARNINGS
    foreach(flag ${CXX_DISABLED_WARNINGS})
      compiler_flag_supported(CXX is_supported ${flag})
      if (is_supported)
        target_compile_options(${example_bin} PRIVATE "${flag}")
      endif(is_supported)
    endforeach(flag ${CXX_DISABLED_WARNINGS})

    foreach (module ${example_module_list})
      set(module_dir "${PROJECT_SOURCE_DIR}/src/${module}")
      include_directories (${module_dir})
      if (${module} STREQUAL "lcd")
        set(module "i2clcd")
      endif()
      target_link_libraries (${example_bin} ${module})
    endforeach()
  else()
    message (WARNING "Ignored ${example_bin}")
    set (example_bin "")
  endif()
endmacro()


# Add specified example by name
# Note special case for grove based examples
macro(add_example example_name)
  set(example_src "${example_name}.cxx")
  set(example_bin "${example_name}-example-cxx")
  get_module_name(${example_name} module_name)
  set(module_dir "${PROJECT_SOURCE_DIR}/src/${module_name}")

  if (EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/${example_src}"
     AND EXISTS ${module_dir}
     AND IS_DIRECTORY ${module_dir})
    add_custom_example(${example_bin} ${example_src} ${module_name})
    if ((NOT ${example_bin} STREQUAL "") AND (${module_name} STREQUAL "grove"))
      set(grove_module_path "${PROJECT_SOURCE_DIR}/src/${example_name}")
      if (EXISTS ${grove_module_path})
        include_directories(${grove_module_path})
        target_link_libraries (${example_bin} ${example_name})
      endif()
    endif()
  else()
    message (WARNING "Ignored ${example_bin}")
  endif()
endmacro()


set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/examples)

# UPM c include directories
include_directories (${PROJECT_SOURCE_DIR}/include
                     ${CMAKE_SOURCE_DIR}/src/utilities)

# Set the mraa include and link directories prior to adding examples
include_directories (${MRAA_INCLUDE_DIRS})
link_directories (${MRAA_LIBDIR})

# If your sample source file matches the name of the module it tests, add it here
# Exceptions are as follows:
#  string after first '-' is ignored (e.g. nrf24l01-transmitter maps to nrf24l01)
#  mq? will use module gas
#  grove* will use module grove
add_example (hmc5883l)
add_example (led)
add_example (groveled)
add_example (relay)
add_example (groverelay)
add_example (light)
add_example (grovelight)
add_example (temperature)
add_example (grovetemp)
add_example (button)
add_example (grovebutton)
add_example (rotary)
add_example (groverotary)
add_example (slide)
add_example (groveslide)
add_example (buzzer)
add_example (nrf24l01-transmitter)
add_example (nrf24l01-receiver)
add_example (nrf24l01-broadcast)
add_example (hcsr04)
add_example (max44000)
add_example (mma7455)
add_example (st7735)
add_example (max31855)
add_example (bmpx8x)
add_example (stepmotor)
add_example (pulsensor)
add_example (mic)
add_example (mpu9150)
add_example (maxds3231m)
add_example (max31723)
add_example (max5487)
add_example (nrf8001-broadcast)
add_example (nrf8001-helloworld)
add_example (lpd8806)
add_example (mlx90614)
add_example (ecs1030)
add_example (mq2)
add_example (mq3)
add_example (mq4)
add_example (mq5)
add_example (mq6)
add_example (mq7)
add_example (mq8)
add_example (mq9)
add_example (tp401)
add_example (tcs3414cs)
add_example (th02)
add_example (ttp223)
add_example (lsm303)
add_example (joystick12)
add_example (lol)
add_example (tsl2561)
add_example (htu21d)
add_example (mpl3115a2)
add_example (ldt0028)
add_example (am2315)
add_example (itg3200)
add_example (enc03r)
add_example (adc121c021)
add_example (ds1307)
add_example (a110x)
add_example (gp2y0a)
add_example (moisture)
add_example (grovemoisture)
add_example (ehr)
add_example (groveehr)
add_example (ta12200)
add_example (grovelinefinder)
add_example (linefinder)
add_example (vdiv)
add_example (grovevdiv)
add_example (water)
add_example (grovewater)
add_example (guvas12d)
add_example (mpr121)
add_example (yg1006)
add_example (wt5001)
add_example (ppd42ns)
add_example (mq303a)
add_example (speaker)
add_example (grovespeaker)
add_example (rfr359f)
add_example (biss0001)
add_example (rotaryencoder)
add_example (adxl345)
add_example (rpr220)
add_example (rpr220-intr)
add_example (mma7660)
add_example (cjq4435)
add_example (adxl335)
add_example (hmtrp)
add_example (nunchuck)
add_example (otp538u)
add_example (collision)
add_example (grovecollision)
add_example (electromagnet)
add_example (groveelectromagnet)
add_example (emg)
add_example (groveemg)
add_example (o2)
add_example (groveo2)
add_example (gsr)
add_example (grovegsr)
add_example (ina132)
add_example (l298)
add_example (l298-stepper)
add_example (at42qt1070)
add_example (grovemd)
add_example (md)
add_example (grovemd-stepper)
add_example (md-stepper)
add_example (pca9685)
add_example (eldriver)
add_example (groveeldriver)
add_example (adafruitss)
add_example (adafruitms1438)
add_example (adafruitms1438-stepper)
add_example (hx711)
add_example (flex)
add_example (a110x-intr)
add_example (mhz16)
add_example (apds9002)
add_example (waterlevel)
add_example (tm1637)
add_example (zfm20)
add_example (zfm20-register)
add_example (uln200xa)
add_example (grovewfs)
add_example (wfs)
add_example (isd1820)
add_example (sx6119)
add_example (si114x)
add_example (maxsonarez)
add_example (hm11)
add_example (ht9170)
add_example (h3lis331dl)
add_example (ad8232)
add_example (grovescam)
add_example (scam)
add_example (m24lr64e)
add_example (rgbringcoder)
add_example (hp20x)
add_example (pn532)
add_example (pn532-writeurl)
add_example (lsm9ds0)
add_example (loudness)
add_example (mg811)
add_example (wheelencoder)
add_example (sm130)
add_example (gprs)
add_example (grovegprs)
add_example (lm35)
add_example (micsv89)
add_example (xbee)
add_example (urm37)
add_example (urm37-uart)
add_example (adxrs610)
add_example (bma220)
add_example (dfrph)
add_example (mcp9808)
add_example (groveultrasonic)
add_example (ultrasonic)
add_example (sx1276-lora)
add_example (sx1276-fsk)
add_example (ili9341)
add_example (nlgpio16)
add_example (ads1x15)
if (MODBUS_FOUND)
  include_directories(${MODBUS_INCLUDE_DIRS})
  add_example (t3311)
  add_example (hwxpxx)
  add_example (h803x)
endif()
add_example (hdxxvxta)
add_example (rhusb)
add_example (apds9930)
add_example (kxcjk1013)
add_example (ssd1351)
add_example (ds1808lc)
add_example (hlg150h)
add_example (lp8860)
add_example (max44009)
add_example (si1132)
add_example (si7005)
add_example (t6713)
add_example (cwlsxxa)
add_example (teams)
add_example (apa102)
add_example (tex00)
add_example (bmi160)
add_example (smartdrive)
if (HAVE_FIRMATA)
  add_example (curieimu)
endif ()
if (BACNET_FOUND)
  include_directories(${BACNET_INCLUDE_DIRS})
  # we need access to bacnetmstp headers too
  include_directories(${PROJECT_SOURCE_DIR}/src/bacnetmstp)
  add_example (e50hx)
  add_example (t8100)
  add_example (tb7300)
endif()
if (JPEG_FOUND)
  add_example (vcap)
endif (JPEG_FOUND)
add_example (ds2413)
add_example (ds18b20)
add_example (bmp280)
add_example (bno055)
add_example (l3gd20)
add_example (l3gd20-i2c)
add_example (bmx055)
add_example (ms5611)
add_example (nmea_gps)
add_example (mma7361)
add_example (bh1750)
add_example (hka5)
add_example (dfrorp)
add_example (dfrec)
add_example (sht1x)
add_example (ms5803)
add_example (ims)

# These are special cases where you specify example binary, source file and module(s)
include_directories (${PROJECT_SOURCE_DIR}/src)
add_custom_example (groveled-multi-example-cxx groveled-multi.cxx grove)
add_custom_example (lcm1602-i2c-example-cxx lcm1602-i2c.cxx lcd)
add_custom_example (lcm1602-parallel-example-cxx lcm1602-parallel.cxx lcd)
add_custom_example (jhd1313m1-lcd-example-cxx jhd1313m1-lcd.cxx lcd)
add_custom_example (es08a-example-cxx es08a.cxx servo)
add_custom_example (ssd1306-oled-example-cxx ssd1306-oled.cxx lcd)
add_custom_example (ssd1308-oled-example-cxx ssd1308-oled.cxx lcd)
add_custom_example (ssd1327-oled-example-cxx ssd1327-oled.cxx lcd)
add_custom_example (sainsmartks-example-cxx sainsmartks.cxx lcd)
add_custom_example (eboled-example-cxx eboled.cxx lcd)
add_custom_example (mpu60x0-example-cxx mpu60x0.cxx mpu9150)
add_custom_example (ak8975-example-cxx ak8975.cxx mpu9150)
add_custom_example (mpu9250-example-cxx mpu9250.cxx mpu9150)
add_custom_example (groveledbar-example-cxx groveledbar.cxx my9221)
add_custom_example (grovecircularled-example-cxx grovecircularled.cxx my9221)
add_custom_example (temperature-sensor-example-cxx temperature-sensor.cxx "si7005;bmpx8x;bmp280")
add_custom_example (humidity-sensor-example-cxx humidity-sensor.cxx "si7005;bmp280")
add_custom_example (pressure-sensor-example-cxx pressure-sensor.cxx "bmpx8x;bmp280")
add_custom_example (co2-sensor-example-cxx co2-sensor.cxx "t6713")
add_custom_example (adc-example-cxx adc-sensor.cxx "ads1x15")
add_custom_example (light-sensor-example-cxx light-sensor.cxx "si1132;max44009")
add_custom_example (light-controller-example-cxx light-controller.cxx "lp8860;ds1808lc;hlg150h")
add_custom_example (bme280-example-cxx bme280.cxx bmp280)
add_custom_example (bma250e-example-cxx bma250e.cxx bmx055)
add_custom_example (bmg160-example-cxx bmg160.cxx bmx055)
add_custom_example (bmm150-example-cxx bmm150.cxx bmx055)
add_custom_example (bmc150-example-cxx bmc150.cxx bmx055)
add_custom_example (bmi055-example-cxx bmi055.cxx bmx055)
if (OPENZWAVE_FOUND)
  include_directories(${OPENZWAVE_INCLUDE_DIRS})

  add_custom_example (ozwdump-example-cxx ozwdump.cxx ozw)
  add_custom_example (aeotecss6-example-cxx aeotecss6.cxx ozw)
  add_custom_example (aeotecsdg2-example-cxx aeotecsdg2.cxx ozw)
  add_custom_example (aeotecdw2e-example-cxx aeotecdw2e.cxx ozw)
  add_custom_example (aeotecdsb09104-example-cxx aeotecdsb09104.cxx ozw)
  add_custom_example (tzemt400-example-cxx tzemt400.cxx ozw)
endif()
add_custom_example (nmea_gps_i2c_example-cxx nmea_gps_i2c.cxx nmea_gps)
