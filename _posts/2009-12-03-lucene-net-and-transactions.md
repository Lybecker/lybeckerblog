---

title: Lucene.Net and Transactions
date: 2009-12-03T21:00:46+01:00


guid: http://www.lybecker.com/blog/?p=400
permalink: /blog/2009/12/03/lucene-net-and-transactions/
dsq_thread_id:
  - "3456317833"
  - "3456317833"
categories:
  - .Net
  - Full Text Search
  - Lucene
tags:
  - dotNet
  - Lucene.Net
  - Transactions
---
<div class="articleInlineBlocks alignright">
  <img class="articleInlineImage" src="/blog/wp-content/themes/default/images/inlineblock_top.gif" alt="" height="5" /></p>

  <p class="articleInlineBlockContent" style="text-align: center;">
    <p class="articleInlineBlockContent">
      <a href="http://www.lybecker.com/blog/wp-content/uploads/LuceneLogo.gif"><img loading="lazy" class="aligncenter size-full wp-image-492" title="Lucene Search Engine Logo" src="http://www.lybecker.com/blog/wp-content/uploads/LuceneLogo.gif" alt="Lucene Search Engine Logo" width="160" height="25" /></a>
    </p>

    <p class="articleInlineBlockContent">
      Lucene.Net is an open source full text search engine library (a port from Java). It is stable and works like a charm &#8211; I’ve been using Lucene.Net for a couple of years now and implement a handful of solutions. Lucene is awesome.
    </p>

    <p class="articleInlineBlockContent">
      If you want to try working with Lucene.Net, then the <a title="Lucene.Net at dimecast.net" href="http://dimecasts.net/Casts/ByTag/Lucene">DimeCast.Net</a> crew has recently made two short webcasts introducing Lucene.Net.
    </p>

    <p style="margin: 0px">
      <img class="articleInlineImage" style="margin-top: 10px;" src="/blog/wp-content/themes/default/images/inlineblock_bottom.gif" alt="" height="15" />
    </p></div>

    <p>
      .Net 2.0 made it simple to use transactions with the System.Transactions namespace. Two of the great features are automatic elevation to distributed transactions (and utilize the Distributed Transaction Coordinator) and the other is the simplicity of creating your own transactional resource managers.
    </p>

    <p>
      The .Net Framework defines a resource manager as a resource that can automatically enlist in a transaction managed by System.Transactions – which means that any object that implements any of the following interfaces can enlist in a transaction:
    </p>

    <ul>
      <li>
        IEnlistmentNotification for the <a title="2PC protocol at Wikipedia" href="http://en.wikipedia.org/wiki/Two-phase_commit_protocol">two-phase-commit protocol</a>
      </li>
      <li>
        IPromotableSinglePhaseNotification for the single-phase-commit protocol (non-distributed transactions)
      </li>
    </ul>

    <p>
      To implement a resource manager for the Lucene.Net IndexWriter, and therefore make it transactional, all you have to do is the following:
    </p>

    ```csharp

public class TransactionalIndexWriter : IndexWriter, IEnlistmentNotification
{
    #region ctor
    public TransactionalIndexWriter(Directory d, Analyzer a, bool create, MaxFieldLength mfl)
        : base(d, a, create, mfl)
    {
        EnlistTransaction();
    }
    /* More constructors */
    #endregion

    public void EnlistTransaction()
    {
        // Enlist in transaction if ambient transaction exists
        Transaction tx = Transaction.Current;
        if (tx != null)
            tx.EnlistVolatile(this, EnlistmentOptions.None);
    }

    #region IEnlistmentNotification Members
    public void Commit(Enlistment enlistment)
    {
        base.Commit();
        enlistment.Done();
    }

    public void InDoubt(Enlistment enlistment)
    {
        // Do nothing.
        enlistment.Done();
    }

    public void Prepare(PreparingEnlistment preparingEnlistment)
    {
        base.PrepareCommit();
        preparingEnlistment.Prepared();
    }

    public void Rollback(Enlistment enlistment)
    {
        base.Rollback();
        enlistment.Done();
    }
    #endregion
}
</pre>

    <p>
      You can use it like so:
    </p>

    ```csharp

IndexWriter indexWriter = null;
TransactionScope tx = null;

try
{
    tx = new TransactionScope();
    indexWriter = new TransactionalIndexWriter(...);

    // Perform transactional work
    indexWriter.AddDocument(new Document());
    indexWriter.AddDocument(new Document());
    indexWriter.AddDocument(new Document());

    // Connect to Database, MSMQ etc. to elevate to a distributed transaction

    // Commit transaction
    tx.Complete();
}
finally
{
    if (tx != null)
        tx.Dispose();

    if (indexWriter != null)
        indexWriter.Close();
}
</pre>

    <p>
      Fairly simply uh? Just remember to instantiate the TransactionalIndexWriter or call the public method EnlistTransaction within the scope of an ambient transaction.<br /> You might consider implementing IDisposable for TransactionalIndexWriter so you can take advantage of the using statement.
    </p>

    <p>
      I will leave it to the reader to implement a TransactionalIndexReader.
    </p>

    <div id="_mcePaste" style="overflow: hidden; position: absolute; left: -10000px; top: 0px; width: 1px; height: 1px;">
      <!--[if gte mso 9]><xml> <w :WordDocument> </w><w :View>Normal</w> <w :Zoom>0</w> <w :TrackMoves /> <w :TrackFormatting /> <w :HyphenationZone>21</w> <w :PunctuationKerning /> <w :ValidateAgainstSchemas /> <w :SaveIfXMLInvalid>false</w> <w :IgnoreMixedContent>false</w> <w :AlwaysShowPlaceholderText>false</w> <w :DoNotPromoteQF /> <w :LidThemeOther>DA</w> <w :LidThemeAsian>ZH-CN</w> <w :LidThemeComplexScript>X-NONE</w> <w :Compatibility> <w :BreakWrappedTables /> <w :SnapToGridInCell /> <w :WrapTextWithPunct /> <w :UseAsianBreakRules /> <w :DontGrowAutofit /> <w :SplitPgBreakAndParaMark /> <w :DontVertAlignCellWithSp /> <w :DontBreakConstrainedForcedTables /> <w :DontVertAlignInTxbx /> <w :Word11KerningPairs /> <w :CachedColBalance /> <w :UseFELayout /> </w> <m :mathPr> <m :mathFont m:val="Cambria Math" /> <m :brkBin m:val="before" /> <m :brkBinSub m:val="&#45;-" /> <m :smallFrac m:val="off" /> <m :dispDef /> <m :lMargin m:val="0" /> <m :rMargin m:val="0" /> <m :defJc m:val="centerGroup" /> <m :wrapIndent m:val="1440" /> <m :intLim m:val="subSup" /> <m :naryLim m:val="undOvr" /> </m> </xml>< ![endif]-->

      <!--[if gte mso 9]><xml> <w :LatentStyles DefLockedState="false" DefUnhideWhenUsed="true"   DefSemiHidden="true" DefQFormat="false" DefPriority="99"   LatentStyleCount="267"> <w :LsdException Locked="false" Priority="0" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Normal" /> <w :LsdException Locked="false" Priority="9" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="heading 1" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 2" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 3" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 4" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 5" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 6" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 7" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 8" /> <w :LsdException Locked="false" Priority="9" QFormat="true" Name="heading 9" /> <w :LsdException Locked="false" Priority="39" Name="toc 1" /> <w :LsdException Locked="false" Priority="39" Name="toc 2" /> <w :LsdException Locked="false" Priority="39" Name="toc 3" /> <w :LsdException Locked="false" Priority="39" Name="toc 4" /> <w :LsdException Locked="false" Priority="39" Name="toc 5" /> <w :LsdException Locked="false" Priority="39" Name="toc 6" /> <w :LsdException Locked="false" Priority="39" Name="toc 7" /> <w :LsdException Locked="false" Priority="39" Name="toc 8" /> <w :LsdException Locked="false" Priority="39" Name="toc 9" /> <w :LsdException Locked="false" Priority="35" QFormat="true" Name="caption" /> <w :LsdException Locked="false" Priority="10" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Title" /> <w :LsdException Locked="false" Priority="1" Name="Default Paragraph Font" /> <w :LsdException Locked="false" Priority="11" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Subtitle" /> <w :LsdException Locked="false" Priority="22" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Strong" /> <w :LsdException Locked="false" Priority="20" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Emphasis" /> <w :LsdException Locked="false" Priority="59" SemiHidden="false"    UnhideWhenUsed="false" Name="Table Grid" /> <w :LsdException Locked="false" UnhideWhenUsed="false" Name="Placeholder Text" /> <w :LsdException Locked="false" Priority="1" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="No Spacing" /> <w :LsdException Locked="false" Priority="60" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Shading" /> <w :LsdException Locked="false" Priority="61" SemiHidden="false"    UnhideWhenUsed="false" Name="Light List" /> <w :LsdException Locked="false" Priority="62" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Grid" /> <w :LsdException Locked="false" Priority="63" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 1" /> <w :LsdException Locked="false" Priority="64" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 2" /> <w :LsdException Locked="false" Priority="65" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 1" /> <w :LsdException Locked="false" Priority="66" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 2" /> <w :LsdException Locked="false" Priority="67" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 1" /> <w :LsdException Locked="false" Priority="68" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 2" /> <w :LsdException Locked="false" Priority="69" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 3" /> <w :LsdException Locked="false" Priority="70" SemiHidden="false"    UnhideWhenUsed="false" Name="Dark List" /> <w :LsdException Locked="false" Priority="71" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Shading" /> <w :LsdException Locked="false" Priority="72" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful List" /> <w :LsdException Locked="false" Priority="73" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Grid" /> <w :LsdException Locked="false" Priority="60" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Shading Accent 1" /> <w :LsdException Locked="false" Priority="61" SemiHidden="false"    UnhideWhenUsed="false" Name="Light List Accent 1" /> <w :LsdException Locked="false" Priority="62" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Grid Accent 1" /> <w :LsdException Locked="false" Priority="63" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 1 Accent 1" /> <w :LsdException Locked="false" Priority="64" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 2 Accent 1" /> <w :LsdException Locked="false" Priority="65" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 1 Accent 1" /> <w :LsdException Locked="false" UnhideWhenUsed="false" Name="Revision" /> <w :LsdException Locked="false" Priority="34" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="List Paragraph" /> <w :LsdException Locked="false" Priority="29" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Quote" /> <w :LsdException Locked="false" Priority="30" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Intense Quote" /> <w :LsdException Locked="false" Priority="66" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 2 Accent 1" /> <w :LsdException Locked="false" Priority="67" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 1 Accent 1" /> <w :LsdException Locked="false" Priority="68" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 2 Accent 1" /> <w :LsdException Locked="false" Priority="69" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 3 Accent 1" /> <w :LsdException Locked="false" Priority="70" SemiHidden="false"    UnhideWhenUsed="false" Name="Dark List Accent 1" /> <w :LsdException Locked="false" Priority="71" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Shading Accent 1" /> <w :LsdException Locked="false" Priority="72" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful List Accent 1" /> <w :LsdException Locked="false" Priority="73" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Grid Accent 1" /> <w :LsdException Locked="false" Priority="60" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Shading Accent 2" /> <w :LsdException Locked="false" Priority="61" SemiHidden="false"    UnhideWhenUsed="false" Name="Light List Accent 2" /> <w :LsdException Locked="false" Priority="62" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Grid Accent 2" /> <w :LsdException Locked="false" Priority="63" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 1 Accent 2" /> <w :LsdException Locked="false" Priority="64" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 2 Accent 2" /> <w :LsdException Locked="false" Priority="65" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 1 Accent 2" /> <w :LsdException Locked="false" Priority="66" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 2 Accent 2" /> <w :LsdException Locked="false" Priority="67" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 1 Accent 2" /> <w :LsdException Locked="false" Priority="68" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 2 Accent 2" /> <w :LsdException Locked="false" Priority="69" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 3 Accent 2" /> <w :LsdException Locked="false" Priority="70" SemiHidden="false"    UnhideWhenUsed="false" Name="Dark List Accent 2" /> <w :LsdException Locked="false" Priority="71" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Shading Accent 2" /> <w :LsdException Locked="false" Priority="72" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful List Accent 2" /> <w :LsdException Locked="false" Priority="73" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Grid Accent 2" /> <w :LsdException Locked="false" Priority="60" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Shading Accent 3" /> <w :LsdException Locked="false" Priority="61" SemiHidden="false"    UnhideWhenUsed="false" Name="Light List Accent 3" /> <w :LsdException Locked="false" Priority="62" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Grid Accent 3" /> <w :LsdException Locked="false" Priority="63" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 1 Accent 3" /> <w :LsdException Locked="false" Priority="64" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 2 Accent 3" /> <w :LsdException Locked="false" Priority="65" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 1 Accent 3" /> <w :LsdException Locked="false" Priority="66" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 2 Accent 3" /> <w :LsdException Locked="false" Priority="67" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 1 Accent 3" /> <w :LsdException Locked="false" Priority="68" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 2 Accent 3" /> <w :LsdException Locked="false" Priority="69" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 3 Accent 3" /> <w :LsdException Locked="false" Priority="70" SemiHidden="false"    UnhideWhenUsed="false" Name="Dark List Accent 3" /> <w :LsdException Locked="false" Priority="71" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Shading Accent 3" /> <w :LsdException Locked="false" Priority="72" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful List Accent 3" /> <w :LsdException Locked="false" Priority="73" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Grid Accent 3" /> <w :LsdException Locked="false" Priority="60" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Shading Accent 4" /> <w :LsdException Locked="false" Priority="61" SemiHidden="false"    UnhideWhenUsed="false" Name="Light List Accent 4" /> <w :LsdException Locked="false" Priority="62" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Grid Accent 4" /> <w :LsdException Locked="false" Priority="63" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 1 Accent 4" /> <w :LsdException Locked="false" Priority="64" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 2 Accent 4" /> <w :LsdException Locked="false" Priority="65" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 1 Accent 4" /> <w :LsdException Locked="false" Priority="66" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 2 Accent 4" /> <w :LsdException Locked="false" Priority="67" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 1 Accent 4" /> <w :LsdException Locked="false" Priority="68" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 2 Accent 4" /> <w :LsdException Locked="false" Priority="69" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 3 Accent 4" /> <w :LsdException Locked="false" Priority="70" SemiHidden="false"    UnhideWhenUsed="false" Name="Dark List Accent 4" /> <w :LsdException Locked="false" Priority="71" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Shading Accent 4" /> <w :LsdException Locked="false" Priority="72" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful List Accent 4" /> <w :LsdException Locked="false" Priority="73" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Grid Accent 4" /> <w :LsdException Locked="false" Priority="60" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Shading Accent 5" /> <w :LsdException Locked="false" Priority="61" SemiHidden="false"    UnhideWhenUsed="false" Name="Light List Accent 5" /> <w :LsdException Locked="false" Priority="62" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Grid Accent 5" /> <w :LsdException Locked="false" Priority="63" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 1 Accent 5" /> <w :LsdException Locked="false" Priority="64" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 2 Accent 5" /> <w :LsdException Locked="false" Priority="65" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 1 Accent 5" /> <w :LsdException Locked="false" Priority="66" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 2 Accent 5" /> <w :LsdException Locked="false" Priority="67" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 1 Accent 5" /> <w :LsdException Locked="false" Priority="68" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 2 Accent 5" /> <w :LsdException Locked="false" Priority="69" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 3 Accent 5" /> <w :LsdException Locked="false" Priority="70" SemiHidden="false"    UnhideWhenUsed="false" Name="Dark List Accent 5" /> <w :LsdException Locked="false" Priority="71" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Shading Accent 5" /> <w :LsdException Locked="false" Priority="72" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful List Accent 5" /> <w :LsdException Locked="false" Priority="73" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Grid Accent 5" /> <w :LsdException Locked="false" Priority="60" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Shading Accent 6" /> <w :LsdException Locked="false" Priority="61" SemiHidden="false"    UnhideWhenUsed="false" Name="Light List Accent 6" /> <w :LsdException Locked="false" Priority="62" SemiHidden="false"    UnhideWhenUsed="false" Name="Light Grid Accent 6" /> <w :LsdException Locked="false" Priority="63" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 1 Accent 6" /> <w :LsdException Locked="false" Priority="64" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Shading 2 Accent 6" /> <w :LsdException Locked="false" Priority="65" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 1 Accent 6" /> <w :LsdException Locked="false" Priority="66" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium List 2 Accent 6" /> <w :LsdException Locked="false" Priority="67" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 1 Accent 6" /> <w :LsdException Locked="false" Priority="68" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 2 Accent 6" /> <w :LsdException Locked="false" Priority="69" SemiHidden="false"    UnhideWhenUsed="false" Name="Medium Grid 3 Accent 6" /> <w :LsdException Locked="false" Priority="70" SemiHidden="false"    UnhideWhenUsed="false" Name="Dark List Accent 6" /> <w :LsdException Locked="false" Priority="71" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Shading Accent 6" /> <w :LsdException Locked="false" Priority="72" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful List Accent 6" /> <w :LsdException Locked="false" Priority="73" SemiHidden="false"    UnhideWhenUsed="false" Name="Colorful Grid Accent 6" /> <w :LsdException Locked="false" Priority="19" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Subtle Emphasis" /> <w :LsdException Locked="false" Priority="21" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Intense Emphasis" /> <w :LsdException Locked="false" Priority="31" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Subtle Reference" /> <w :LsdException Locked="false" Priority="32" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Intense Reference" /> <w :LsdException Locked="false" Priority="33" SemiHidden="false"    UnhideWhenUsed="false" QFormat="true" Name="Book Title" /> <w :LsdException Locked="false" Priority="37" Name="Bibliography" /> <w :LsdException Locked="false" Priority="39" QFormat="true" Name="TOC Heading" /> </w> </xml>< ![endif]-->

      <!--  /* Font Definitions */  @font-face 	{font-family:SimSun; 	panose-1:2 1 6 0 3 1 1 1 1 1; 	mso-font-alt:宋体; 	mso-font-charset:134; 	mso-generic-font-family:auto; 	mso-font-pitch:variable; 	mso-font-signature:3 680460288 22 0 262145 0;} @font-face 	{font-family:"Cambria Math"; 	panose-1:2 4 5 3 5 4 6 3 2 4; 	mso-font-charset:1; 	mso-generic-font-family:roman; 	mso-font-format:other; 	mso-font-pitch:variable; 	mso-font-signature:0 0 0 0 0 0;} @font-face 	{font-family:Calibri; 	panose-1:2 15 5 2 2 2 4 3 2 4; 	mso-font-charset:0; 	mso-generic-font-family:swiss; 	mso-font-pitch:variable; 	mso-font-signature:-520092929 1073786111 9 0 415 0;} @font-face 	{font-family:"@SimSun"; 	panose-1:2 1 6 0 3 1 1 1 1 1; 	mso-font-charset:134; 	mso-generic-font-family:auto; 	mso-font-pitch:variable; 	mso-font-signature:3 680460288 22 0 262145 0;}  /* Style Definitions */  p.MsoNormal, li.MsoNormal, div.MsoNormal 	{mso-style-unhide:no; 	mso-style-qformat:yes; 	mso-style-parent:""; 	margin-top:0cm; 	margin-right:0cm; 	margin-bottom:10.0pt; 	margin-left:0cm; 	line-height:115%; 	mso-pagination:widow-orphan; 	font-size:11.0pt; 	font-family:"Calibri","sans-serif"; 	mso-ascii-font-family:Calibri; 	mso-ascii-theme-font:minor-latin; 	mso-fareast-font-family:SimSun; 	mso-fareast-theme-font:minor-fareast; 	mso-hansi-font-family:Calibri; 	mso-hansi-theme-font:minor-latin; 	mso-bidi-font-family:"Times New Roman"; 	mso-bidi-theme-font:minor-bidi;} .MsoChpDefault 	{mso-style-type:export-only; 	mso-default-props:yes; 	mso-ascii-font-family:Calibri; 	mso-ascii-theme-font:minor-latin; 	mso-fareast-font-family:SimSun; 	mso-fareast-theme-font:minor-fareast; 	mso-hansi-font-family:Calibri; 	mso-hansi-theme-font:minor-latin; 	mso-bidi-font-family:"Times New Roman"; 	mso-bidi-theme-font:minor-bidi;} .MsoPapDefault 	{mso-style-type:export-only; 	margin-bottom:10.0pt; 	line-height:115%;} @page Section1 	{size:612.0pt 792.0pt; 	margin:3.0cm 2.0cm 3.0cm 2.0cm; 	mso-header-margin:35.4pt; 	mso-footer-margin:35.4pt; 	mso-paper-source:0;} div.Section1 	{page:Section1;} -->

      <!--[if gte mso 10]> <mce :style>< !   /* Style Definitions */  table.MsoNormalTable 	{mso-style-name:"Table Normal"; 	mso-tstyle-rowband-size:0; 	mso-tstyle-colband-size:0; 	mso-style-noshow:yes; 	mso-style-priority:99; 	mso-style-qformat:yes; 	mso-style-parent:""; 	mso-padding-alt:0cm 5.4pt 0cm 5.4pt; 	mso-para-margin-top:0cm; 	mso-para-margin-right:0cm; 	mso-para-margin-bottom:10.0pt; 	mso-para-margin-left:0cm; 	line-height:115%; 	mso-pagination:widow-orphan; 	font-size:11.0pt; 	font-family:"Calibri","sans-serif"; 	mso-ascii-font-family:Calibri; 	mso-ascii-theme-font:minor-latin; 	mso-hansi-font-family:Calibri; 	mso-hansi-theme-font:minor-latin; 	mso-bidi-font-family:"Times New Roman"; 	mso-bidi-theme-font:minor-bidi;} -->

      <!--[endif]--></p>

      <p class="MsoNormal">
        <span lang="EN-US">Lucene.Net is an open source full text search engine library (a port from Java). It is stable and works like a charm &#8211; I’ve been using Lucene.Net for a couple of years now and implement a handful of solutions. Lucene is awesome.</span>
      </p>

      <p class="MsoNormal">
        <span lang="EN-US">If you want to try working with Lucene.Net, then the DimeCast.Net crew has recently made two 10 short webcast introducing Lucene.Net (http://dimecasts.net/Casts/ByTag/Lucene).</span>
      </p>

      <p>
        </mce></div>
