# Description

This page will description the Kconfig how to use.

This Kconfig reference the: https://github.com/Neutree/c_cpp_project_framework and modify to adaptation my project.

The follow will description several folders to make the user can be understand their purpose.

## menuconfig

The menuconfig.cmake file, which control Kconfig setting and generate the library. If you want to use the Kconfig, you must copy the menuconfig folder to your project and call the menuconfig.cmake in the project root folder CMakeLists.txt file.

### scripts

In this folder, there are three files. The generateBuildInfo.py will control the build project time and build version infomations. The generateConfigFile.py will generate the global config files by your Kconfig configuration. menuconfigProject.py will control the Kconfig support commands.

## config

In this folder, which include two files, menuconfigDefaults.mk and .config.mk. The first file set the Kconfig default value, it will enable/disable target menu item when you run "meke menuconfig".

## Demo

In this folder, include a demo, which show how set the menu item. You can reference to modify your project to support menuconfig feature.