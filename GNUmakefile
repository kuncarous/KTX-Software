#
# run GYP to generate various project files.
#
# Use this because GYP does not have a way to set its options within
# the GYP file.
#
# © 2015 Mark Callow.
#
# TODO: Clean up this makefile and try using gypi files with things like
# {
#   'GYP_DEFINES:' 'OS=ios',
#   'GYP_GENERATORS:' 'msvs',
#   'GYP_GENERATOR_FLAGS:' 'msvs_version=2010'
# }

pname := ktx
# Formats to generate by default.
formats=msvs xcode # make

builddir := build

# GYP only updates solution & project files that have changed since
# the last time they were generated. It is impractical to list
# as dependencies herein all the output files of a given GYP generator
# so use a file with the timestamp of the latest generation to ensure
# gyp is only run when necessary.
stampfile := .${pname}-stamp

msvs_buildd := $(builddir)/msvs
msvs_platforms := win# web wingl
msvs_vernames := vs2008 vs2010 vs2010e vs2013 vs2013e vs2015
# Build list of names "${platform}/vs{2010,2010e,2008}/.ktx-stamp"
msvs_targets = $(addprefix ${platform}/,$(addsuffix /${stampfile},${msvs_vernames}))
msvs_platform_dirs := $(addprefix ${msvs_buildd}/,${msvs_platforms})
# Expand the ${platform} in each of name in msvs_targets
msvs_targets := $(foreach platform,${msvs_platform_dirs},${msvs_targets})

xcode_buildd := $(builddir)/xcode
xcode_platforms := ios mac
xcode_targets = $(addsuffix /${stampfile},$(addprefix ${xcode_buildd}/,${xcode_platforms}))

cmake_buildd := $(builddir)/cmake
cmake_platforms := linux# mac
cmake_targets = $(addsuffix /${stampfile},$(addprefix ${cmake_buildd}/,${cmake_platforms}))

make_buildd := $(builddir)/make
make_platforms := linux# mac
make_targets = $(addsuffix /${stampfile},$(addprefix ${make_buildd}/,${make_platforms}))

# Used by msvs recipe to extract version number from the partial target
# name set in $* by the pattern rule. Loops over the list of platform
# names attempting to match and delete it. if findstring is necessary
# because subst returns the full string in the event of no match.
msvs_version=$(strip $(foreach platform,${msvs_platforms},$(if $(findstring ${platform},$*),$(subst ${platform}/vs,,$*))))

# Returns ktxtools.gyp if the OS set in $* is a desktop OS, otherwise an
# empty string.
ktxtools.gyp=$(if $(or $(findstring linux,$*),$(findstring mac,$*),$(findstring win,$*)),ktxtools.gyp)

gypfiles=ktxtests.gyp \
		 ktxtools.gyp \
		 libktx.gyp \
		 gyp_include/adrenoemu.gypi \
		 gyp_include/angle.gypi \
		 gyp_include/config.gypi \
		 gyp_include/default.gypi \
		 gyp_include/libgl.gypi \
		 gyp_include/libgles1.gypi \
		 gyp_include/libgles2.gypi \
		 gyp_include/libgles3.gypi \
		 gyp_include/libsdl.gypi \
		 gyp_include/maliemu.gypi \
		 gyp_include/pvremu.gypi \
		 lib/libktx.gypi \
		 tests/tests.gypi \
		 tests/loadtests/appfwSDL/appfwSDL.gypi \
		 tests/loadtests/loadtests.gypi \
		 tools/tools.gypi \
		 tools/toktx/toktx.gypi

# Uncomment these 2 lines if you do not want to install our modified
# GYP (i.e. run setup.py install). Set gypdir to the directory
# containing the modified GYP. PYTHONPATH is inserted at start of
# python's search path thus the second line ensures the modified
# GYP is used.
#gypdir=tools/gyp/
#export PYTHONPATH=$(gypdir)pylib

# Normal use
gyp=$(gypdir)gyp# --debug=all

# Use this to start in the Python debugger
#gyp=python -m pdb $(gypdir)gyp_main.py

# Use this if running in a Mac OS X terminal with the current
# directory set to an SVN workarea in Windows that is being
# viewed via a Parallels (or VMWare?) VM. The explicit python
# command is necessary because the gyp shell script does not
# appear have execute permission in this case.
#gyp=python $(gypdir)/gyp_main.py

.PHONY: msvs xcode default

default:
	@echo Pick one of "\"make {all,cmake,make,msvs,xcode}\""

all: $(formats)

msvs: $(msvs_targets)

xcode: $(xcode_targets)

cmake: $(cmake_targets)

make: $(make_targets)

#android: $(android_targets)

# Can't use wildcards in target patterns so have to match the whole
# {win+web,wingl}/vs<version> part of the target name. Uses the
# msvs_version macro above to extract the version.
$(msvs_targets): $(msvs_buildd)/%/$(stampfile): GNUmakefile $(gypfiles)
	$(gyp) -f msvs -G msvs_version=$(msvs_version) --generator-output=$(dir $@) --depth=. ktxtests.gyp ktxtools.gyp
	@date -R > $@

$(xcode_targets): $(xcode_buildd)/%/$(stampfile): GNUmakefile $(gypfiles)
	$(gyp) -f xcode -DOS=$* --generator-output=$(dir $@) --depth=. ktxtests.gyp $(ktxtools.gyp)
	@date -R > $@

$(cmake_targets): $(cmake_buildd)/%/$(stampfile): GNUmakefile $(gypfiles)
	$(gyp) -f cmake -DOS=$* --generator-output=$(dir $@) -G output_dir=. --depth=. ktxtests.gyp $(ktxtools.gyp)
	@date -R > $@

$(make_targets): $(make_buildd)/%/$(stampfile): GNUmakefile $(gypfiles)
	$(gyp) -f make -DOS=$* --generator-output=$(dir $@) --depth=. ktxtests.gyp $(ktxtools.gyp)
	@date -R > $@

# vim:ai:noexpandtab:ts=4:sts=4:sw=2:textwidth=75
