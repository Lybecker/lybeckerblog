---
id: 324
title: How to view default values for a WCF binding
date: 2009-04-23T21:23:51+01:00
author: Anders Lybecker

guid: http://www.lybecker.com/blog/?p=324
permalink: /2009/04/23/view-default-values-for-a-wcf-binding/
dsq_thread_id:
  - "3460944399"
  - "3460944399"
categories:
  - .Net
  - Useful tools
  - WCF
tags:
  - dotNet
  - WCF
  - XML
---
… or create a custom binding from a build-in binding.  
… or create an administrative XML-based configuration from an administrative programmatic configuration.

Below codes does all that:

<pre class="brush: csharp; title: ; notranslate" title="">// Specify the source binding
// - Programmatic binding
// - Administrative XML-based binding
// - Convert to custom binding

/* Programmatic binding */
var binding = new BasicHttpBinding();
binding.TransferMode = TransferMode.Streamed;
binding.MaxReceivedMessageSize = 10000;

/* Administrative XML-based binding */
// var binding = new BasicHttpBinding("basicHttp");

/* Convert to custom binding */
// var wsBinding = new WSHttpBinding("wsHttp");
// var binding = new CustomBinding(wsBinding);

string outputConfigFile = "out.config";

Configuration machineConfig = ConfigurationManager.OpenMachineConfiguration();

var fileMap = new ExeConfigurationFileMap();
fileMap.ExeConfigFilename = outputConfigFile;
fileMap.MachineConfigFilename = machineConfig.FilePath;

Configuration config = ConfigurationManager.OpenMappedExeConfiguration(fileMap, ConfigurationUserLevel.None);
config.NamespaceDeclared = true;

var scg = new ServiceContractGenerator(config);

string sectionName, configName;
scg.GenerateBinding(binding, out sectionName, out configName);
config.Save();
</pre>

The programmatic source binding will create a configuration file with all default values for the BasicHttpBinding except for TransferMode and MaxReceivedMessageSize attributes like so:

<pre class="brush: xml; title: ; notranslate" title="">&lt;basichttpbinding&gt;
    &lt;binding name="BasicHttpBinding" 
             closeTimeout="00:01:00" 
             openTimeout="00:01:00" 
             receiveTimeout="00:10:00" 
             sendTimeout="00:01:00" 
             allowCookies="false"
             bypassProxyOnLocal="false" 
             hostNameComparisonMode="StrongWildcard"
             maxBufferSize="65536" 
             maxBufferPoolSize="524288" 
             maxReceivedMessageSize="10000" 
             messageEncoding="Text" 
             textEncoding="utf-8" 
             transferMode="Streamed"
             useDefaultWebProxy="true"&gt;
        &lt;readerquotas maxDepth="32" 
                      maxStringContentLength="8192" 
                      maxArrayLength="16384" 
                      maxBytesPerRead="4096" 
                      maxNameTableCharCount="16384" /&gt;
        &lt;security mode="None"&gt;
            &lt;transport clientCredentialType="None" 
                       proxyCredentialType="None" 
                       realm="" /&gt;
            &lt;message clientCredentialType="UserName" 
                     algorithmSuite="Default" /&gt;
        &lt;/security&gt;
    &lt;/binding&gt;
&lt;/basichttpbinding&gt;
</pre>

I found this [tip by Brian McNamara on the MSDN WCF forum](http://social.msdn.microsoft.com/Forums/en-US/wcf/thread/fcad2118-d7bc-4d23-84b2-1ac168afa1c8 "Generate WCF config binding").