<h1>Hush ReadMe</h1>
hush development

<h2>ToDo</h2>
<ul>
    <li>Transfer Management</li>
    <ul>
        <li>Build a system to track pending requested downloads and decide when a transfer request has had long enough and cancel it.</li>
        <li>The request tracking needs to ensure it handles rogue bots that take ages to send files. If Hush decides to move on from a requested download, it needs to be able to ignore if a bot decides to send the file later on.</li>
    </ul>
</ul>
<h2>bug notes</h2>

<h3>aSTi bug</h3>
there is a bug that producesd the following error: 

[Nov08-23:07:12] [KVS] Runtime error: Array index didn't evaluate to an integer
[Nov08-23:07:12] [KVS]   In script context "OnQueryNotice::hushErrorHandler", line 160, near character 17
[Nov08-23:07:12] [KVS] Code listing:
[Nov08-23:07:12] [KVS]   158 ...
[Nov08-23:07:12] [KVS]   159 	#debug -c "$k(8)--> found active searchTerm %G_hush->%activeSearchTerm[%aSTi] at index [%aSTi]. removing it from the active searchTerm array <--$k()"
[Nov08-23:07:12] [KVS]   160 	debug -c $k(15)[FATAL ERROR]$k()\n$b()asti = %aSTi$b()\n typeOf asti = $typeOf(%aSTi)\nfnArrayBinarySearch was called using:\n	st = %st\n	activeSearchTermArray = %G_hush->%activeSearchTerms\n	--- ($k(8)This value has caused onQueryNotice::hushErrorHandler to fail$k(), so check it
[Nov08-23:07:12] [KVS]   161 	debug -c "-------------"
[Nov08-23:07:12] [KVS]   162 ...

%aSTi is somehow getting a null value (it should always be an int)

when this occurred on Nov 08, the debug logs showed that Hush had attempted to auto download a file 3 times although it successfully downloaded that file twice already. It should never duplicate downloads, so I need to determine the conditions that allow this to come about.

I am not sure if the multi-download bug relates to the aSTi bug?

----
<h3>The Multi Download Bug</h3>

Debug log showed that: 

1. Passive search found 3 results (expected)
2. Primal.mkv was req from Waveform and downloaded successfully. (expected)
3. Passive search timer is triggered again at 2 hour interval, 0 results (expected)
4. searchesImport timer is triggered which re-adds the Primal search term to the Searches list [NOT EXPECTED]
    Expected behaviour is for Hush to filter the Primal search term from the list because we have already downloaded the title. 
    Proposed Remedy: 
        Find code section that deals with insertion of search terms into Searches list and ensure that a comparison against existing titles is being done.
        Check if the existing title list (or array) is being updated immediately when a new title is transferred successfully.
        Trace code logic after a transfer completes to decide where to update the existing list of titles.
5. Passive search found 3 results (expected)
6. hush::main::checkDownloadQueue finds an xdccobject with Status "Queued" under the PRimal search term (but we already have a completed download for this title!) [NOT EXPECTED]
    Expected behaviour is for checkDownloadQueue to see that we have a completed transfer for this search term already and to skip downloading.
7. Download of 2nd Primal title begins and completes. (expected)
8. Passive search found 1 result (expected)
9. hush::main::checkDownloadQueue finds an xdccobject with Status "Queued" under the PRimal search term (but we already have a completed download for this title!) [NOT EXPECTED]
    Expected behaviour is for checkDownloadQueue to see that we have a completed transfer for this search term already and to skip downloading.
10. 3rd Primal title is requested from 84659 (expected)
11. At 23:07:12 a query notice triggers the hushErrorHandler. The query notice sent by the bot was: "Sorry, this command is unsupported" which is a case handled by hushErrorHandler
12. aSTi is returned value of hush::common::fnArrayBinarySearch
13. appears as though in this instance only, the error generated was dues to the $k()[FATAL ERROR]$k() <- square brackets were mistaken for an array index reference. This was not truly the aSTi bug.

<h3>Media Libary Scan Bug</h3>
On KVIrc startup, the media library scan function will run. It will successfully scan the configured media content locations and find Movies and TV titles, placing their values into the appropriate arrays and files.

If you open the Media Library window after startup and click the "Scan Media Library" button, the media scan will run, but it will find 0 or 1 title only.

This bug needs investigation.
