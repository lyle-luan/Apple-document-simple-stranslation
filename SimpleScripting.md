###关于SimpleScripting

此示例将引导你完成编写脚本化App所需要的最基本的步骤。包括建立Info.plist文件，添加一个脚本字典，并在App主类中添加一个属性。

###ReadMe.txt

	SimpleScripting
	===============
	
	关于：
	此示例说明了编写脚本化App所需的初始步骤。
	SimpleScripting*系列中基于此示例建立的其他示例，
	显示如何添加额外的脚本功能。
	
	Step 1: 创建
	
	创建一个包含标准scripting suite的新.sdef文件，并把它添加到你的Xcode项目中。
	通常情况下，这个文件具有与你的应用程序相同的名称。
	遵循这条规则，此示例中的.sdef文件被命名为“SimpleScripting.sdef”。
	(在SimpleScripting.sdef文件最开头添加一下代码：
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE dictionary SYSTEM "file://localhost/System/Library/DTDs/sdef.dtd">
	这两行代码示例中没有介绍)
	在文件中，输入一个包含标准AppleScript suite的空字典，如下：
	<dictionary xmlns:xi="http://www.w3.org/2003/XInclude">
 
    <xi:include href="file:///System/Library/ScriptingDefinitions/CocoaStandard.sdef" xpointer="xpointer(/dictionary/suite)"/>
 
        <!-- add your own suite definitions here -->
 
	</dictionary>
	
	以下介绍重要的几点：
	
	1.在开放的dictionary标签内的'xi'域内声明字典元素遵循XInclude标准定义的约定。这将使我们能够包括标准定义。
	2.'xi:include'标签在.sdef中包含标准套件。
	
	NOTE: 在此之前的Mac OS X 10.5开发者可以从ScriptingDefinitions示例中(http://developer.apple.com/samplecode/ScriptingDefinitions/index.html)将标准套件直接到他们的.sdef文件。为了向后兼容Mac OS X v10.4，您可以继续这样做，但推荐使用上述先进的XInclude技术。
	
	
	
	
	Step 2: 指明需要脚本化
	
	在Info.plist文件内添加一下两项：
	
		<key>NSAppleScriptEnabled</key>
	    <string>YES</string>
	    
	    <key>OSAScriptingDefinition</key>
	    <string>Simple.sdef</string>
    
    第一项标记你的应用程序支持脚本，第二项让系统知道去哪里寻找你应用程序的脚本定义文件。你应该使用在步骤1中使用的文件名。
    
    http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ScriptableCocoaApplications/SApps_evolution/SAppsEvolution.html
    
    
    
    
    Step 3: 开始编辑脚本定义文件(.sdef)
    
    在Xcode中，选择您.sdef文件，然后选择“File>Open As...>Plain Text File”，然后编辑该文件添加你自己应用程序的定义。
   
    重要提示：您可以使用脚本编辑器应用程序以字典浏览方式来查看你的应用程序的字典和编写测试脚本。每当更改.sdef文件后，你将不得不退出并再次运行脚本编辑器来看到的变化，因为它在运行时会缓存字典。
    
    
    
    Step 4: 添加开始套件
    
    在</dictionary>标记之前添加一个新的套件：
    
	    <suite name="Simple Scripting Suite" code="SScr" description="SimpleScripting application specific scripting facilities.">
	        <!-- put your application specific scripting suite information here -->
	    </suite>
	
	你要把应用程序的特定信息记录在这个suite标签里面。
	你可以添加额外的套件来将相关的功能整合到一起，但对于大多数情况一个应该是足够了。
	
	
	
	Step 5: 在脚本suite中添加App类
	
	以下是添加了App类后的suite：
	
		<suite name="Simple Scripting Suite" code="SScr"
	        description="SimpleScripting application specific scripting facilities.">
	        <!-- put your application specific scripting suite information here -->
	        <class name="application" code="capp" 
	                    description="Our simple application class." inherits="application">
	            <cocoa class="NSApplication"/>
	            <property name="ready" code="Srdy" type="boolean" access="r"
	                    description="we're always ready"/>
	        </class>
	    </suite>
	    
	请注意，我们添加的application类继承自Skeleton.sdef标准套件中定义的App类。此外，我们给application类增加了一个命名为'ready'的属性。请注意，我在access中指定"r"来定义该属性为“只读”。
	
	application类是AppleScriptable应用的根容器类。所有应用程序提供的根本功能将被包含在这个类和该类包含的其他类和对象。
	
	以下是需要注意的重要事项：
	- 该套件具有一个独特的四字符代码，'SScr'。
	- 'ready'属性具有一个独特的四字符代码。在给这个代码起名字时，我先查看了AppleScript Terminology and Apple Event Codes表：
		http://developer.apple.com/releasenotes/AppleScript/ASTerminology_AppleEventCodes/TermsAndCodes.html(该链接失效了)
	
	查看'ready'属性是否已经与四字母代码相关联。如果有的话，我会使用这个代码，但我没有找到'ready'与某个四字母代码关联，所以我为'ready'起了一个四字符代码。而且，当然，我仔细检查了Apple Event Codes表，以确保我起的四字符代码没有被用于另一个术语。
	
	
	
	
	Step 6: 在NSApplication类中添加一个类别
	
	在项目中添加文件SimpleApplication.h和SimpleApplication.m，在两者中添加一个实现了属性'ready'的访问方法的类别。
	(文件名跟项目名相同)
	
	in SimpleApplication.h:
 
	    #import <Cocoa/Cocoa.h>
	    
	    @interface NSApplication (SimpleApplication)
	    
	    - (NSNumber*) ready;
	    
	    @end
 
 
	and in SimpleApplication.m:
 
 
	    #import "SimpleApplication.h"
	    
	    @implementation NSApplication (SimpleApplication)
	    
	    - (NSNumber*) ready 
	    {
	        return [NSNumber numberWithBool:YES];
	    }
	    
	    @end
	既然属性'ready'被声明为只读，我们只需要提供读方法。为一个只读属性实现一个set方法没有意义。
	
	
	
	
	Step 7: 测试
	
	编译运行该App。然后在脚本编辑器里运行一下脚本：
	
	tell application "SimpleScripting"
	    properties
	end tell
	
	应该会得到以下结果：
	
	{name:"SimpleScripting", frontmost:false, version:"0", class:application, ready:true}
	
	如果成功得到以上结果，那么恭喜你，你制作了第一个脚本化App。
	
	
	
	
	Step 8: 最后...
	
	在为你的App添加脚本支持的时候，你会想要调试并跟踪程序运行情况。但是调试器不是跟踪脚本程序运行情况的最佳选择。因为App中的脚本总是在接收回调，你可以跟踪这些回调函数来观察程序。那么该怎么做呢？我们建议你在实现了脚本回调的函数里添加日志语句。在项目中添加scriptLog.h文件包含一下定义：
	
	#define scriptLoggingMasterSwitch   ( 1 )
    
    #if scriptLoggingMasterSwitch
    #define SLOG(format,...) NSLog( @"SLOG: File=%s line=%d proc=%s " format, strrchr("/" __FILE__,'/')+1, __LINE__, __PRETTY_FUNCTION__, ## __VA_ARGS__ )
    #else
    #define SLOG(format,...)
    #endif
 
	因此我们需要在SimpleApplication.m做如下修改:
 
    #import "SimpleApplication.h"
    #import "scriptLog.h"
    
    @implementation NSApplication (SimpleApplication)
    
    - (NSNumber*) ready
    {
        SLOG(@"Here we are!");
        return [NSNumber numberWithBool:YES];
    }
    
    @end
    
    如果添加好这些内容，并运行之前的测试脚本，在App运行日志里你可以找到如下内容：
    
    2007-02-21 17:36:56.913 SimpleScripting[4905] SLOG: File=SimpleApplication.m line=15 proc=-[NSApplication(SimpleApplication) ready] Here we are!
    
    以上语句显示了我们使用的文件名，行号，方法名和字串。随着以后添加更多的脚本函数，你会发现该log日志可以很方便的debug我们的脚本。
    
    
    
    
    Step 9: 接下来是什么？
    
    好了，现在你已经学会了基础，你已经可以开始给你的App添加脚本化了。但是，在开始添加脚本功能前花点时间仔细规划下，将是非常值得的。因此，请考虑阅读下面的文档。
    
    - 在此页上“Implementing a Scriptable Application”中列出的项目是必不可少的读物。刚接触脚本的人应该仔细阅读这些文件，熟悉其中讨论的议题。
    http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ScriptableCocoaApplications/SApps_implement/SAppsImplement.html
    
    - "Cocoa Scripting Guide"中的"Designing for Scriptability"提供了有关设计问题和策略的清单
    http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ScriptableCocoaApplications/SApps_design_apps/SAppsDesignApp.html
    
    - 此Scripting Interface Guidelines文档提供了当为App添加脚本化时，应该考虑的更详细的信息：
    http://developer.apple.com/technotes/tn2002/tn2106.html
    
    - The AppleScript terminology and Apple Event Codes文档提供了一个包含所有已经被使用了的四字符代码列表。当你往App汇总添加术语时，总应检查看看，你要使用的四字符代码已经被定义了，并确保你想定义的四字节代码尚未使用。
    http://developer.apple.com/releasenotes/AppleScript/ASTerminology_AppleEventCodes/TermsAndCodes.html
    
    - NSScriptCommand类用于实现动词（又名命令）
    http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSScriptCommand_Class/Reference/Reference.html
    
    
    
    
    Step 10: 再然后呢？
    
    该示例是一套示例中的一个，该套示例以下面的顺序排列，一个示例到另一个示例逐渐完善。
    
    SimpleScripting (you are here)
    http://developer.apple.com/samplecode/SimpleScripting/
    
	SimpleScriptingProperties
    http://developer.apple.com/samplecode/SimpleScriptingProperties/
	    
	SimpleScriptingObjects
    http://developer.apple.com/samplecode/SimpleScriptingObjects/
	    
	SimpleScriptingVerbs
    http://developer.apple.com/samplecode/SimpleScriptingVerbs/
    
    
    ===========================================================================
	BUILD REQUIREMENTS
	 
	Xcode 3.2, Mac OS X 10.6 Snow Leopard or later.
	 
	===========================================================================
	RUNTIME REQUIREMENTS
	 
	Mac OS X 10.6 Snow Leopard or later.
	 
	===========================================================================
	CHANGES FROM PREVIOUS VERSIONS
	 
	Version 1.1
	- Project updated for Xcode 4.
	Version 1.0
	- Initial Version
	 
	===========================================================================
	Copyright (C) 2008-2011 Apple Inc. All rights reserved.
	
或许我应该加个版权声明？