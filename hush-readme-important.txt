What is Hush?

Hush is XDCC search and downloading software.

Where can you get Hush?

The official website for Hush is http://hush.getenjoyment.net/
The official eepsite for Hush is http://hush.i2p/
New releases are uploaded to the eepsite.

You can also download Hush from alexzulu's repo: http://kvirc.alexzulu.ru/
The repo may not always have the latest version.

Which version should you use?

The best release to use is the latest release (at time of writing v0.3.9) for Windows OS users. 
For Linux users, 0.3.5 onward should work but it is completely untested. v0.3.3 should work fine on Linux 
but it will use a lot of CPU if you have a lot of passive search terms.

--- Upgrading ---

IMPORTANT FOR USERS UPGRADING TO v0.4.0

v0.4.0 introduces the Transfer Priority system. This system requires a new table in the Hush database.
Hush will create the table it needs if you reset the Hush database in the Options.
To do this, click: Hush -> Options -> Searching -> Reset Entire Database

Note: you will lose all the current pack announce data and the 'haystack' data when you do this. 
Just wait a while and Hush will rebuild all the content in the database as bots announce in the channels.
The haystack data is built by Hush as it finds passive searched items.

IMPORTANT FOR USERS WITH v0.2.6 OR LOWER

Export your Hush settings or you will lose all your channel and search settings which can be a pretty big inconvenience!

Go to: Hush -> Options -> Export Settings and save to a file, it doesn't matter what you call it.
After following the instructions below, import your settings back into Hush.
Go to: Hush -> Options -> Import Settings and load from the file you saved to.

From v0.2.7 onwards you will be prompted if you want to preserve your config file on script uninstall or upgrade.

--- Installing ---

Extract the tar file (hush-x.x.x.tar)
In KVIrc go to Scripting -> Execute Script or press Ctrl+Shift+X
Browse to the extracted files and select install.kvs file, open it.

Hush should install and a new button should appear on the toolbar in KVIrc (it has a spiral on it).
You can also press Ctrl+Alt+H to open Hush in KVIrc.

See Help or hover mouse for tooltip help from there.