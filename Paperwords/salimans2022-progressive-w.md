by orders of magnitude 多个数量级
distill 蒸馏
degradation 降格，弱化
monotonically 单调地

cmake -G "Visual Studio 14 2015" -DCMAKE_BUILD_TYPE:string="Release" -DBUILD_SHARED_LIBS:bool=on -DCMAKE_INSTALL_PREFIX:path="%USERPROFILE%" -DCMAKE_LIBRARY_PATH:path="%USERPROFILE%" -DCMAKE_INCLUDE_PATH:path="%USERPROFILE%\include" ..