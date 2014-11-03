###Technical Note TN1164
##Scripting Additions for Mac OS X

完全不知道我写了些什么

该技术说明描述了开发者用来为Mac OS X开发AppleScript脚本扩展的API。是为有兴趣开发脚本扩展的App开发者准备的。

- [What are Scripting Additions?]()

- [Packaging Scripting Additions]()

- [Mac OS X v10.6 (Snow Leopard) and later: Info.plist]()

	- [Handler]()

	- [ThreadSafe]()

	- [Context]()

- [Mac OS X v10.5 (Leopard): Info.plist]()

- [Mac OS X v10.4 (Tiger) and earlier: Required]()

	- [Functions]()

	- [Initialization]()

	- [Termination]()

	- [Reference Counting]()

- [Helpful Tips]()

	- [Runtime Considerations]()

	- [Locating Your Scripting Addition's Bundle Resources]()

	- [Local and Remote Requests]()

- [References]()

- [Downloadables]()

- [Document Revision History]()

###什么是脚本扩展

脚本扩展提供了一种传递AppleScripts扩展功能的机制。脚本扩展处理Apple event和Apple event数据的强制处理。

通过脚本扩展安装的Apple event handlers和Apple event数据强制处理handlers的编程方法与在app内部使用的处理相同。脚本扩展和App的不同之处是代码的打包方式和脚本扩展安装到系统上的机制。这些不同将会在接下来的章节中介绍。

[Back to Top]()


###打包脚本扩展

脚本扩展被打包为以``.osax``为扩展名的bundle，且其Info.plist文件内有一个``CFBundleSignature``条目。

**Note:**

[Back to Top]()

###Mac OS X v10.6 ()之后版本: Info.plist

Mac OS X v10.6(Snow Leopard) 使用数据驱动的方案来在脚本扩展中安装handlers。以下将描述的旧方法依然支持，但是10.6版本的Info.plist可以带来性能上的提升。10.6的格式不向后兼容；使用该方法的扩展只能在Mac OS X v10.6或更新的系统中使用。

``Info.plist``文件内必须包含``OSAXHandlers``键，对应的值是一个包含三个字典的字典类型。
- ``Events``：Event handlers
- ``DescCoercions``：使用AECoerceDesc接口的Coercion handlers
- ``PtrCoercions``：使用AESCoercePtr接口的Coercion handlers

每项的值是一个以8字符event code(event class或者event id)或"form" and "to" ``DescType`` value为键，以描述handler函数的字典为值的字典。有了该值，系统会自动安装和卸载handlers：开发者不需要编写初始化和清理的代码。比如，Listing 1中的Info.plist文件声明了一个``syso``/``dlog``事件(``display dialog``)的handler，一个将``STXT``转换为``utxt``的强制转换，一个将``TEXT``转换为``utxt``的强制转换。

**Listing 1:** 声明了脚本扩展函数的Info.plist片段。

	<key>OSAXHandlers</key>
	<dict>
    <key>Events</key>
    <dict>
        <key>sysodlog</key>
        <dict>
            <key>Handler</key>
            <string>DisplayDialogEventHandler</string>
            <key>ThreadSafe</key>
            <false/>
            <key>Context</key>
            <string>Process</string>
        </dict> 
    </dict>
    <key>DescCoercions</key>
    <dict>
        <key>STXTutxt</key>
        <dict>
            <key>Handler</key>
            <string>CoerceStyledTextToUnicodeText</string>
        </dict>
    </dict>
    <key>PtrCoercions</key>
    <dict>
        <key>TEXTutxt</key>
        <dict>
            <key>Handler</key>
            <string>CoercePlainTextToUnicodeText</string>
        </dict>
    </dict>
	</dict>
对于每个handler，字典包含了handler函数的名字，和一些event handlers的信息：是否要求线程安全，handler请求的内容，使用一下的key和value描述：

**Table 1:** Handler字典键

**Key** -------------- **Description**

Handler --------- A string; handler处理函数的名字。

ThreadSafe ---- A boolean; 该handler是否是线程安全的？(Event handerls only.)

Context --------- A string，one of Any，Machine，User，or Process；handler请求的内容.(Event handerls only.)


####Handler
handler处理函数的名字。该函数必须作为可执行文件的入口。(其所在文件名要与项目名相同)

####ThreadSafe
OSA和AppleScript在Mac OS X v10.6中是线程安全的。如果脚本工作在后台线程切掉用了一个线程不安全的event handler，该处理会被放到住线程中执行，比起直接在调用线程中执行，在主线程中可以更慢，且可能会降低用户输入的响应速度。最理想的情况是所有的handlers都是线程安全的。有些不是；比如，几乎所有UI操作在主线程中执行。我们鼓励无论是检查还是更新event handler，请保持其线程安全。

如果该键为true，则handler在多线程环境中可以运行在非主线程上。如果为false，则handler职能运行在主线程上。event handlers必须指定该键，coercion handlers必须不指定该键，因为coercion handlers必须是线程安全的。如果coercion handlers不能是线程安全的，handler必须将coercion运行在主线程上，通过libdispatch和main dispatch queue可以做到。

####Context
处于安全考虑，Mac OS X v10.6在必要的时候只在另一个进程中执行脚本扩展，因此某些情况下可能会请求用户认证。Context对应的值指定了脚本扩展的运行方式，指定为了确保函数正常运行handler需要的运行环境。在脚本中，脚本扩展的命令可能在一个``tell``块内，比如说远程计算机上的Mail.app，但是如果context足够宽松的话，脚本扩展会在另一个线程中执行因此降低风险。Context为以下四个值中的一个：

**Table2:** Context values

Value ---------- Description

Any ------------ handler只在乎自己的输入参数，可以在任何机器上的任何线程中运行。(比如，offset of，round。)

Machine ------ handler依赖运行的机器，必须在目标机器上的某个线程内运行。(比如，beep，system info.)

User ---------- handler依赖用户和组，一般来说是因为用户的设置或者权限，因此必须运行在使用相同用户id和组id的线程中。(比如，commands that perform file I/O such as open for access and info for。)

Process ----- handler依赖在不同线程中不一样的变量，因此必须运行在目标机器上的目标线程内。(比如，display alert, which layers the alert with the target application's windows.)

Context的值与之上的值存在相关性，因此User包含Machine，Process包含User和Machine。尽量以最小的依赖，因为越多的依赖意味着更多的安全风险。

该键只针对event handler，coercion handler因为总是与脚本工作在同一个线程中，因此不是必须的。     

###Mac OS X v10.5 (Leopard): Info.plist
###Mac OS X v10.4 (Tiger) and earlier: Required Functions
###温馨提示

####运行时的注意事项

您的脚本扩展会被加载到任意的app进程中，因此应小心不要影响扩展程序本身以外的全局状态，例如资源管理器(Resource Manager)的资源链，有效用户ID，等等。

脚本扩展分别加载到使用它们的每个应用程序进程。因此，你应该牢记，有可能你的脚本扩展在许多不同的应用程序中存在多个实例。一些脚本添加可能需要额外的代码，如果他们使用单一的共享资源，如打印机或一个串行端口。

####定位你的脚本扩展的包资源

脚本扩展可能需要访问位于其bundle内的资源和文件。要做到这一点，使用*CFBundleGetBundleWithIdentifier*来得到一个*CFBundleRef*，将你的脚本扩展的包标识符传递给它。

关于包引用和包格式的信息可以在在这篇文章的末尾的[References]()章节中找到。

####本地和远程请求

如果在远程计算机上执行你的脚本扩展没有意义或会构成安全风险，你的命令处理程序应检测和拒绝从远程计算机上传递的事件。处理程序可以通过检查输入事件的keyEventSourceAttr属性确定事件源。从远程系统发送的事件将具有kAERemoteProcess属性。

	DescType sourceAttr;

	err = AEGetAttributePtr(eventPtr, keyEventSourceAttr, typeType, NULL, &sourceAttr, sizeof(sourceAttr), NULL);
	if (err != noErr || sourceAttr == kAERemoteProcess)
	{
	    return errAELocalOnly;
	}	
[Back to Top]()

###参考资料
- [Apple Event Programming Guide]()
- [Bundle Programming Guide]()

[Back to Top]()

###可下载
- 脚本扩展项目。（"tn1164_SkeletonAddition.zip", 7.2K）

[Back to Top]()                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             

##Document Revision History

Date ---------- Notes

2009-05-29 ---- Updated for Mac OS X v10.6 with information about security and thread-safety Info.plist entries.

2008-04-24 ---- Add sample project.

2008-01-15 ---- Update to cover API changes for Leopard.

2004-04-26 ---- Unspecified content revisions.

2001-09-13 ---- New document that explains how to create AppleScript scripting additions (OSAX) for Mac OS X.



##Usages from WWW

###怎样安装脚本扩展?

From: [http://macscripter.net/viewtopic.php?id=24375](http://macscripter.net/viewtopic.php?id=24375)

####MacOS
Either drop it onto your closed System folder and it will be installed automatically, or put it in the "Scripting Additions" folder inside your System folder (in earlier versions of the Mac OS the Scripting Additions folder will be found inside the Extensions folder).

Chances are you will already notice several additions already there, installed as part of the original operating system...even AppleScript uses additions to its own commands!

If AppleScript is running you need to quit it and relaunch it. There is no need to restart the computer.

To uninstall a scripting addition, just drag it from the Scripting Additions folder to the trash. The next time you launch your script editor that particular scripting addition will not be present.
####MacOS X
To make the Scripting Addition available to only yourself, you should install it into the "ScriptingAdditions" folder in your "Users > UserName > Library" folder.  If this folder doesn't exist, you can create it.  However, make sure it's spelled "ScriptingAdditions" with no spaces.

To make the Scripting Addition available to all users, you should install it into the "ScriptingAdditions" folder in the root directory's "Library".

For a network, install it into "/Network/Library/ScriptingAdditions/".

For instance, if you run this code, the folder will be created automatically:

	Open this Scriplet in your Editor:
	path to scripting additions from user domain --> "local domain" or "network domain"
The commands and functionalities of the new osaxen will be available immediatelly.