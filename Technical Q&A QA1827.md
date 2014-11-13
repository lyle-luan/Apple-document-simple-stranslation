#Technical Q&A QA1827
 
##Automating Version and Build Numbers Using agvtool

**Important:**这是关于发展中的API或技术的初步文件。虽然这份文件已进行技术审查其准确性，但它不是最终的。只能苹果应用程序开发者的注册会员才能使用该苹果机密信息。苹果公司提供这些机密信息帮助你使用本文所描述的技术和编程接口。这些信息可能随时更改，根据该文档实现的软件应在最终的操作系统上参考最终文档进行测试。本文档的新版本可能随新的API或技术一起放出。(Newer versions of this document may be provided with future seeds of the API or technology.)

###Q: How do I auto-increment my build and version numbers using *agvtool*?

A：版本号和内部版本号分别指定应用程序的市场版本和内部版本。agvtool是一个命令行工具，可以自动增加版本号到某最高位数字的版本号或一个具体的版本号。本文档提供了使用agvtool更新内部版本号和版本号按步指示。"Xcode"和"Command Line"章节分别指示了在Xcode和命令行中设置的步骤。

**Note:** 版本号，App用户看到的数字，指明了App的发布版本。它由Info.plist中的CFBundleShortVersionString(Bundle versions string, short)指定。

内部版本号标识应用程序的一个未发布或发布的版本。它由Info.plist中的CFBundleVersion(Bundle version)指定。更多信息参考[Information Property List Key Reference]()。

图1中的示例显示了App Store的版本号和构建次数，分别为1.3和201.4。

图1：App Store的版本号和构建次数

![Alt text][pic]
[pic]: http://mouapp.com/Mou_128.png "Mou"

**Important:** 当您的应用程序包含多个目标，agvtool会将所有目标的版本号设置为相同​​的号码。

###Xcode###

你必须在Xcode工程中完成以下步骤。

####1)使能agvtool
导航到目标的Build Settings面板中，然后按如下更新所有构建配置：

- 设置*Current Project Version*

	Xcode工程文件，*project.pbxproj*，包含指定工程版本的构建设置，*CURRENT_PROJECT_VERSION* (*Current Project Version*)。*agvtool*会在*project.pbxproj*中查找*CURRENT_PROJECT_VERSION*。如果*CURRENT_PROJECT_VERSION*存在则继续运行，否则停止。该值用户更新构建数值。
	
	**Note：** 当前项目版本的值必须为整数或类似34.6的浮点数。新工程的该值应该设置为1。
- 设置*Versioning System*为*Apple Generic*

	默认情况下，Xcode不使用版本系统。将*Versioning System*设置为*Apple Generic*确保Xcode在工程中包含所有*agvtool*产生的版本信息。
	
图2：设置Current Project Version和Versioning System

![Alt text][pic]
[pic]: http://mouapp.com/Mou_128.png "Mou"

####2)设置版本和内部版本号
*agvtool*搜索应用程序的Info.plist查找版本和内部版本号。如果存在则更新，如果不存在则什么也不处理。确保Info.plist文件内存在CFBundleVersion(Bundle version)和CFBundleShortVersionString(Bundle versions string, short)，如图3所示。

图3:Info面板内的版本和内部版本数值

![Alt text][pic]
[pic]: http://mouapp.com/Mou_128.png "Mou"

**Note:** DYLIB_CURRENT_VERSION(Current Library Version)选项指定库或框架的内部版本。如果正在构建库或框架，请参照以上步骤并确保目标的Build Settings面板中包括了该选项。

###Command Line###
退出Xcode，在运行下面命令前在Terminal中导航进包含*.xcodeproj*文件的目录下。*.xcodeproj*文件包含*agvtool*需要操作的*project.pbxproj*文件。

####更新版本号
更新版本号到某个特定值，运行

	agvtool new-marketing-version <your_specific_version>

<your_specific_version>是你选定的值，例如表1。

表1: 更新版本号为2.0

	$ xcrun agvtool new-marketing-version 2.0
	Setting CFBundleShortVersionString of project MyProject to:
	    2.0.
	 
	Updating CFBundleShortVersionString in Info.plist(s)...
	 
	Updated CFBundleShortVersionString in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist" to 2.0
	
####更新内部版本号

- 自动更新内部版本号，运行
	``agvtool next-version -all``
	
	表2:自动更新内部版本号为下一个最大整数值
	
		$ xcrun agvtool next-version -all
		Setting version of project MyProject to:
		    2.
	 
		Also setting CFBundleVersion key (assuming it exists)
		 
		Updating CFBundleVersion in Info.plist(s)...
		 
		Updated CFBundleVersion in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist" to 2
	Note: 运行``agvtool next-version -all``增加版本号为下一个最大整数值。比如，2增大到3，1.3增大到2。
- 将App的内部版本设置为指定的值，运行
	``agvtool new-version -all <your_specific_version>``
	<your_specific_version>是你选定的值。
	
	表3: 设置内部版本为2.6.9
	
		$ xcrun agvtool new-version -all 2.6.9
		Setting version of project MyProject to:
		    2.6.9
		 
		Also setting CFBundleVersion key (assuming it exists)
		 
		Updating CFBundleVersion in Info.plist(s)...
		 
		Updated CFBundleVersion in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist" to 2.6.9

####查看版本号####
- 查看当前版本号，运行
	``agvtool what-marketing-version``
	
	表4: 展示当前版本号
	
		$ xcrun agvtool what-marketing-version
		No marketing version number (CFBundleShortVersionString) found for Jambase targets.
		 
		Looking for marketing version in native targets...
		Looking for marketing version (CFBundleShortVersionString) in native targets...
		 
		Found CFBundleShortVersionString of "2.0" in "MyProject.xcodeproj/../MyProject/MyProject-Info.plist"
- 查看当前内部版本号，运行
	``agvtool what-version``
	
	表5: 展示当前内部版本号
	
		$ xcrun agvtool what-version
		Current version of project MyProject is:
		    2.2
		    
##Document Revision History##

2014-05-12 New document that describes how to auto-increment build and version numbers using agvtool.