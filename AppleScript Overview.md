##AppleScript概述的简介

本文档高度概括的描述了AppleScript和其相关技术，帮助你决定在哪里可以使用它们。

Note：想要了解OS X提供的脚本技术有哪些，可以参看*Getting Started With Scripting & Automation*。

AppleScript脚本语言可以直接控制脚本化的app和Mac OS系统的很多方面。脚本化app可以响应AppleScript对app的操作和数据的请求，这些请求被称为：Apple events。

脚本化app，用户可以编写脚本来实现自动化，开发者可以使用AppleScript作为快速构建原型和自动化测试的辅助工具。开发者也可以使用Apple events，AppleScript，Automator和Scripting Bridge来利用其他app和Mac OS系统提供的功能。

AppleScript和Apple events都基于Open Scripting Architecture，该体系结构由几个OS X框架共同实现。Apple提供了很多额外的app和技术来加强AppleScript或者利用它。

###谁应该阅读该文档

你应该先阅读*AppleScript Overview*，对AppleScript和相关自动化技术有个广泛了解，来决定在开发中怎么使用它们。

编写AppleScript脚本，并想了解其背后技术的人，也可以阅读该文档。

*AppleScript Overview*适用于普遍的开发者群众，但是有脚本语言经验会很有帮助。如果之前没有接触过脚本语言，可以先阅读*Getting Started with AppleScript*。

###文档组织

文档包含以下几点：
- [关于AppleScript]() 介绍AppleScript，说明你什么时候应该使用它和它的一些限制。
- [Open Scripting Architecture]() 介绍实现AppleScript和Apple evnets的底层技术。以及如果扩展AppleScript。
- [使用AppleScript编写脚本]() 提供简单的使用AppleScript的教程。以及AppleScript与其他脚本结合的方法。
- [脚本化App]() 解释脚本化App的工作原理，包括脚本术语的制订，介绍可以用来创建脚本化App的资源。
- [Scripting Bridge]() 介绍Mac OS X v10.5首次发布的使用Objective-C访问脚本化App的技术。
- [Automator]() 介绍Apple的图形化自动程序，和开发者应该怎么利用它。
- [支持AppleScript的工具和应用]() 介绍一些运用了AppleScript技术或者提供让AppleScript可以利用特性的工具和应用。

###参阅

你可以在*Getting Started with AppleScript*中找到有关AppleScript和相关技术的其他介绍。

整篇*AppleScript Overview*文档也有相关文档的链接。

##关于AppleScript

**AppleScript**脚本语言可以直接控制脚本化的app和Mac OS系统的很多方面。**脚本化app**可以通过执行操作或者提交数据来对各种Apple events作出回应。**Apple event**是一种可以封装命令和任意复杂数据的进程间通信消息。由于提供了支持这些自动化的API，[Open Scripting Archicture]()称为OS X系统中最重要的特性之一－－编写能自动化操作多个程序的脚本的能力。

你可以使用AppleScript脚本来执行重复工作，自动化复杂的工作流程，控制本地或远程计算机的应用，访问Web服务。因为脚本编程者可以利用任意脚本化App的功能，因此他们可以将多个App的功能结合起来。比如，某个脚本可以远程调用Web服务器来获取股票行情，将当前股票价格添加到数据库，然后使用电子表格App可视化数据库中的数据。通过控制图像处理工作流来对App进行质量保障测试，AppleScript使自动化成为可能。

虽然AppleScript脚本语言（AppleScript的在语言指南说明，并在一些具体的第三方书籍）使用简单的英语式的术语，但是它是一个丰富的，面向对象的语言，能够进行复杂的编程任务。但其真正的价值来自于其能访问脚本化App提供的可用功能。如果你让你的App脚本化，它会帮助脚本编写者完成工作，并且很可能成为其工作流程不可缺少的一部分。

[Automator]()，随Mac OS X v10.4首次发布，让用户工作在一个图形界面中整合复杂的，自动化的工作流程。工作流包括一个或多个动作，这些动作由Apple公司，由开发者，并且由脚本开发者提供，并且可以使用AppleScript和其它语言编写，包括Objective-C。在Mac OS X v10.5中，开发者可以将工作流程直接纳入应用程序中，提供了另一种机制来访问其他应用程序和Mac OS的功能。

[Scripting Bridge]()，随Mac OS X v10.5首次发布，提供了为脚本化App创建Objective-C接口的自动化过程。这使得Cocoa应用程序和其他Objective-C代码使用原生的Objective-C语法能有效地访问脚本化App。其他一些脚本语言，如Ruby和Python，可以使用Scripting Bridge，但也有自己的访问的脚本化App的软件桥梁，为获取更多信息，请参阅*Getting Started With Scripting & Automation*。

AppleScript在OS X v10.5有其他几个新的或改进的功能，包括Unicode文本支持，识别和应用脚本对象的额外支持，64位支持，更准确和有用的错误信息，更多Apple公司App脚本化，比如iChat和Dock。欲了解更多信息，请参阅[AppleScript Features]()。

###什么时候该使用AppleScript

以下是你可以使用AppleScript或相关的技术开发的常见工作场景。
- 您要创建或更新OS X App，你希望它是脚本化的。作为一个脚本化App，用户可以在自己的AppleScript脚本中调用它，你可以编写脚本在开发过程中进行自动化测试。你也可以使App被Automator的用户访问，或者使用Objective-C，Ruby和Python等语言通过Apple的Scripting Bridge访问，或通过开源Bridge技术对其进行访问。

	有关这些技术的信息，请参阅本文档中的[Scriptable Applications]()，[Scripting Bridge]()，和[Automator]()章节，还有*Getting Started With Scripting & Automation*和*Getting Started with AppleScript*中的相关内容。 

	在*Apple Events Programming Guide*文档的*About Apple Events*章的“Framework and Language Support”节中，描述了使用面向对象的语言和使用Carbon API或者Cocoa程序框架编写脚本化App的利弊权衡。
- 如果你有兴趣自动重复操作，无论是在开发还是在其他工作中，使用脚本或Automator的工作流程。
 
	有关使用脚本的信息，请参阅本文档中的[Scripting with AppleScript]()，以及*Getting Started with AppleScript*中的相关内容。 

	要了解扩展了AppleScript并帮助它与图形图像，XML，属性列表，数据库等技术协同工作的App和技术，参阅[AppleScript Utilities and Applications]()。

###AppleScript的限制

AppleScript的脚本语言擅长调用多个应用程序，不能单独实现特定任务。因此，例如，您不能使用AppleScript有效的执行密集的数学运算或冗长的文字处理。但是，您可以结合使用shell脚本，Perl脚本和其他脚本语言调用AppleScript。这可以让你用最有效的语言完成手头工作。有关相关信息，请参阅[ Using AppleScript with Other Scripting Systems]()。 

AppleScript依赖于开发人员开发脚本化App。但是，一中被称为GUI脚本的机制，随OS X v10.3版本推出，允许那些不包含Apple events响应代码的App脚本化。欲了解更多信息，请参阅[System Events and GUI Scripting]()。

##Open Scripting Architecture 
##使用AppleScript编写脚本
##脚本化App

**脚本化App**不只是回应Mac OS为了让AppleScript脚本或其他App能访问其最重要数据和操作而发送的Apple events的App。要做到这一点，App必须提供一个脚本编写者使用的术语和底层的Apple event code来支持。Carbon和Cocoa应用程序都可脚本化，Cocoa框架包含内置的支持，最大限度地减少你需要编写的代码。

###指定脚本术语

脚本App提供一个脚本字典描述他们支持的脚本术语。字典指定App支持的命令和objects，以及其他AppleScript或app本身使用的信息，或者被其他app和脚本想要使用。由该希望利用该应用程序的脚本化的其它应用程序或脚本对象的应用程序支持，以及其它信息，用于通过AppleScript的或应用程序本身，以及可能的。有关设计的脚本术语的信息，请参见技术说明TN2106，脚本界面指南。

