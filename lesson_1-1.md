---
layout: page
title: "Setting up to work with the Universal API"
tagline:
description: "Understanding how to configure your Java development environment to work with the Travelport provided WSDL and make calls, via the internet with SOAP/XML, to their API."
---
{% include JB/setup %}

##  Unit 1, Lesson 1

### Objective of Unit 1

After working through the three lessons of Unit 1, you should be able to work with the Travelport Universal API to make requests for services and understand the responses.  You will become familiar with *web services* if you are not already.

### Programming language agnostic and WSDL

Although these lessons are using Java, the lessons' concepts apply to pretty much any programming language.  The _interface_ to the Universal API (uAPI) is defined with [WSDL](http://en.wikipedia.org/wiki/Web_Services_Description_Language) or Web Services Definition Language, pronounced "whiz-dul".

This means that any programming language that knows how to use *web services* can access the APIs and get useful things done.  In practice, this means that you need a _generator_ that can take a file "foo.wsdl" and spit out "foo_client.Java", or whatever, in your favorite language.

Important note: different generators have slightly different behaviors. WSDL is a standard, but some differences are a fact of life when dealing with multiple versions and different versions implemented by different vendors. In this tutorial, we'll be using [Apache's CXF](http://cxf.apache.org/) to work with the uAPI in Java. "CXF" is not an acronym, but is related to the project's origin.

### Requesting 30-day trial credentials

You must first create an account on [Travelport Developer Network](http://developer.travelport.com).

* Once you have an account, you can find the link to *Request Test Access* via the navigation menus on the [Universal API Resource Centre](http://developer.travelport.com/app/developer-network/resource-centre-uapi).

* When you request test credentials, you should choose *Galileo* as your preferred GDS.

* After you have filled out the form, in a few minutes you'll receive an email from *webmaster@travelport.com* with the relevant information.

### Downloading the tutorial code

You can download the tutorial code using *github* to clone the repository. You can use your favorite [git tool](https://git.wiki.kernel.org/articles/i/n/t/Interfaces,_frontends,_and_tools.html) or just use the command line like this:

{% highlight console %}
> mkdir learn-tport
> cd learn-tport
> git clone git@github.com:iansmith/travelport-uapi-tutorial.git
{% endhighlight %}

This last command will create the directory `travelport-uapi-tutorial`. Within that directory you will find the a few files that we will discuss later, plus the directories `src` and `wsdl`.  If you explore the `wsdl` directory, you'll see many WSDL files as well as a number of [XML schemas](http://en.wikipedia.org/wiki/XSD), as `xsd` files.  All of these files have been supplied by Travelport, and the latest version of the uAPI can be downloaded from [the developer site](http://developer.travelport.com).

For the rest of the tutorial we'll assume that you have created a Java (or web services) project in Eclipse and have put all the  code provided by tutorial at the top level of the project.  You will see a `src` and a `wsdl` directory when you have done this correctly.

### Apache CXF

Apache's CXF is the critical glue for connecting Travelport's web services to our Java tutorial code. There are plenty of other choices for interacting with web services in Java, but CXF is just a very popular one.

You'll need to [download and install](http://cxf.apache.org/download.html) the Apache CXF code. This tutorial expects you to be using at least version 2.6.0.

#### Using Eclipse

If you are using [Eclipse](http://www.Eclipse.org) like most Java developers, you can install the CXF directly into Eclipse by going to "Preferences > WebServices".  If you don't have this preference option, you'll need to install the [Web Tools Platform](http://www.Eclipse.org/webtools/), or WTP, for Eclipse. The preferences screen where you add your installation of CXF should look something like this (with the preference choice highlight by the red box).  

<br/>
<img src="images/preferences.png"/>
<br/>


#### Not using Eclipse

If you are not using Eclipse, you'll need to be sure that the CXF jar files are [in your classpath](http://docs.oracle.com/Javase/tutorial/essential/environment/paths.html) and that you can run the command 'cxf' from the command line.

Be aware that there many Java libraries that are "subsumed" by CXF, so you'll end up with about 75 libraries in your classpath!


### Generating a Java client for the uAPI

We now need to generate Java code from the WSDL and XSD files supplied by Travelport's uAPI.

Let's start by generating the Java code for the `System` service.  In Eclipse, you can do this by selecting the WSDL file `System.wsdl` in the directory `wsdl/system_v8_0` and then using the context-menu in Eclipse to choose "Generate Client", as is shown here:

<br/>
<img src="images/generate-client-menu.png"/>
<br/>

You'll then be presented with a sequence of three dialog boxes connected by hitting the "Next" button.  The vast majority of these options are really not of much interest to us for this tutorial, but you can verify your configuration against this set of screenshots:

<br/>
<img src="images/generate-client-dialog1.png"/>
<br/>
<img src="images/generate-client-dialog2.png"/>
<br/>
<img src="images/generate-client-dialog3.png"/>
<br/>


On the first screenshot in this sequence, the red box indicates a slider.  This slider can be moved to another position, the *non error* position, if you are the type of person who doesn't like having "errors" on your screen.

The last dialog box shows a number of checkboxes selected. These control various options in CXF's generation of the Java code.  The lower set of checkboxes that are checked (such as -Xts and -Xts:multiline) are not crucial to having the tutorial work properly, but do make debugging easier.

#### Command line generationof a Java client

If you are using the command line, you can do manually what Eclipse does behind the scenes based on the values given in these dialogs. Below is a very complex command line generated by Eclipse that you can start from, although you will need to adjust the paths to your local machine. 

{% highlight console %}
wsdl2Java -client -d /Users/johndoe/tport-workspace/uapiJava/.cxftmp/src -classdir /Users/johndoe/tport-workspace/uapiJava/build/classes -p http://www.travelport.com/service/system_v8_0=com.travelport.service.system_v8_0 -impl -validate -exsh true -dns true -dex true -autoNameResolution -xjc-Xts,-Xts:style:multiline,-Xlocator,-mark-generated -wsdlLocation http://localhost:8080/kestrel/ExternalCacheAccessService?wsdl -verbose -fe jaxws -db jaxb -wv 1.1 file:/Users/johndoe/tport-workspace/uapiJava/wsdl/system_v8_0/System.wsdl
{% endhighlight %}


### The Java client for "System"

If you use the WDT viewer for a WSDL file, you will see this image when you open `wsdl/system_v8_0/System.wsdl`. There are tabs at the bottom of the main editor view to control whether you view this file in "design view" or as a normal source code file.

<br/>
<img src="images/SystemService.png"/>
<br/>

This diagram shows you that there are two *services* exposed by`System` : `ExternalCacheAccessService` and `SystemService`.  Often we will get sloppy with the nomenclature and refer to the "system service" as any object that is accessible from objects _generated from the System.wsdl file_.  In this case, the real, concrete class `SystemService` is derived from `System.wsdl` so there is less confusion, but this can become more confusing with other WSDL files, like `Air.wsdl`, that expose many "services" as a by-product of generating the code for the "Air service." Whew, a lot of uses of "service"!
 
For the rest of this lesson we will be working only with `SystemService`.

You can see the Java code that has been generated for this service in your project's `src` folder, the class `com.travelport.service.system_v8_0.SystemService`.  Although you are welcome to read and explore the source, the good news is that you can *safely ignore* all the implementation details about this service.  The beauty of WSDL is not knowing!

Referring back to the diagram for `SystemService` above, you'll see that there are three "ports" exposed by the `SystemService`: `SystemInfoPortType`, `SystemPingPortType`, and the `SystemTimePortType`.  In the code for this lesson, we'll run a simple ping request through the `SystemPingPortType`.

### The programming model

The pattern used by the uAPI design is to expose a *port* which has a single method called *service*.

Usually we refer to the port object just by its name without the prefix or suffix such as "the ping port", or just "ping". All the ports exposed from the file `System.wsdl` start with `System` and end with `PortType`.

The ping port's only method is `service`, as you can see from the diagram. Far more interesting: the diagram shows you the input and output parameters are of type `PingReq` and `PingRsp`, for the request and response respectively.  You can find the source code for these classes in the `src` Java folder with the name `com.travelport.schema.system_v8_0.PingReq`, or you can explore them with design view of `System.wsdl`.  

Again, the details of the implementation are not important to you, you can just _use_ these facilities as part of the uAPI.

We now have the logical pieces necessary to understand how to use functionality exposed by the uAPI.  Let's think about this sequence of actions with the ping port as an example:

1. Create an object of type `PingReq`
2. Fill in the necessary fields of the `PingReq` using its "setter" methods
3. Create an instance of `SystemService`
4. Access the `SystemService` object to get an object of type `SystemPingPortType`
5. Call the method `service` on the `SystemPingPortType` instance, passing the `PingReq` object created in step 1
6. Examine the results of our request by looking at the `PingRsp` object using its "getter" methods

With very few exceptions, all the features and functions of the uAPI follow this pattern of "first build the request parameters and then use the port object to get the results."

### Lesson 1 proper

The file  [Lesson1.Java](https://github.com/iansmith/travelport-uapi-tutorial/blob/master/src/com/travelport/uapi/unit1/Lesson1.Java) is in the Java package [com.travelport.uapi.unit1](https://github.com/iansmith/travelport-uapi-tutorial/tree/master/src/com/travelport/uapi/unit1).  You'll notice that in the web-viewable version of this tutorial, we often make the names of files and packages direct links into the github repository for the tutorial.  This makes it easy for those that just want to read the tutorial without "playing along at home" by compiling/running all the examples.

Let's examine the `main` method of Lesson 1:

{% highlight java %}

public static void main(String[] argv) {		
	//
	// PING REQUEST
	//
	String payload= "this my payload; there are many like it but this one is mine";
	String someTraceId = "doesntmatter-8176";
	
	//set up the request parameters into a PingReq object
	PingReq req = new PingReq();
	req.setPayload(payload);
	req.setTraceId(someTraceId);
	
	try {
		//run the ping request
		PingRsp rsp = WSDLService.getPing(false).service(req);
		//print results.. payload and trace ID are echoed back in response
		System.out.println(rsp.getPayload());
		System.out.println(rsp.getTraceId());
		System.out.println(rsp.getTransactionId());
	} catch (SystemFaultMessage e) {
		//usually only the error message is useful, not the full stack
		//trace, since the stack trace in is your address space...
		System.err.println("Error making ping request: "+e.getMessage());
	}
}

{% endhighlight %}


The code in `main()` above follows exactly the pattern explained in the programming model section: we set up a `PingReq` object with the proper parameters and then pass them through to the uAPI via the ping port.

The only unexpected part of this simple example is the call to `WSDLService.getPing(false)` that returns the ping port object. The _false_ parameter indicates that you do not want a copy of the XML input and output sent to the console; setting this to _true_ can often help your understanding of what is happening!

`WSDLService` is a helper class that has been provided with the tutorial to simplify things, and allow you to focus on the essential parts of the uAPI.

### The details

You will need to configure your copy of the class `WSDLService` slightly to allow you to use the uAPI. You should not need to do this again!

If you look inside [WSDLService](https://github.com/iansmith/travelport-uapi-tutorial/blob/master/src/com/travelport/uapi/unit1/WSDLService.Java) in the package [com.travelport.uapi.unit1](https://github.com/iansmith/travelport-uapi-tutorial/tree/master/src/com/travelport/uapi/unit1) you will see the things that you may need to change:

{% highlight java %}
// these endpoint parameters vary based on which region you are
// in...check your Travelport sign up to see which url you should use...
static protected String SYSTEM_ENDPOINT = "https://emea.universal-api.travelport.com/B2BGateway/connect/uAPI/SystemService";
static protected String AIR_ENDPOINT = "https://emea.universal-api.travelport.com/B2BGateway/connect/uAPI/AirService";
{% endhighlight %}

The URLs above, called "endpoints" in the parlance of WSDL, may need to be changed if you are not in the EMEA (_Europe, Middle East, Africa_) geography.  The functionality is the same from any endpoint, the only difference are which servers are used to provide the functionality.

If you try to run the `Lesson1` code either from the command line or from Eclipe's Run menu you'll get an error like this:

{% highlight console %}
Exception in thread "main" Java.lang.RuntimeException: One or more of your properties has not been set properly for you to access the travelport uAPI.  Check your command line arguments or Eclipse run configuration for these properties:travelport.username,travelport.password,travelport.gds,travelport.targetBranch
{% endhighlight %}


This error indicates that you have not set some "Java properties" (often done using the -D option from the command line) in your Run configuration.  You access or create run configurations in Eclipse with "Run" then "Run Configuration..." from the primary Eclipse menubar.  Here is a screenshot of how to configure your (and Lesson 1's) environment via the "Arguments" tab and supplying "VM arguments":

<br/>
<img src="images/runconfig.png">
<br/>

The red boxes show you the tab you need to use to get to this configuration option and where to supply the values.

The values are included in your sign-up documents from the Travelport website.  Note that each of the parameters is of the form -Dxxx="yyy" and are separated by spaces.  The values are always quoted and the names always begin with "travelport".

After you have made the adjustments necessary for your account to this panel, return to the [WSDLService](https://github.com/iansmith/travelport-uapi-tutorial/blob/master/src/com/travelport/uapi/unit1/WSDLService.Java) file one last time.  You'll see the `URLPrefix` value is set like this near the top of the file:

{% highlight java %}
static protected String URLPREFIX = "file:///Users/iansmith/tport-workspace/uapiJava/";
{% endhighlight %}


This example shows a path on the tutorial developer's home machine.  You should adjust this to be the prefix of a URL that points to the root of your project. Note that URLs start with "file:///"  and use forward slash (/) to separate directories, even if you are running on the Windows operating system.  You need to be sure to include a trailing slash as well, beacuse various other values are derived from `URLPREFIX` by concatenation.

After you have made this adjustment, you should be able to run Lesson 1 via your run configuration created above.  If you see an error like this:

{% highlight console %}

Exception in thread "main" Javax.xml.ws.WebServiceException: org.apache.cxf.service.factory.ServiceConstructionException: Failed to create service.
	at org.apache.cxf.jaxws.ServiceImpl.<init>(ServiceImpl.Java:150)
	at org.apache.cxf.jaxws.spi.ProviderImpl.createServiceDelegate(ProviderImpl.Java:97)
	at Javax.xml.ws.Service.<init>(Service.Java:56)
	at com.travelport.service.system_v8_0.SystemService.<init>(SystemService.Java:41)
	at com.travelport.uapi.unit1.WSDLService.getPing(WSDLService.Java:133)
	at com.travelport.uapi.unit1.Lesson1.main(Lesson1.Java:23)
Caused by: org.apache.cxf.service.factory.ServiceConstructionException: Failed to create service.
	at org.apache.cxf.wsdl11.WSDLServiceFactory.<init>(WSDLServiceFactory.Java:94)
	at org.apache.cxf.jaxws.ServiceImpl.initializePorts(ServiceImpl.Java:204)
	at org.apache.cxf.jaxws.ServiceImpl.<init>(ServiceImpl.Java:148)
	... 5 more
Caused by: Javax.wsdl.WSDLException: WSDLException: faultCode=PARSER_ERROR: Problem parsing 'file:/Users/iansmith/tport-workspace/uapiJava/wsdl/system_v8_0/System.wsdl'.: Java.io.FileNotFoundException: /xUsers/iansmith/tport-workspace/uapiJava/wsdl/system_v8_0/System.wsdl (No such file or directory)
	at com.ibm.wsdl.xml.WSDLReaderImpl.getDocument(Unknown Source)
	...
{% endhighlight %}


Then you have misconfigured the `URLPREFIX` above and you should re-check the value you gave for it. It should point to the directory that is the _parent_ of the `wsdl` and `src` directories in your project.

### The pay-off

The final result of all this work (hopefully the last time you'll need to do this type of thing in this tutorial for a long while) is when you can run `Lesson1` and see this output like this:

{% highlight console %}
this my payload; there are many like it but this one is mine
doesntmatter-8176
E07E825F0A07580E004DED8EB9130465
{% endhighlight %}


The last value is a _transaction ID_ that is unique to each call on the uAPI, so it will differ from what is shown here.

When you see this output, you have run a "ping" to the Travelport service and successfully gotten back the values transmitted.  Although this seems trivial, this is good news because it means that all the layers of software and networking (and there are plenty!) between your computer and Travelport's servers are working as expected.

### The other gunk

You will also probably see some messages (usually in red, because they are directed to the system's default error log) like this in your Eclipse console:

{% highlight console %}
INFO: Creating Service {http://www.travelport.com/service/system_v8_0}SystemService from WSDL: file:/Users/iansmith/tport-workspace/uapiJava/wsdl/system_v8_0/System.wsdl
{% endhighlight %}


This is a helpful message provided by the CXF framework to tell you it is creating a service based on a WSDL file and giving you some information about the endpoint it is using.  You can control the "chattiness" of the CXF infrastructure by adjusting the values in the [logging.properties](https://github.com/iansmith/travelport-uapi-tutorial/blob/master/src/com/travelport/uapi/unit1/logging.properties) file, just as with most other Java applications.  Most people can safely ignore these messages, unless they are printed out to the `Warning` or `Error` logging levels.

### Exercises for the reader

 * Create a run configuration as above but for the [SystemSvcTest](https://github.com/iansmith/travelport-uapi-tutorial/blob/master/src/com/travelport/uapi/unit1/SystemServiceTest.Java) test code.  This is a JUnit4 style test suite so will require the appropriate configuration type in the Run Configurations menu.

 * Change the ping values in `Lesson1` and prove to yourself that the values being returned are the values you supplied.

 * Modify the code in Lesson1 to use the "time port" (`SystemTimePortType`).  This will require that you discover how to get access to different ports (via the `WSDLService` helper class), and that you supply different request and response types to match the appropriate types for this different function.   The `time port` returns the current time according to Travelport if you succeed in calling it.

----------------------

[Proceed to Unit 1, Lesson 2 >](lesson_1-2.html)

[Table of Contents](index.html)
