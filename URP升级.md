# 安装URP插件

点击Window
选择Package Manager
在出现的窗口左上角选择Unity Registry
搜索关键字Universal
在出现的Universal RP插件中下面点击Install

# 创建URP渲染管线

## 安装完成之后，在Project窗口中点击上方加号，选择Rendering，选择Universal Render Pipeline，选择Pipeline Asset （Forward Renderer），点击创建。


创建之后使用默认的命名即可，之后自动创建好两个Renderer

## 另：如果unity编辑器版本不一样的话，可能是以下的情况，不过不影响创建，只是创建的方式不一样。

同样点击Project窗口上方加号，选择最下方Rendering，选择URP Asset（with Universal Renderer），然后点击创建，默认命名即可。


也可以在资源空白处点击右键，选择Create，选择下面的Rendering，选择URP Asset（with Universal Renderer），然后点击创建，默认命名即可。结果都一样。

# 创建文件夹，便于管理文件资源。



Pipeline Setting

# 配置渲染管线

编辑器左上角选择Edit，选择Project Settings …，在弹出的窗口选择Graphics。


在Scriptable Render Pipeline Settings属性右侧点击小圆点，选择刚才创建好的Renderer


再选择Quality选项，在其中Render Popeline Asset属性点击小圆点，选择刚才创建好的Renderer。至此就配置好了。


升级素材的渲染管线
拖入素材之后会发现素材是粉色的，是由于素材资源并没有适配渲染管线，所以我们要给素材升级渲染管线



升级步骤

双击SimpleNaturePack_2020.3_URP_v1.24。（此处资源在文章最下方可以获取）


点击Import导入。

方式一
导入完成之后，在编辑器上方选择Edit，选择Render Pipeline，选择Universal Render Pipeline，选择Upgrade Project Materials to UniversalRP Materials，升级项目中所有材质。下面的选项是升级现在选择的材质。
（如果找不到Render Pipeline选项，参考下方方式二）


升级完成


方式二
如果在Edit中找不到Render Pipeline选项，按照下面的步骤操作。

导入完成之后，选择一个材质(渲染问题应该是粉色的，此处我已升级完毕)。


在编辑器选择Edit，选择Rendering，选择Material，选择Convert Selected Built-in Material to URP


至此升级完成

————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。

原文链接：https://blog.csdn.net/xks18232047575/article/details/134117859