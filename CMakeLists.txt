cmake_minimum_required( VERSION 3.25 )
project( ase )

# Determine if we're built as a subproject (using add_subdirectory)
# or if this is the master project.
set(MASTER_PROJECT OFF)
if( CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR )
	set(MASTER_PROJECT ON)
endif()

### Standard
set( CMAKE_CXX_STANDARD 23 )
set( CMAKE_CXX_STANDARD_REQUIRED ON )
set( CMAKE_CXX_EXTENSIONS OFF )

### Verbosity
set( CMAKE_COLOR_MAKEFILE ON )
set( CMAKE_VERBOSE_MAKEFILE ON )
set( CMAKE_EXPORT_COMPILE_COMMANDS ON )

### Compiler Options

if( MSVC )
	add_compile_options(
		/MP # Parallel builds 
		/permissive- # Stricter C++ conformance

		# Warnings
		/W3

		# Consider these warnings as errors
		/we4018 # 'expression': signed/unsigned mismatch
		/we4062 # Enumerator 'identifier' in a switch of enum 'enumeration' is not handled
		/we4101 # 'identifier': unreferenced local variable
		/we4265 # 'class': class has virtual functions, but destructor is not virtual
		/we4305 # 'context': truncation from 'type1' to 'type2'
		/we4388 # 'expression': signed/unsigned mismatch
		/we4389 # 'operator': signed/unsigned mismatch

		/we4456 # Declaration of 'identifier' hides previous local declaration
		/we4457 # Declaration of 'identifier' hides function parameter
		/we4458 # Declaration of 'identifier' hides class member
		/we4459 # Declaration of 'identifier' hides global declaration

		/we4505 # 'function': unreferenced local function has been removed
		/we4547 # 'operator': operator before comma has no effect; expected operator with side-effect
		/we4549 # 'operator1': operator before comma has no effect; did you intend 'operator2'?
		/we4555 # Expression has no effect; expected expression with side-effect
		/we4715 # 'function': not all control paths return a value
		/we4834 # Discarding return value of function with 'nodiscard' attribute
		/we5038 # data member 'member1' will be initialized after data member 'member2'
		/we5245 # 'function': unreferenced function with internal linkage has been removed

	)
elseif( CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang" )
	# Force colored diagnostic messages in Ninja's output
	if( CMAKE_GENERATOR STREQUAL "Ninja" )
		add_compile_options( -fdiagnostics-color=always )
	endif()

	add_compile_options(
		-Wall
		-Warray-bounds
		-Wextra
		-Wimplicit-fallthrough
		-Wmissing-declarations
		-Wmissing-declarations
		-Wmissing-field-initializers
		-Wno-attributes
		-Wno-invalid-offsetof
		-Wno-unused-parameter
		-Wreorder
		-Wshadow
		-Wsign-compare
		-Wswitch
		-Wuninitialized
		-Wunused-function
		-Wunused-result
		-Wunused-variable
	)
endif()

### Targets
add_library(
	ase STATIC
	source/ase/ase.cpp
)
target_include_directories(
	ase
	PUBLIC
	include
)

### Tests
if( MASTER_PROJECT )
	enable_testing()

	# Copy Sample swatch file to build folder
	file(
		COPY test/SampleSwatch.ase
		DESTINATION ${CMAKE_CURRENT_BINARY_DIR}
	)

	add_executable(
		Parse
		test/Parse.cpp
	)
	target_link_libraries(
		Parse
		PRIVATE
		ase
	)

	add_test(
		NAME Parse
		COMMAND Parse SampleSwatch.ase
	)
endif()