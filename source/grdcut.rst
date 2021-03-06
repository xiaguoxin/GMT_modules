.. index:: ! grdcut

grdcut
======

:官方文档: :ref:`grdcut`
:简介: 从一个网格文件中裁剪出一个子区域的网格文件

该模块可以从一个网格文件中根据条件裁剪出一个新的网格文件：

- ``-R`` 选项： 直接指定子区域的范围
- ``-Z`` 选项： 通过检查网格节点的值间接限定子区域的范围
- ``-S`` 选项： 通过指定与特定点的距离间接限定子区域的范围

必选选项
--------

``<ingrid>``
    输入网格文件名

``-G<outgrid>``
    输出网格文件名

可选选项
--------

``-N[<nodata>]``
    允许新网格的区域范围超过原网格的区域范围。

    一般情况下，若指定的区域范围大于输入网格的区域范围，超出的部分会被自动忽略，
    实际的输出网格的区域范围会自动适应输入网格的区域范围。使用 ``-N`` 选项，
    则超出的区域范围内，网格节点会被赋以指定的值，默认赋值为NaN，也可以自己
    指定其值为 ``<nodata>``

``-R<west>/<east>/<south>/<north>``
    直接指定要截取的网格子区域的范围

``-S[n]<lon>/<lat>/<radius>[<unit>]``
    指定圆心位置及其半径，程序会自动计算一个矩形区域，该矩形区域包含了圆上及
    圆内所有网格点

    #. ``<lon>/<lat>`` 圆心位置
    #. ``<radius>`` 圆半径
    #. ``<unit>`` 圆半径所使用的距离单位，见 :ref:`doc:unit` 一节
    #. ``-Sn`` 将所有矩形区域内但不在圆内的节点的值为NaN

``-Z[n|r]<min>/<max>``
    定义一个Z值范围 ``<min>/<max>`` 并据此确定一个新的矩形子区域，使得在该子
    区域\ **外**\ 的所有节点值都位于给定的Z值范围外。 ``<min>`` 和 ``<max>``
    默认值为正负无穷，可以用 ``-`` 表示无穷。

    为了理解这个选项，需要了解该选项是如何具体实现的。为了得到满足要求的矩形
    子区域，代码中首先将矩形子区域的四条边界设置为原始网格文件的四条边界，然后
    逐步向内收缩，当某条边界上的某个网格点的值在给定的Z值范围内，则此边界停止
    收缩。需要格外注意的是，该选项只保证了子区域\ **外**\ 的所有节点值都位于
    给定的Z值范围外，矩形子区域\ **内**\ 的网格点的值并不一定都在Z值范围内。

    在收缩四个边界时，正常情况下，值为NaN的记录会被直接跳过：

    - ``-Zn`` 认为NaN记录位于Z值范围之外，故而保证子区域内是没有NaN记录的
    - ``-Zr`` 认为NaN记录位于Z值范围之内，因而当遇到NaN记录时，则停止收缩边界

    .. TODO::

       1. 查看源码确认 -Z 选项的解释是否正确
       2. 确认 -Z 选项的示例是否正确

示例
----

使用 ``-R`` 选项直接指定子区域的范围::

    gmt grdcut input.nc -Goutput.nc -R0/30/-30/30

使用 ``-S`` 选项生成一个包含了原点(45,30)周围 500 km 以内的所有点的矩形区域，
并设置矩形区域内圆外的节点值为NaN::

    gmt grdcut input.nc -Goutput.nc -Sn45/30/500k -V

使用 ``-Z`` 选项使得子区域内的所有节点值都小于0::

        gmt grdcut abc.nc -Gdef.nc -Z-/0 -V

相关
----

- :doc:`grdpaste`
- :doc:`grdsample`
