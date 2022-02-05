---
title: How to view default values for a WCF binding
permalink: /blog/2009/04/23/view-default-values-for-a-wcf-binding/
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
{{site.data.messages.dotnetframeworkwarning}}
{: .notice--warning}

… or create a custom binding from a build-in binding.
… or create an administrative XML-based configuration from an administrative programmatic configuration.

Below codes does all that:

```csharp
// Specify the source binding
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
```

The programmatic source binding will create a configuration file with all default values for the `BasicHttpBinding` except for `TransferMode` and `MaxReceivedMessageSize` attributes like so:

```xml
<basichttpbinding>
    <binding name="BasicHttpBinding"
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
             useDefaultWebProxy="true">
        <readerquotas maxDepth="32"
                      maxStringContentLength="8192"
                      maxArrayLength="16384"
                      maxBytesPerRead="4096"
                      maxNameTableCharCount="16384" />
        <security mode="None">
            <transport clientCredentialType="None"
                       proxyCredentialType="None"
                       realm="" />
            <message clientCredentialType="UserName"
                     algorithmSuite="Default" />
        </security>
    </binding>
</basichttpbinding>
```

I found this [tip by Brian McNamara on the MSDN WCF forum](http://social.msdn.microsoft.com/Forums/en-US/wcf/thread/fcad2118-d7bc-4d23-84b2-1ac168afa1c8 "Generate WCF config binding").
