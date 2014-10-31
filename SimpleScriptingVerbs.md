##SimpleScriptingVerbs

- Last Revision: Version 1.2, 2011-09-07
Project updated for Xcode 4.
([Full Revision History]())
- Build Requirements: Xcode 3.0 
- Runtime Requirements: Mac OS X 10.5

此示例是一个后续的SimpleScripting示例，展示如何让AppleScriptable应用程序支持动词。

##ReadMe.txt

	SimpleScriptingVerbs
	====================
	
	FORWARD:
	
	此示例是一个后续的SimpleScripting示例，展示如何让AppleScriptable应用程序支持动词。(动词指在脚本中可以调用的命令或动作)
	
	关于:
	
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
	
	
	
	
	Step 2: 添加开始套件
	
	在</dictionary>标记之前添加一个新的套件：
	
		<suite name="Simple Scripting Verbs" code="SVrb" description="Terminology for the SimpleScriptingVerbs Sample.">
	    
	        <!-- We'll put our verb definitions here -->
	         
	    </suite>
	    
	你要把应用程序的特定信息记录在这个suite标签里面。
	你可以添加额外的套件来将相关的功能整合到一起，但对于大多数情况一个应该是足够了。
	
	重要事项：
	- 该套件用“SVrb”唯一标识。与另外四字节码结合成八字符码作为脚本命令。

	
	
	
	Step 3: 在套件添加动词
	
	以下是已经添加的动词：
	
	(a) 'do simple command'
	
		<command name="do simple command" code="SVrbSimp" description="run a simple command with no parameters">
            <cocoa class="SimpleCommand"/>
            <result type="integer" description="returns the number seven"/>
        </command>
        
    这是一个简单的不带参数的动词。它返回一个整数。没有比这更简单的动词。
    
    重要事项：
    
    - 注意代码是一个八个字符码。实际上是两个四字符码结合在一起。前四个字符是Apple event发送给你的应用程序的'event class'码。后四个字母是Apple events发送给你的应用程序的'event code'码。
    - 实现该命令的Cocoa类在“<cocoa class="SimpleCommand"/>”中指定。这里，我们指定为在SimpleCommand.h和SimpleCommand.m中实现的'SimpleCommand'类。当Cocoa接收到这个命令时，将实例化SimpleCommand类的一个实例，然后调用该实例的-performDefaultImplementation方法。你把命令执行的代码放置在performDefaultImplementation方法中。

    
    (b) 'do direct parameter command'
    
    	<command name="do direct parameter command" code="SVrbDpCm" description="run a simple command with a direct parameter">
            <cocoa class="DirectParameterCommand"/>
            <direct-parameter description="a text parameter passed to the command">
                <type type="text"/>
            </direct-parameter>
            <result type="text" description="the direct parameter enclosed in quotes"/>
        </command>
    
    这是一个接收单个字符串参数并返回字符串（用引号讲参数括起来）的动词。
    
    重要事项：
    
    - 注意代码值中有八个字符。这实际上是两个四字符代码结合在一起。前四个字符将是“事件类”在发送给你的应用程序的命令苹果事件。最后四个字母将在发送该命令的苹果事件“事件代码”。在这里，我们使用了套件的四个字母的代码作为事件类，然后，我们使用了一个独特的四字母代码的事件代码。
    - 实现该命令的Cocoa类在“<cocoa class="DirectParameterCommand"/>”中指定。这里，我们指定为在DirectParameterCommand.h和DirectParameterCommand.m中实现的'DirectParameterCommand'类。当Cocoa接收到这个命令时，将实例化SimpleCommand类的一个实例，然后调用该实例的-performDefaultImplementation方法。你把命令执行的代码放置在performDefaultImplementation方法中。
    - 在performDefaultImplementation方法中，可以调用[自directParameter]直接访问参数。
    
    
    
    (c) 'do command with args'
    
    	<command name="do command with args" code="SVrbAgCm" description="run a command with a bunch of arguments">
            <cocoa class="CommandWithArgs"/>
            
            <direct-parameter description="a text parameter passed to the command">
                <type type="text"/>
            </direct-parameter>
            
            <parameter name="blinking" code="savo" type="boolean" optional="yes" 
                description="a boolean parameter.">
                <cocoa key="SaveOptions"/>
            </parameter>
            
            <parameter name="preferred hand" code="LRnd" type="preferredhands" optional="yes" 
                description="a parameter using our enumeration.">
                <cocoa key="TheHand"/>
            </parameter>
            
            <parameter name="prose" code="Pros" type="text" optional="yes" 
                description="a text parameter.">
                <cocoa key="ProseText"/>
            </parameter>
            
            <parameter name="ivalue" code="iVal" type="integer" optional="yes" 
                description="an integer parameter.">
                <cocoa key="IntegerValue"/>
            </parameter>
            
            <parameter name="rvalue" code="rVal" type="real" optional="yes" 
                description="an real number parameter.">
                <cocoa key="RealValue"/>
            </parameter>
            
            <result type="text" description="the direct parameter enclosed in quotes"/>
        </command>
        
    该命令接收一个数字参数返回字符串(either a copy of the prose parameter enclosed in quotes or, if the prose parameter is not provided, a copy of the direct parameter enclosed in quotes)。
    
    重要事项：
    
    - 注意代码值中有八个字符。这实际上是两个四字符代码结合在一起。前四个字符将是“事件类”在发送给你的应用程序的命令苹果事件。最后四个字母将在发送该命令的苹果事件“事件代码”。在这里，我们使用了套件的四个字母的代码作为事件类，然后，我们使用了一个独特的四字母代码的事件代码。
    - 实现该命令的Cocoa类在“<cocoa class="CommandWithArgs"/>”中指定。这里，我们指定为在CommandWithArgs.h和CommandWithArgs.m中实现的'CommandWithArgs'类。当Cocoa接收到这个命令时，将实例化CommandWithArgs类的一个实例，然后调用该实例的-performDefaultImplementation方法。你把命令执行的代码放置在performDefaultImplementation方法中。
    - 每一个参数都具有与之相关联的cocoa键。例如，“rvalue”命令与“RealValue'相关联。在performDefaultImplementation方法中，调用[self evaluatedArguments]检索所有的参数。该evaluatedArguments方法返回一个每个参数和其对应的Cocoa键的NSDictionary。例如，在performDefaultImplementation方法里，调用[[self evaluatedArguments] objectForKey：@"RealValue"]检索“右rvalue”参数的值。当然，如果有一个参数被标记为可选的，并且用户没有提供该参数，那么该Cocoa键在NSDictionary中没有对应的条目。
    - 'preferred hand'参数说明了如何使用AppleScript枚举作为参数。有关使用枚举的详细信息，请参阅SimpleScriptingProperties例子。
    - 尽管我们已经定义了很多参数，动词总是会得到直接参数。在performDefaultImplementation方法，你可以通过调用[自directParameter]访问这个值。

    
    
    
    Step 4: 接下来是什么？
    
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
    
    该示例是一套示例中的一个，该套示例以下面的顺序排列，一个示例到另一个示例逐渐完善。如果你还没有看过任何一项示例，那么请参考来获取额外的信息。
    
    SimpleScripting (you are here)
    http://developer.apple.com/samplecode/SimpleScripting/
    
	SimpleScriptingProperties
    http://developer.apple.com/samplecode/SimpleScriptingProperties/
	    
	SimpleScriptingObjects
    http://developer.apple.com/samplecode/SimpleScriptingObjects/
	    
	SimpleScriptingVerbs
    http://developer.apple.com/samplecode/SimpleScriptingVerbs/
    
    
    
    ===========================================================================
	BUILD REQUIREMENTS:
	 
	Xcode 3.2, Mac OS X 10.6 Snow Leopard or later
	 
	===========================================================================
	RUNTIME REQUIREMENTS:
	 
	Mac OS X 10.6 Snow Leopard or later
	 
	===========================================================================
	CHANGES FROM PREVIOUS VERSIONS:
	 
	Version 1.2
	- Project updated for Xcode 4.
	Version 1.1
	- Changed the .sdef so that it uses XInclude to import the Standard Suite from another file instead of providing a definition of the Standard Suite in the .sdef file.
	Version 1.0
	- First version.
	 
	===========================================================================
	Copyright (C) 2008-2011 Apple Inc. All rights reserved.