# Hush ReadMe
## About
Hush is an addon for KVIrc and requires at least v4.9.2 or higher from v0.2.6 onwards.
Hush aims to create a local search system for XDCC channels along with automation for a number of aspects related to your media content. 
It: 
 * removes the need to publicly search for content
 * removes the need to use subscription systems that reside on other computers
 * provides a degree of automation to continue content acquisition
 * supports watching multiple IRC networks and channels

## Latest Update

### 20260428
Auto Searches Import Workaround
IMDB was 'blocking' me from accessing their Calendar page, but only when I accessed it using KVIrc. I believe they are blocking based on the HTTP User Agent since KVIrc identifies as KVIrc and not a normal web browser. As my script was only accessing the page once per day I guess they picked up it was a bot from the User Agent or the precise timing of the request each day.

To change the User Agent string I would have to build KVIrc from source and then you have to use a custom version of KVIrc for this feature to work, so instead I added a Local File option in the Searches Import for IMDB Calendar page. This means the automatic acquisition of Movies will no longer work, but you can periodically fetch and save the IMDB Calendar page to your local drive using your browser and point Hush at it via this new option in Searches Import and it will grab the Movie names for you and import them to Searches.

### 20250124
Bug hunting - my install was not updating the permanent record array. Things I found were: 

	* There was no Hush folder in the Users\username\AppData\Roaming\KVIrc4\config location (running on Windows)
	* The file.mkdir command is used to create this folder in the install.kvs file, so either this file was not ran, or there is a permission problem preventing the install.kvs from creating that folder.
	* If the config/Hush folder is created manually, then hush/kvirc is able to write to the folder, even if kvirc is ran without admin permissions

I am continuing to see why the permanent record array doesn't save and update.
I updated the Hush code to save its config files in the file.homedir locations (the user's home directory) instead of the file.localdir location (the AppData\Roaming\KVIrc4 folder) and it seems to have fixed the permanent record generation.


### 20240110
Added new classes: hush::sport and hush::sportobject
Added new widgets to: 
    
        * Control Sport settings
    


## ToDo

    * Sport
    
        * Frontend: Build UI widgets to represent each "Sport Trail" the user creates
        * Backend: Decide how to build search strings to detect desired sport content
        * Backend: Decide how/when to perform a Sport search
    

## bug notes
These bugs are pretty old now and for the most part don't seem to affect the operation of Hush, so they are very low priority.
### aSTi bug
there is a bug that producesd the following error:

[Nov08-23:07:12] [KVS] Runtime error: Array index didn't evaluate to an integer
[Nov08-23:07:12] [KVS] In script context "OnQueryNotice::hushErrorHandler", line 160, near character 17
[Nov08-23:07:12] [KVS] Code listing:
[Nov08-23:07:12] [KVS] 158 ...
[Nov08-23:07:12] [KVS] 159 #debug -c "$k(8)--> found active searchTerm %G_hush->%activeSearchTerm[%aSTi] at index [%aSTi]. removing it from the active searchTerm array <--$k()"
[Nov08-23:07:12] [KVS] 160 debug -c $k(15)[FATAL ERROR]$k()\n$b()asti = %aSTi$b()\n typeOf asti = $typeOf(%aSTi)\nfnArrayBinarySearch was called using:\n	st = %st\n	activeSearchTermArray = %G_hush->%activeSearchTerms\n	--- ($k(8)This value has caused onQueryNotice::hushErrorHandler to fail$k(), so check it
[Nov08-23:07:12] [KVS] 161 debug -c "-------------"
[Nov08-23:07:12] [KVS] 162 ...

%aSTi is somehow getting a null value (it should always be an int)

when this occurred on Nov 08, the debug logs showed that Hush had attempted to auto download a file 3 times although it successfully downloaded that file twice already. It should never duplicate downloads, so I need to determine the conditions that allow this to come about.

I am not sure if the multi-download bug relates to the aSTi bug?

---

### The Multi Download Bug

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

### Media Libary Scan Bug
On KVIrc startup, the media library scan function will run. It will successfully scan the configured media content locations and find Movies and TV titles, placing their values into the appropriate arrays and files.

If you open the Media Library window after startup and click the "Scan Media Library" button, the media scan will run, but it will find 0 or 1 title only.

This bug needs investigation.
