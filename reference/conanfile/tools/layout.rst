.. _conan_tools_layout:

conan.tools.layout
==================

.. warning::

    These tools are still **experimental** (so subject to breaking changes) but with very stable syntax.
    We encourage the usage of it to be prepared for Conan 2.0.
    The ``layout()`` feature will be fully functional only in the new build system integrations
    (:ref:`in the conan.tools space <conan_tools>`). If you are using other integrations, they
    might not fully support this feature.

.. _conan_tools_layout_predefined_layouts:

Predefined layouts
------------------

There are some pre-defined common layouts, ready to be simply used in recipes:

- ``cmake_layout()``: :ref:`a layout for a typical CMake project <cmake_layout>`
- ``vs_layout()``: a layout for a typical Visual Studio project
- ``basic_layout()``: :ref:`a very basic layout for a generic project <conan_tools_basic_layout>`


The predefined layouts define a few things, for example in the ``cmake_layout()`` the source folder is  called ``"."``, meaning that Conan will
expect the sources in the same directory where the conanfile is (most likely the project root, where a ``CMakeLists.txt`` file will be typically found).
This could be customized without fully changing the layout:

.. code:: python

    def layout(self):
        cmake_layout(self)
        self.folders.source = "mysrcfolder"


Even if this pre-defined layout doesn't suit your specific projects layout, checking how they implement their logic
shows how you could implement your own logic (and probably put it in a common ``python_require`` if you are going to use it in multiple
packages).


.. _conan_tools_basic_layout:

basic_layout
------------

Usage:

.. code:: python

    from conan.tools.layout import basic_layout

    def layout(self):
        basic_layout(self)


The current layout implementation is very simple, basically sets a different build folder for different build_types
and set the generators output folder inside the build folder. This way we avoid to clutter our project
while working locally.


.. code:: python

    def basic_layout(conanfile, src_folder="."):
        conanfile.folders.build = "build"
        if conanfile.settings.get_safe("build_type"):
            conanfile.folders.build += "-{}".format(str(conanfile.settings.build_type).lower())
        conanfile.folders.generators = os.path.join(conanfile.folders.build, "conan")
        conanfile.cpp.build.bindirs = ["."]
        conanfile.cpp.build.libdirs = ["."]
        conanfile.folders.source = src_folder