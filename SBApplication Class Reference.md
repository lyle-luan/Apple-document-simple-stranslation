https://developer.apple.com/library/mac/documentation/Cocoa/Reference/SBApplication_Class/index.html#//apple_ref/occ/cl/SBApplication
https://developer.apple.com/library/mac/documentation/Cocoa/Reference/SBApplication_Class/
##SBApplication

``SBApplication``类为Objective-C程序提供一个向脚本程序发送和接收Apple events的自动化机制。因此，Objective-C程序可以控制脚本程序并相互交换数据。Scripting Bridge将Apple event和Cocoa objects的数据类型联系起来。

虽然``SBApplication``包含了手动发送和处理Apple events的方法，但是不要直接调用这些方法。``SBApplication``的子类扩展处理特定App的方法，来自动处理Apple events的发送。

比如，如果你想获取iTunes中的曲目，你可以简单的使用为iTunes动态生成的子类定义的``currentTrack``方法来替你处理发送Apple evnets的细节，而不用使用复杂、低级的其他方法：

``[iTunes propertyWithCode:'pTrk'];``

如果确实要手动发送Apple events，考虑使用``NSAppleEventDescriptor ``类。

###Subclassing Notes

不需要直接实例化``SBApplication``对象。而应该调用``applicationWith...``类方法来获得共享实例，使用bundle identifier，process identifier，或者URL指定App。

##以上算翻译完了，具体的方法就不翻了，因为如果我没用熟的话，很可能有错误，虽然上面的文字也会有很多错误。

##下面是``SBApplication``中的一个例子**SBSendEmail**的**README.TXT**的翻译

##ReadMe.txt
	SBSendEmail
	===========



	说明:
	该文件详细描述了怎样攒一个为了能自动发送邮件而使用Scripting Bridge向Mail发送Apple events的工程步骤。
	
	
	1.新建一个Cocoa程序
	
	因为我们使用Mail来发送邮件，所以我们称这个工程为 'SBSendEmail'。这里我们示范的技术可以用于任何Cocoa程序。为了保持简单，该App我们使用单视图工程。
	
	该项目自带Interface Builder的.nib文件和Controller对象，且已经设立联系。我们假定读者熟悉Cocoa编程的这部分，所以将这些部分组合的机制，这里不赘述。
	
	2.给工程添加Scripting Bridge编译条件
	
	要做的第一件事，是让Xcode自动生成能触发目标App的Scripting Bridge代码。
	
	(a) 在project navigator中，选择工程文件，然后选择"SBSendEmail"目标。(./SBSendEmailPic/1)
	(b) 选中"SBSendEmail"目标后，切换到"Build Rules"标签。(./SBSendEmailPic/2)
	(c) 在"Build Rules"标签，点击＋来添加新的规则。(./SBSendEmailPic/3)
	(d) 增加一下的新规则：
		Process 'Source files with names matching:'   *.app
   
    	using: 'Custom Script'
   
    	set the script field to:
   
    	sdef "$INPUT_FILE_PATH" | sdp -fh -o "$DERIVED_FILES_DIR" --basename "$INPUT_FILE_BASE" --bundleid `defaults read "$INPUT_FILE_PATH/Contents/Info" CFBundleIdentifier`
 
    	click on the "+" icon below the 'with output files:' field, and then set the field to contain:
    
    	$(DERIVED_FILES_DIR)/$(INPUT_FILE_BASE).h 
    	
		注意：如果你自己输入上面的规则，请保证文本只有一行且没有错误。直接复制粘贴是不错的选择。
		
	(e) 搞定。Xcode会为任何加入到该工程的程序生成Scripting Bridge代码。
	
	注意：该规则使用了sdef和sdp命令行工具。如果想详细了解他们，可以在Terminal中输入：
		man sdp
		man sdef
		
	3.选择目标程序
	
	将目标程序拖拽到工程内。(./SBSendEmailPic/4)
	
	你可以将目标程序的源代码拖拽进该工程。因为我们在第二步中添加的编译规则，Xcode将Mail程序当作源代码文件。也就是说会编译咯。
	
	不要选择'Copy items into destination group's folder (if needed)'选项，不然目标App会拷贝到你的工程中。添加目标App时，选择'Create folder references'，这样该工程可以在其他机器上编译而不用改变配置。（比如，Mail总是存放在System/Library/CoreServices目录下）。(./SBSendEmailPic/5)
	
	这样，我们讲Mail加入到工程。Mail App存放在/Applications目录下。
	
	4.将目标程序加入到编译源
	
	你已经将目标程序加入到工程中，还需要将其加入到程序目标文件的编译源中。(./SBSendEmailPic/6)(./SBSendEmailPic/7)
	
	5.将Scripting Bridge framework添加到工程中
	
	在程序目标文件的'Build Phases'选项里，展开'Link Binary With Libraries'。点击＋并选择ScriptingBridge.framework。(这个大家都会就不上图了)
	
	6.在Info.plist指定最小系统版本
	
	因为ScriptingBridge.framework在Mac OS X 10.5之前不存在，所以在Info.plist文件内指定该App只能运行在10.5之后的系统上。
	
	<key>LSMinimumSystemVersion</key>
     <string>10.5</string>
    
   	你可以在resources section点击icon来编辑Info.plist文件，也可以点击SBSendEmail目标，点击Info icon，选择Properties标签，然后点击"Open Info.plist as File".
    
	7.编译工程
	如果你正确的执行了以上几步，Xcode会生成好Scripting Bridge代码。代码会被放在编译目录下，使连接器和编译器能使用它们。
	
	我们添加的编译规则会为每个App生成一个名字一样的.h文件。比如，添加了Mail App，编译规则就会创建Mail.h。这些文件会被存放在DerivedSources目录内的编译路径下，以便编译器可以找到。
	
	Debug编译时，Mail.h文件被放置在编译目录的该子目录下：
	/build/Intermediates/SBSendEmail.build/Debug/SBSendEmail.build/DerivedSources/Mail.h
	
	Release编译时，Mail.h文件被放置在编译目录的该子目录下：
	/build/Intermediates/SBSendEmail.build/Release/SBSendEmail.build/DerivedSources/Mail.h
	
	你可以在文件菜单中使用'Open Quickly'命令来方便的打开和检查这些文件。一般来说，.h文件内包含最有趣的信息，所以你应该打开'Mail.h'文件看一下。
	
	有些情况下，主要取决于工程使用的框架，'Open Quickyly'命令可能打开的是系统的'Mail.h'文件，其中包含文件系统和Mail App本身使用的常量和定义。如果你打开的是系统的Mail.h文件，你就需要进入编译目录找到正确的头文件。
	
	8.添加Mail App的Scripting Bridge头文件
	
	在Controller.m中，我们已经在文件最上面的'#import "Controller.h"'下面添加了'#import "Mail.h"'语句。这样就包含了所有Mail的Scripting Bridge定义。
	
	以下的代码很重要：
	
		#import "Controller.h"
    	#import "Mail.h"
    	
    在本工程里，我们会在Controller类中的三个方法里使用Scripting Bridge接口，因此我们在Controller.m内引入该头文件。
    
    9.发送邮件的代码
    
    目前，我们已经描述了怎样组建使用Scripting Bridge来与Mail App交互的工程的预备步骤。本工程里示范的发送邮件的方法和代码相对要简单。在被节，我们讲解下Controller类的-sendEmailMessage:方法。
    
    以下是该方法的代码：
    
    - (IBAction)sendEmailMessage:(id)sender 
    {
    	/* create a Scripting Bridge object for talking to the Mail application */
    	MailApplication *mail = [SBApplication applicationWithBundleIdentifier:@"com.apple.Mail"];
    	
    	/* set ourself as the delegate to receive any errors */
    	mail.delegate = self;

		/* create a new outgoing message object */
    	MailOutgoingMessage *emailMessage = [[[mail classForScriptingClass:@"outgoing message"] alloc] initWithProperties:[NSDictionary dictionaryWithObjectsAndKeys:[self.subjectField stringValue], @"subject",[[self.messageContent textStorage] string], @"content",nil]];

		/* Handle a nil value gracefully. */
    	if(!emailMessage)
			return;
 
      	/* add the object to the mail app  */
    	[[mail outgoingMessages] addObject: emailMessage];
 
        /* set the sender, show the message */
    	emailMessage.sender = [self.fromField stringValue];
    	emailMessage.visible = YES;
 
        /* create a new recipient and add it to the recipients list */
    	MailToRecipient *theRecipient = [[[mail classForScriptingClass:@"to recipient"] alloc] initWithProperties:[NSDictionary dictionaryWithObjectsAndKeys:[self.toField stringValue], @"address",nil]];
        /* Handle a nil value gracefully. */
    	if(!theRecipient)
        	return;
    	[emailMessage.toRecipients addObject: theRecipient];
    	[theRecipient release];
 
        /* add an attachment, if one was specified */
    	NSString *attachmentFilePath = [self.fileAttachmentField stringValue];
    	if ( [attachmentFilePath length] > 0 ) 
    	{
        	MailAttachment *theAttachment;
 
        	/* In Snow Leopard, the fileName property requires an NSString representing the path to the 
             * attachment.  In Lion, the property has been changed to require an NSURL.   */
        	SInt32 osxMinorVersion;
        	Gestalt(gestaltSystemVersionMinor, &osxMinorVersion);
 
        	/* create an attachment object */
        	if(osxMinorVersion >= 7)
            	theAttachment = [[[mail classForScriptingClass:@"attachment"] alloc] initWithProperties:[NSDictionary dictionaryWithObjectsAndKeys:[NSURL URLWithString:attachmentFilePath], @"fileName",nil]];
        	else
        	/* The string we read from the text field is a URL so we must create an NSURL instance with it
                 * and retrieve the old style file path from the NSURL instance. */
            	theAttachment = [[[mail classForScriptingClass:@"attachment"] alloc] initWithProperties:[NSDictionary dictionaryWithObjectsAndKeys:[[NSURL URLWithString:attachmentFilePath] path], @"fileName",nil]];
 
        	/* Handle a nil value gracefully. */
        	if(!theAttachment)
            	return;
 
        	/* add it to the list of attachments */
        	[[emailMessage.content attachments] addObject: theAttachment];
 
        	[theAttachment release];
    	}
    	/* send the message */
    	[emailMessage send];
 
    	[emailMessage release];
    }
	
	有趣的注意事项：
	
	a.我们在刚创建新的outgoing method后立即将其加入到Mail App的outgoingMessages中。这样会迫使Scripting Bridge向Mail App发送apple events时要求Mail App为Scripting Bridge委托对象创建一个实际的scripting对象。之后，我们可以通过委托对象设置scripting对象的属性，而不引起任何麻烦。
	
	b.当创建新对象时，为新对象提供属性的字典中的属性名使用Objective-C的健来指定。比如，当创建新的attachment时，我们使用Cocoa的健"fileName"来作为AppleScript的"file name"属性的名字。
	
	c.在创建新对象时，我们不使用Cocoa健来指定类名。比如，当创建outgoing email message时，我们使用AppleScript名字"outgoing message"来命名类。
	
	d.在-chooseFileAttachment:方法中，NSOpenPanel被设置为允许搜索路径(通过-setCanChooseDirectories:)。在Mail App中为outgoing mail message指定路径时，Mail会将其处理为.zip附件，与信息一起发送。
	
	e.在本例中我们只提供了添加一个附件的方法。因为我们要保证用户界面简单。然而，通过修改-sendEmailMessage:可以很简单使其能添加多个附件。
	
	首先，不想翻。
	
	11.更多探索
	
	Scripting Bridge编程文档可以在以下地址的Scripting Bridge Release Note中找到：
	
		http://developer.apple.com/releasenotes/ScriptingAutomation/RN-ScriptingBridge/index.html
	
	Scripting Bridge使用的命令行工具有man手册页可供参考。在Ternimal中使用如下命令查看：
	
	man sdp
	man sdef
	
	还有其他的Scripting Bridge示例可供参考，比如ScriptingBridgeiCal和ScriptingBridgeFinder展示了如果使用App名字来调用Scripting Bridge的方法。
	

##SBObject

``SBObject``类声明了脚本程序的对象都能调用的方法。定义了获取对象的elements和properties的方法，还有设置对象新值的方法。

每个``SBObject``都有一个对象标示符，使Scripting Bridge能定位该对象。因此，你可以认为``SBObject``是目标App的某个对象，而不是自己工程的对象。To bypass this reference-based approach and force evaluation, use the get method.

一般来说，比起直接创建一个``SBObject``实例，你应该使用子类``SBApplication``来得到一个``SBObject``对象。比如，如果你想得到代表iTunes曲目的``SBObject``对象，你可以使用以下代码（``iTunesTrack``是``SBObject ``的子类）：

	iTunesApplication *iTunes = [SBApplication applicationWithBundleIdentifier:@"com.apple.iTunes"];
	iTunesTrack *track = [iTunes currentTrack];
	
你可以在由``sdp``生成的头文件里发现这些动态生成的类的名字，比如``iTunesApplication``和``iTunesTrack``。