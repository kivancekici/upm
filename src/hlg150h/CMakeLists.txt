include_directories(..)
set (libname "hlg150h")
set (libdescription "Lighting power supply")
set (module_src ${libname}.cxx mraa-utils.cxx)
set (module_hpp ${libname}.hpp)
upm_module_init()
