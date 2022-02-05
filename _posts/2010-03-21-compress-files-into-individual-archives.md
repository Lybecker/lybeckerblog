---
title: Compress files into individual archives
permalink: /blog/2010/03/21/compress-files-into-individual-archives/
dsq_thread_id:
  - "4209585180"
  - "4209585180"
categories:
  - Useful tools
tags:
  - 7-Zip
  - Batch
  - DOS
---
I needed to compress a lot of files into individual zip archives – I did not want to do it manually 🙂

Add the following to a bat file and every file with the extension txt will be compressed into a Zip archive with [7-Zip file archiver](http://www.7-zip.org/ "7-Zip's website"):

```cmd
@echo off
For %%f in (*.txt) do 7z.exe a -tzip %%f.zip %%f
```

E.g. _a.txt_ will be compressed to the archive _a.txt.zip_

This was not exactly what I needed, as the dual extension caused problems in later processing. In needed to remove the extension preceding the zip extension – therefore:

```cmd
@echo off
For %%f in (*.txt) do 7z.exe a -tzip %%~nf.zip %%f
```

E.g. _a.txt_ will be compressed to the archive _a.zip_

That’s it 🙂
