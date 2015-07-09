==============================
Overview of generic easyblocks
==============================

.. BinariesTarball:
``BinariesTarball``
===================

(derives from :ref:`Tarball`)

Support for installing a tarball of binaries


Customised steps in ``BinariesTarball`` easyblock
-------------------------------------------------
* ``build_step`` - Dummy build method: nothing to build (inherited)
* ``configure_step`` - Dummy configure method (inherited)
* ``test_step`` - Dummy configure method (inherited)

.. Binary:
``Binary``
==========

(derives from EasyBlock)

Support for installing software that comes in binary form.
    Just copy the sources to the install dir, or use the specified install command.

Extra easyconfig parameters specific to ``Binary`` easyblock
------------------------------------------------------------

====================   ===============================================================   =============
easyconfig parameter   description                                                       default value
====================   ===============================================================   =============
``staged_install``     Perform staged installation via subdirectory of build directory   ``False``
``install_cmd``        Install command to be used.                                       ``None``
====================   ===============================================================   =============


Customised steps in ``Binary`` easyblock
----------------------------------------
* ``build_step`` - No compilation, this is binary software
* ``configure_step`` - No configuration, this is binary software
* ``test_step`` - Run unit tests provided by software (if any). (inherited)


Example for ``Binary`` easyblock
--------------------------------

::

    easyblock = 'Binary'

    name = 'Platanus'
    version = '1.2.1'
    versionsuffix = '-linux-x86_64'

    homepage = 'http://platanus.bio.titech.ac.jp/'
    description = """PLATform for Assembling NUcleotide Sequences"""

    toolchain = {'name': 'dummy', 'version': 'dummy'}

    source_urls = ['http://platanus.bio.titech.ac.jp/Platanus_release/20130901010201']
    sources = ['platanus']
    checksums = ['02cf92847ec704d010a54df293b9c60a']

    sanity_check_paths = {
    'files': ['platanus'],
    'dirs': [],
    }

    moduleclass = 'bio'


.. Bundle:
``Bundle``
==========

(derives from EasyBlock)

Bundle of modules: only generate module files, nothing to build/install


Customised steps in ``Bundle`` easyblock
----------------------------------------
* ``build_step`` - Do nothing.
* ``configure_step`` - Do nothing.
* ``test_step`` - Run unit tests provided by software (if any). (inherited)


Example for ``Bundle`` easyblock
--------------------------------

::

    easyblock = 'Bundle'

    name = 'Autotools'
    version = '20150119' # date of the most recent change

    homepage = 'http://autotools.io'
    description = """This bundle collect the standard GNU build tools: Autoconf, Automake and libtool"""

    toolchain = {'name': 'GCC', 'version': '4.9.2'}

    dependencies = [
    ('Autoconf', '2.69'), # 20120424
    ('Automake', '1.15'), # 20150105
    ('libtool', '2.4.5'), # 20150119
    ]

    moduleclass = 'devel'


.. CMakeMake:
``CMakeMake``
=============

(derives from :ref:`ConfigureMake`)

Support for configuring build with CMake instead of traditional configure script

Extra easyconfig parameters specific to ``CMakeMake`` easyblock
---------------------------------------------------------------

========================   =====================================================   ===============
easyconfig parameter       description                                             default value
========================   =====================================================   ===============
``configure_cmd_prefix``   Prefix to be glued before ./configure                   ``""``
``prefix_opt``             Prefix command line option for configure script         ``"--prefix="``
``tar_config_opts``        Override tar settings as determined by configure.       ``False``
``separate_build_dir``     Perform build in a separate directory                   ``False``
``srcdir``                 Source directory location to provide to cmake command   ``None``
========================   =====================================================   ===============


Customised steps in ``CMakeMake`` easyblock
-------------------------------------------
* ``build_step`` - Start the actual build
        - typical: make -j X (inherited)
* ``configure_step`` - Configure build using cmake (inherited)
* ``test_step`` - Test the compilation
        - default: None (inherited)


Example for ``CMakeMake`` easyblock
-----------------------------------

::

    easyblock = 'CMakeMake'

    name = 'ANTs'
    version = '2.1.0rc3'

    homepage = 'http://stnava.github.io/ANTs/'
    description = """ANTs extracts information from complex datasets that include imaging. ANTs is useful for managing,
    interpreting and visualizing multidimensional data."""

    toolchain = {'name': 'goolf', 'version': '1.5.14'}
    toolchainopts = {'pic': True}

    source_urls = ['https://github.com/stnava/ANTs/archive/']
    sources = ['v%(version)s.tar.gz']

    builddependencies = [('CMake', '3.0.2')]

    skipsteps = ['install']
    buildopts = ' && mkdir -p %(installdir)s && cp -r * %(installdir)s/'

    parallel = 1

    separate_build_dir = True

    sanity_check_paths = {
    'files': ['bin/ANTS'],
    'dirs': ['lib'],
    }

    moduleclass = 'data'


.. CMakePythonPackage:
``CMakePythonPackage``
======================

(derives from :ref:`CMakeMake`, :ref:`PythonPackage`)

Build a Python package and module with cmake.

    Some packages use cmake to first build and install C Python packages
    and then put the Python package in lib/pythonX.Y/site-packages.

    We install this in a seperate location and generate a module file
    which sets the PYTHONPATH.

    We use the default CMake implementation, and use make_module_extra from PythonPackage.

Extra easyconfig parameters specific to ``CMakePythonPackage`` easyblock
------------------------------------------------------------------------

========================   =====================================================   ===============
easyconfig parameter       description                                             default value
========================   =====================================================   ===============
``configure_cmd_prefix``   Prefix to be glued before ./configure                   ``""``
``prefix_opt``             Prefix command line option for configure script         ``"--prefix="``
``tar_config_opts``        Override tar settings as determined by configure.       ``False``
``separate_build_dir``     Perform build in a separate directory                   ``False``
``srcdir``                 Source directory location to provide to cmake command   ``None``
========================   =====================================================   ===============


Customised steps in ``CMakePythonPackage`` easyblock
----------------------------------------------------
* ``build_step`` - Build Python package with cmake
* ``configure_step`` - Main configuration using cmake
* ``test_step`` - Main configuration using cmake

.. CmdCp:
``CmdCp``
=========

(derives from :ref:`MakeCp`)

Software with no configure, no make, and no make install step.
    Just run the specified command for all sources, and copy specified files to the install dir

Extra easyconfig parameters specific to ``CmdCp`` easyblock
-----------------------------------------------------------

========================   =====================================================================   ====================================================
easyconfig parameter       description                                                             default value
========================   =====================================================================   ====================================================
``configure_cmd_prefix``   Prefix to be glued before ./configure                                   ``""``
``tar_config_opts``        Override tar settings as determined by configure.                       ``False``
``with_configure``         Run configure script before building                                    ``False``
``files_to_copy``          List of files or dirs to copy                                           ``[]``
``cmds_map``               List of regex/template command (with 'source'/'target' fields) tuples   ``[('.*', '$CC $CFLAGS %(source)s -o %(target)s')]``
``prefix_opt``             Prefix command line option for configure script                         ``"--prefix="``
========================   =====================================================================   ====================================================


Customised steps in ``CmdCp`` easyblock
---------------------------------------
* ``build_step`` - Build by running the command with the inputfiles
* ``configure_step`` - Configure build if required (inherited)
* ``test_step`` - Configure build if required (inherited)

.. ConfigureMake:
``ConfigureMake``
=================

(derives from EasyBlock)

Support for building and installing applications with configure/make/make install

Extra easyconfig parameters specific to ``ConfigureMake`` easyblock
-------------------------------------------------------------------

========================   =================================================   ===============
easyconfig parameter       description                                         default value
========================   =================================================   ===============
``configure_cmd_prefix``   Prefix to be glued before ./configure               ``""``
``prefix_opt``             Prefix command line option for configure script     ``"--prefix="``
``tar_config_opts``        Override tar settings as determined by configure.   ``False``
========================   =================================================   ===============


Customised steps in ``ConfigureMake`` easyblock
-----------------------------------------------
* ``build_step`` - Start the actual build
        - typical: make -j X
* ``configure_step`` - Configure step
        - typically ./configure --prefix=/install/path style
* ``test_step`` - Test the compilation
        - default: None


Example for ``ConfigureMake`` easyblock
---------------------------------------

::

    easyblock = 'ConfigureMake'

    name = 'zsync'
    version = '0.6.2'

    homepage = 'http://zsync.moria.org.uk/'
    description = """zsync-0.6.2: Optimising file distribution program, a 1-to-many rsync"""

    sources = [SOURCE_TAR_BZ2]
    source_urls = ['http://zsync.moria.org.uk/download/']


    toolchain = {'name': 'ictce', 'version': '5.3.0'}

    sanity_check_paths = {
    'files': ['bin/zsync'],
    'dirs': []
    }

    moduleclass = 'tools'


.. ConfigureMakePythonPackage:
``ConfigureMakePythonPackage``
==============================

(derives from :ref:`ConfigureMake`, :ref:`PythonPackage`)

Build a Python package and module with 'python configure/make/make install'.

    Implemented by using:
    - a custom implementation of configure_step
    - using the build_step and install_step from ConfigureMake
    - using the sanity_check_step and make_module_extra from PythonPackage

Extra easyconfig parameters specific to ``ConfigureMakePythonPackage`` easyblock
--------------------------------------------------------------------------------

========================   =================================================   ===============
easyconfig parameter       description                                         default value
========================   =================================================   ===============
``runtest``                Run unit tests.                                     ``True``
``configure_cmd_prefix``   Prefix to be glued before ./configure               ``""``
``prefix_opt``             Prefix command line option for configure script     ``"--prefix="``
``options``                Dictionary with extension options.                  ``{}``
``tar_config_opts``        Override tar settings as determined by configure.   ``False``
========================   =================================================   ===============


Customised steps in ``ConfigureMakePythonPackage`` easyblock
------------------------------------------------------------
* ``build_step`` - Build Python package with 'make'.
* ``configure_step`` - Configure build using 'python configure'.
* ``test_step`` - Test Python package.


Example for ``ConfigureMakePythonPackage`` easyblock
----------------------------------------------------

::

    easyblock = 'ConfigureMakePythonPackage'

    name = 'PyQt'
    version = '4.11.3'

    homepage = 'http://www.riverbankcomputing.co.uk/software/pyqt'
    description = """PyQt is a set of Python v2 and v3 bindings for Digia's Qt application framework."""

    toolchain = {'name': 'goolf', 'version': '1.5.14'}

    sources = ['%(name)s-x11-gpl-%(version)s.tar.gz']
    source_urls = ['http://sourceforge.net/projects/pyqt/files/PyQt4/PyQt-%(version)s']

    python = 'Python'
    pyver = '2.7.9'
    pythonshortver = '.'.join(pyver.split('.')[:2])
    versionsuffix = '-%s-%s' % (python, pyver)

    dependencies = [
    (python, pyver),
    ('SIP', '4.16.4', versionsuffix),
    ('Qt', '4.8.6'),
    ]

    configopts = "configure-ng.py --confirm-license"
    configopts += " --destdir=%%(installdir)s/lib/python%s/site-packages " % pythonshortver
    configopts += " --no-sip-files"

    options = {'modulename': 'PyQt4'}

    modextrapaths = {'PYTHONPATH': 'lib/python%s/site-packages' % pythonshortver}

    moduleclass = 'vis'


.. FortranPythonPackage:
``FortranPythonPackage``
========================

(derives from :ref:`PythonPackage`)

Extends PythonPackage to add a Fortran compiler to the make call

Extra easyconfig parameters specific to ``FortranPythonPackage`` easyblock
--------------------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``runtest``            Run unit tests.                      ``True``
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``FortranPythonPackage`` easyblock
------------------------------------------------------
* ``build_step`` - Customize the build step by adding compiler-specific flags to the build command.
* ``configure_step`` - Configure Python package build. (inherited)
* ``test_step`` - Test the built Python package. (inherited)

.. IntelBase:
``IntelBase``
=============

(derives from EasyBlock)

Base class for Intel software
    - no configure/make : binary release
    - add license_file variable

Extra easyconfig parameters specific to ``IntelBase`` easyblock
---------------------------------------------------------------

======================   ===================================   ====================
easyconfig parameter     description                           default value
======================   ===================================   ====================
``m32``                  Enable 32-bit toolchain               ``False``
``license_activation``   License activation type               ``"license_server"``
``usetmppath``           Use temporary path for installation   ``False``
======================   ===================================   ====================


Customised steps in ``IntelBase`` easyblock
-------------------------------------------
* ``build_step`` - Binary installation files, so no building.
* ``configure_step`` - Configure: handle license file and clean home dir.
* ``test_step`` - Run unit tests provided by software (if any). (inherited)

.. JAR:
``JAR``
=======

(derives from :ref:`Binary`)

Support for installing JAR files.

Extra easyconfig parameters specific to ``JAR`` easyblock
---------------------------------------------------------

====================   ===============================================================   =============
easyconfig parameter   description                                                       default value
====================   ===============================================================   =============
``staged_install``     Perform staged installation via subdirectory of build directory   ``False``
``install_cmd``        Install command to be used.                                       ``None``
====================   ===============================================================   =============


Customised steps in ``JAR`` easyblock
-------------------------------------
* ``build_step`` - No compilation, this is binary software (inherited)
* ``configure_step`` - No configuration, this is binary software (inherited)
* ``test_step`` - No configuration, this is binary software (inherited)

.. MakeCp:
``MakeCp``
==========

(derives from :ref:`ConfigureMake`)

Software with no configure and no make install step.

Extra easyconfig parameters specific to ``MakeCp`` easyblock
------------------------------------------------------------

========================   =================================================   ===============
easyconfig parameter       description                                         default value
========================   =================================================   ===============
``files_to_copy``          List of files or dirs to copy                       ``[]``
``configure_cmd_prefix``   Prefix to be glued before ./configure               ``""``
``prefix_opt``             Prefix command line option for configure script     ``"--prefix="``
``tar_config_opts``        Override tar settings as determined by configure.   ``False``
``with_configure``         Run configure script before building                ``False``
========================   =================================================   ===============


Customised steps in ``MakeCp`` easyblock
----------------------------------------
* ``build_step`` - Start the actual build
        - typical: make -j X (inherited)
* ``configure_step`` - Configure build if required (inherited)
* ``test_step`` - Test the compilation
        - default: None (inherited)

.. PackedBinary:
``PackedBinary``
================

(derives from :ref:`Binary`, EasyBlock)

Support for installing packed binary software.
    Just unpack the sources in the install dir

Extra easyconfig parameters specific to ``PackedBinary`` easyblock
------------------------------------------------------------------

====================   ===============================================================   =============
easyconfig parameter   description                                                       default value
====================   ===============================================================   =============
``staged_install``     Perform staged installation via subdirectory of build directory   ``False``
``install_cmd``        Install command to be used.                                       ``None``
====================   ===============================================================   =============


Customised steps in ``PackedBinary`` easyblock
----------------------------------------------
* ``build_step`` - No compilation, this is binary software (inherited)
* ``configure_step`` - No configuration, this is binary software (inherited)
* ``test_step`` - No configuration, this is binary software (inherited)

.. PerlModule:
``PerlModule``
==============

(derives from ExtensionEasyBlock, :ref:`ConfigureMake`)

Builds and installs a Perl module, and can provide a dedicated module file.

Extra easyconfig parameters specific to ``PerlModule`` easyblock
----------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``runtest``            Run unit tests.                      ``"test"``
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``PerlModule`` easyblock
--------------------------------------------
* ``build_step`` - No separate build procedure for Perl modules.
* ``configure_step`` - No separate configuration for Perl modules.
* ``test_step`` - No separate (standard) test procedure for Perl modules.

.. PythonPackage:
``PythonPackage``
=================

(derives from ExtensionEasyBlock)

Builds and installs a Python package, and provides a dedicated module file.

Extra easyconfig parameters specific to ``PythonPackage`` easyblock
-------------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``runtest``            Run unit tests.                      ``True``
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``PythonPackage`` easyblock
-----------------------------------------------
* ``build_step`` - Build Python package using setup.py
* ``configure_step`` - Configure Python package build.
* ``test_step`` - Test the built Python package.

.. RPackage:
``RPackage``
============

(derives from ExtensionEasyBlock)

Install an R package as a separate module, or as an extension.

Extra easyconfig parameters specific to ``RPackage`` easyblock
--------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``RPackage`` easyblock
------------------------------------------
* ``build_step`` - No separate build step for R packages.
* ``configure_step`` - No configuration for installing R packages.
* ``test_step`` - No configuration for installing R packages.

.. Rpm:
``Rpm``
=======

(derives from :ref:`Binary`)

Support for installing RPM files.
    - sources is a list of rpms
    - installation is with --nodeps (so the sources list has to be complete)

Extra easyconfig parameters specific to ``Rpm`` easyblock
---------------------------------------------------------

====================   ===============================================================   =============
easyconfig parameter   description                                                       default value
====================   ===============================================================   =============
``postinstall``        Enable post install                                               ``False``
``force``              Use force                                                         ``False``
``install_cmd``        Install command to be used.                                       ``None``
``staged_install``     Perform staged installation via subdirectory of build directory   ``False``
``makesymlinks``       Create symlinks for listed paths                                  ``[]``
``preinstall``         Enable pre install                                                ``False``
====================   ===============================================================   =============


Customised steps in ``Rpm`` easyblock
-------------------------------------
* ``build_step`` - No compilation, this is binary software (inherited)
* ``configure_step`` - Custom configuration procedure for RPMs: rebuild RPMs for relocation if required. (inherited)
* ``test_step`` - Custom configuration procedure for RPMs: rebuild RPMs for relocation if required. (inherited)

.. RubyGem:
``RubyGem``
===========

(derives from ExtensionEasyBlock)

Builds and installs Ruby Gems.

Extra easyconfig parameters specific to ``RubyGem`` easyblock
-------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``RubyGem`` easyblock
-----------------------------------------
* ``build_step`` - No separate build procedure for Ruby Gems.
* ``configure_step`` - No separate configuration for Ruby Gems.
* ``test_step`` - No separate (standard) test procedure for Ruby Gems.

.. Tarball:
``Tarball``
===========

(derives from EasyBlock)

Precompiled software supplied as a tarball:
    - will unpack binary and copy it to the install dir


Customised steps in ``Tarball`` easyblock
-----------------------------------------
* ``build_step`` - Dummy build method: nothing to build
* ``configure_step`` - Dummy configure method
* ``test_step`` - Run unit tests provided by software (if any). (inherited)

.. Toolchain:
``Toolchain``
=============

(derives from :ref:`Bundle`)

Compiler toolchain: generate module file only, nothing to build/install


Customised steps in ``Toolchain`` easyblock
-------------------------------------------
* ``build_step`` - Do nothing. (inherited)
* ``configure_step`` - Do nothing. (inherited)
* ``test_step`` - Do nothing. (inherited)

.. VSCPythonPackage:
``VSCPythonPackage``
====================

(derives from :ref:`VersionIndependentPythonPackage`)

Support for install VSC Python packages.

Extra easyconfig parameters specific to ``VSCPythonPackage`` easyblock
----------------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``runtest``            Run unit tests.                      ``True``
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``VSCPythonPackage`` easyblock
--------------------------------------------------
* ``build_step`` - No build procedure. (inherited)
* ``configure_step`` - No build procedure. (inherited)
* ``test_step`` - No build procedure. (inherited)

.. VersionIndependendPythonPackage:
``VersionIndependendPythonPackage``
===================================

(derives from :ref:`VersionIndependentPythonPackage`)

No longer supported class for building/installing python packages without requiring a specific python package.

Extra easyconfig parameters specific to ``VersionIndependendPythonPackage`` easyblock
-------------------------------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``runtest``            Run unit tests.                      ``True``
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``VersionIndependendPythonPackage`` easyblock
-----------------------------------------------------------------
* ``build_step`` - No build procedure. (inherited)
* ``configure_step`` - No build procedure. (inherited)
* ``test_step`` - No build procedure. (inherited)

.. VersionIndependentPythonPackage:
``VersionIndependentPythonPackage``
===================================

(derives from :ref:`PythonPackage`)

Support for building/installing python packages without requiring a specific python package.

Extra easyconfig parameters specific to ``VersionIndependentPythonPackage`` easyblock
-------------------------------------------------------------------------------------

====================   ==================================   =============
easyconfig parameter   description                          default value
====================   ==================================   =============
``runtest``            Run unit tests.                      ``True``
``options``            Dictionary with extension options.   ``{}``
====================   ==================================   =============


Customised steps in ``VersionIndependentPythonPackage`` easyblock
-----------------------------------------------------------------
* ``build_step`` - No build procedure.
* ``configure_step`` - Configure Python package build. (inherited)
* ``test_step`` - Test the built Python package. (inherited)


