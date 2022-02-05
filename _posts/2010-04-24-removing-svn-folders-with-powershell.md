---

title: Removing SVN folders with PowerShell
date: 2010-04-24T15:42:57+01:00


guid: http://www.lybecker.com/blog/?p=595
permalink: /blog/2010/04/24/removing-svn-folders-with-powershell/
dsq_thread_id:
  - "3460747946"
  - "3460747946"
categories:
  - .Net
  - Everyday coding
  - PowerShell
  - Useful tools
tags:
  - .Net
  - Batch
  - PowerShell
  - SVN
---
[<img loading="lazy" class="alignright size-thumbnail wp-image-613" title="PowerShell Logo" src="http://www.lybecker.com/blog/wp-content/uploads/PowerShellLogo-150x150.jpg" alt="" width="150" height="150" />](http://www.lybecker.com/blog/wp-content/uploads/PowerShellLogo.jpg)
I need to remove.svn folders from an existing Visual Studio Solution a customer email me, so I could commit it to another SVN repository.

If I had access to the original SVN repository, I could have used the export function, as it does not include the .svn folders – but no, it should not be that easy.

What the heck, I have been putting it off way too long to start working with PowerShell. It should be a familiar environment as it is object-oriented with a C# like syntax with full access to the .Net Framework Base Class Libraries (BCL).

Here it goes – my first PowerShell script…

<pre class="brush: powershell; title: ; notranslate" title="">function RemoveSvnFolders([string]$path)
{
    Write-Host "Removing .svn folders in path $path recursive"

	Get-ChildItem $path -Include ".svn" -Force -Recurse |
		Where {$_.psIsContainer -eq $true} |
		Foreach ($_)
		{
			Remove-Item $_.Fullname -Force -Recurse
		}
}
</pre>

The [Write-Host Cmdlet](http://technet.microsoft.com/en-us/library/ee177031.aspx "Write-Host Cmdlet article on TechNet") just writes the content to console window.

If you are like me, a PowerShell novice &#8211; start with the [Getting Started with Windows PowerShell](http://technet.microsoft.com/en-us/library/ee177003.aspx "Getting Started with Windows PowerShell article on TechNet") article and use the free tool [PowerGUI](http://www.quest.com/powershell/ "Free PowerShell IDE for PowerShell") from Quest Software. It’s PowerShell IDE with an integrated syntax highlighter editor and debugger.

In line 5 the [Get-ChildItem Cmdlet](http://technet.microsoft.com/en-us/library/dd347686.aspx "Get-ChildItem Cmdlet article on TechNet") iterates the path recursively and filtering the result to include only “.svn” files and folders. The force parameter allows the cmdlet to get items that cannot otherwise be accessed by the user, such as hidden or system files. Get-ChildItem Cmdlet can also iterate the registry.

Afterwards the result from Get-ChildItem Cmdlet is piped to the Where-Object Cmdlet (Where is an alias for Where-Object). The psIsContainer is a property on a folder. If it is equal to true pass it to the next pipe. I could have written the following instead:

<pre class="brush: powershell; title: ; notranslate" title="">Where {$_.mode -match "d"}
</pre>

Use the below statement to list all properties for the files and folders in the current folder:

<pre class="brush: powershell; title: ; notranslate" title="">Get-ChildItem | format-list -property *
</pre>

The foreach statement iterates every item and deletes the folder with the [Remove-Item Cmdlet](http://technet.microsoft.com/en-us/library/dd315401.aspx "Remove-Item Cmdlet article on TechNet").

Calling the method is as simple as:

<pre class="brush: powershell; title: ; notranslate" title="">RemoveSvnFolders("c:svnMy Solution")
</pre>

On TechNet there is a myriad of articles with the root [Windows PowerShell Core](http://technet.microsoft.com/en-us/library/bb978525.aspx "Windows PowerShell articles on TechNet") and more task oriented like [A Task-Based Guide to Windows PowerShell Cmdlets](http://technet.microsoft.com/en-us/scriptcenter/dd772285.aspx "How-to articles on TechNet") and [Piping and the Pipeline in Windows PowerShell](http://technet.microsoft.com/en-us/library/ee176927.aspx "Article on Pipelining on TechNet").

[Remove SVN folders PowerShell Script](http://www.lybecker.com/blog/wp-content/uploads/Remove-SVN-folders.zip).

Happy PowerShelling… 🙂
