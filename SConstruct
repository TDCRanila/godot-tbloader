#!/usr/bin/env python
import os
import sys

env = SConscript("godot-cpp/SConstruct")

# For the reference:
# - CCFLAGS are compilation flags shared between C and C++
# - CFLAGS are for C-specific compilation flags
# - CXXFLAGS are for C++-specific compilation flags
# - CPPFLAGS are for pre-processor flags
# - CPPDEFINES are for pre-processor defines
# - LINKFLAGS are for linking flags

# Default num_jobs to local cpu count if not user specified.
# SCons has a peculiarity where user-specified options won't be overridden
# by SetOption, so we can rely on this to know if we should use our default.
initial_num_jobs = env.GetOption("num_jobs")
altered_num_jobs = initial_num_jobs + 1
env.SetOption("num_jobs", altered_num_jobs)
if env.GetOption("num_jobs") == altered_num_jobs:
    cpu_count = os.cpu_count()
    if cpu_count is None:
        print("Couldn't auto-detect CPU count to configure build parallelism. Specify it with the -j argument.")
    else:
        safer_cpu_count = cpu_count if cpu_count <= 4 else cpu_count - 1
        print(
            "Auto-detected %d CPU cores available for build parallelism. Using %d cores by default. You can override it with the -j argument."
            % (cpu_count, safer_cpu_count)
        )
        env.SetOption("num_jobs", safer_cpu_count)

# tweak this if you want to use different folders, or more folders, to store your source code in.
env.Append(CPPPATH=["src/", "src/map/"])
sources = Glob("src/*.cpp")
sources += Glob("src/builders/*.cpp")
sources += Glob("src/map/*.cpp")

if env["platform"] == "windows":
	env.Append(LINKFLAGS=["/DEBUG"])

if env["platform"] == "osx":
	library = env.SharedLibrary(
		"addons/tbloader/bin/libtbloader.{}.framework/libtbloader.{}".format(
			env["platform"], env["platform"]
		),
		source=sources,
	)
else:
	library = env.SharedLibrary(
		"addons/tbloader/bin/tbloader.{}.{}{}".format(
			env["platform"], env["arch_suffix"], env["SHLIBSUFFIX"]
		),
		source=sources,
	)

Default(library)
