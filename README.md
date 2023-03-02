# ExternalProject Install step regression

This project was created to reproduce a regression introduced in CMake v3.25 through [kitware/cmake@66b5d51f3](https://github.com/Kitware/CMake/commit/66b5d51f3812ecb04d1cc1901016236249e70b49) originally intended to fix issue [#23946](https://gitlab.kitware.com/cmake/cmake/-/issues/23946).

```
ExternalProject: Install CMake projects using 'cmake --install'

In some cases, `cmake --install .` implements additional semantics over
just `cmake --build . --target install`.  For example, using the Xcode
"new build system" with `IOS_INSTALL_COMBINED` requires special support
from `cmake --install` beyond building the `install` target.

Fixes: #23946
```

## Reproducing the issue

```
git clone https://github.com/jcfr/cmake-ExternalProject-install-step-regression

cmake -S cmake-ExternalProject-install-step-regression -B cmake-ExternalProject-install-step-regression-build

cmake --build cmake-ExternalProject-install-step-regression-build --config Release
```

* Output of `cmake --build cmake-ExternalProject-install-step-regression-build --target install --config Release`

  ```
  [100%] This custom target is always expected to be built
  Building project...
  [100%] Built target build
  Install the project...
  -- Install configuration: ""
  Installing project...
  ```

* Output of `cmake --install cmake-ExternalProject-install-step-regression-build --config Release`

  ```
  Installing project...
  ```


## Observations

Running the CMake command `--install` does not attempt to re-build the project whereas building the `install` target is.


## Analysis

External projects implemented with the assumption that the build step will implicitly be re-built by simply building the install target may not work as expected.

This understanding seems to be consistent with the description of the install step command:

> If the configure step assumed the external project uses CMake as its build system, the install step will also. Otherwise, the install step will assume a Makefile-based build and simply run make install as the default build step.
>
> Source: https://cmake.org/cmake/help/v3.25/module/ExternalProject.html
