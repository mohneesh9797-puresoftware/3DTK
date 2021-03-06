if(WITH_PYTHON)
	#if(WIN32)
	#	find_package(Boost 1.45.0 COMPONENTS "python${version}" REQUIRED)
	#elseif (Boost_VERSION LESS "106700")
	#	# the following will issue a warning
	#	# see: https://gitlab.kitware.com/cmake/cmake/issues/16335
	#else()
	if(${CMAKE_VERSION} VERSION_LESS "3.12")
		# FindPythonLibs has been deprecated since CMake 3.12
		# these CMake versions do not ship with FindPython.cmake
		find_package(PythonLibs 3 REQUIRED)
		set(version ${PYTHONLIBS_VERSION_STRING})
		STRING( REGEX REPLACE "([0-9.]+).[0-9]+(rc[0-9]+)?" "\\1" version ${version} )
		STRING( REGEX REPLACE "[^0-9]" "" version ${version} )
		set(pyincl ${PYTHON_INCLUDE_DIRS})
		set(pylibs ${PYTHON_LIBRARIES})
	else()
		# FindPython3 is new in CMake 3.12
		find_package(Python3 REQUIRED COMPONENTS Interpreter Development)
		set(version ${Python3_VERSION_MAJOR}${Python3_VERSION_MINOR})
		set(pyincl ${Python3_INCLUDE_DIRS})
		set(pylibs ${Python3_LIBRARIES})
	endif()
	message(STATUS "found python version: ${version}")
	# since CMP0093 Boost_VERSION has a different meaning...
	if (Boost_VERSION LESS "106700" AND NOT DEFINED Boost_VERSION_MACRO)
		find_package(Boost COMPONENTS "python-py${version}" REQUIRED)
	else()
		find_package(Boost COMPONENTS python${version} REQUIRED)
	endif()
	add_subdirectory(python)
endif()
