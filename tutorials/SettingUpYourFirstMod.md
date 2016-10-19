
# Setting Up Your First Mod

Since the release of Black Ops 3 Mod Tools [Beta], I have been searching and searching for a tutorial on how to setup a standalone "Mod" which can be executed on any stock map or custom map. My constant searching lead me to nothing... my guess, no one has wrote a tutorial yet. So here it goes (I'll try be as beginner friendly as possible and if you don't understand anything, simply leave a reply!).

Firstly, to start things off we must open up the Black Ops III Mod Tools Launcher. From here, we want to press "File -> New" (Ctrl + N) and create **our_first_mod** (see screenshots below)

![](http://image.prntscr.com/image/7b61d9fb3e56482ca9b33a15711b1cbb.png) ![](http://image.prntscr.com/image/d1910fb58ff4410c8282e60185f51f28.png) ![](http://image.prntscr.com/image/28d90ce681cb47f7a43e16e246f21f79.png)

In this tutorial, we'll just be focusing on **mp_mod** and we will be ignoring the others: core_mod, cp_mod and zm_mod.

Secondly, we need to setup a GSC which we know will be **guaranteed** to be executed by the Black Ops III engine. This is known as **"_clientids.gsc"**, this will be our home script.

We will want to right-click on our mp_mod and open the **Zone Folder** where you will see 4 .zone files. Go back a directory so that you are in the directory **/path/to/BO3/mods/our_first_mod/**

![](http://image.prntscr.com/image/f8e9c6434f3e40158e5dfdda889ecd36.png) ![](http://image.prntscr.com/image/bbb8b0de3bf0408a81015cefe51c0243.png)

Now we need to make 3 directories, all within each other as we need this path:
**/path/to/BO3/mods/our_first_mod/scripts/mp/gametypes**
![](http://image.prntscr.com/image/8969a4e5ec1d4040b4df7a1b42c41f7c.png)

In the above screenshot, I have already created the file "**_clientids.gsc**" - so if you haven't already, you should create the file as this is our home script and is a necessity to making our mod work within all stock and custom maps. Now within the "**_clientids.gsc**" file, we need to write (or paste) the following code:

```c
#using scripts\codescripts\struct;
#using scripts\shared\callbacks_shared;
#using scripts\shared\system_shared;

#insert scripts\shared\shared.gsh;
#namespace clientids;
REGISTER_SYSTEM( "clientids", &__init__, undefined )
	
function __init__()
{
	callback::on_start_gametype( &init );
	callback::on_connect( &onPlayerConnect );
	callback::on_spawned( &onPlayerSpawned ); 
}
function init() { level.clientid = 0; }

function onPlayerConnect()
{
	self.clientid = matchRecordNewPlayer(self);
	if (!isdefined(self.clientid) || self.clientid == -1)
	{
		self.clientid = level.clientid;
		level.clientid++;
	}
	iprintln("Client: " + self.name + " | Client ID: " + self.clientid);
}

function onPlayerSpawned()
{
	self iprintln("Tutorial by ^4Byte");
	self iprintln("viamods.com");
}
```

To break this code down, it does the following:
When the file is first called (executed) by the engine, it will run the function **__init__()** by default. So because of this, we want to setup our callbacks straight away so we can start catching the player's in-game.
We have setup callbacks for **on_connect();** and **on_spawned();** - *on_connect();* is called when a player is connecting to the game or server. *on_spawned();* is called every time a player spawns (the first initial spawn, or when they spawn after dying, etc.).
When the player joins, we assign a "**clientid**" to them. This allows us to call or make edits to the player by using their ID (identifier).
When a player spawns, we will write them a message in their *killfeed* by using the function "**iprintln(text);**".
Tip: You want to make sure to reference self before calling "**iprintln(text);**" because if you don't, the text is then printed to all the players within the game!

Now that our GSC is all setup, we need to make a link from our Zone file to our GSC (home script) file. This can be done by opening up your "**mp_mod**" Zone File by right-clicking via the Mod Tools Launcher and selecting "**Edit Zone File**".

![](http://image.prntscr.com/image/711f9bae35ee40d3aac821150aac65d4.png)

Amend your Zone File to look like this:

```c
>mode,mp
>type,common

#include "mp_mod.class"
scriptparsetree,scripts/mp/gametypes/_clientids.gsc
```

*(The extra line is the line beginning with **scriptparsetree** - this defines the location of a GSC to be compiled into the FastFile)*

Now we need to **compile** our mod and load up a game. To compile our mod, we will need to select the following functions on the Mod Tools Launcher.
Within the parameters field, enter "**mp_mod.ff**" so we can compile our map into a FastFile.

![](http://image.prntscr.com/image/d13eb4dc5bfd415b8893c1d41d89d423.png)

Your output should be like this:

> B:\SteamLibrary\steamapps\common\Call of Duty Black Ops III\\gdtdb\gdtdb.exe /update

> gdtDB: updating

> processed (0 GDTs) (0 assets) in 2.612 sec

> gdtDB: successfully updated database.

> B:\SteamLibrary\steamapps\common\Call of Duty Black Ops III\\bin\linker_modtools.exe -language english -fs_game our_first_mod -modsource mp_mod

> Linking "mp_mod" (mods\our_first_mod stable 2535281 v593): 

> processing...

> done: 0m3.25s

> Linking "en_mp_mod" (mods\our_first_mod stable 2535281 v593): 

> processing...

> done: 0m2.03s

And to launch, we simply untick everything on the right-side, make sure "**mp_mod**" is ticked on the left window and click Build. This should launch Black Ops 3 to the title screen and at the top-right, we should see "**mod loaded: our_first_mod**" like this:

![](http://image.prntscr.com/image/1538ee64d4b44649b028ff088dc7f8ec.png)

All you'll want to do is go into a private match and start the game! Once you're finally in a game, you should see when you spawn at the bottom-left of your screen (in the killfeed) "Tutorial by Byte", "viamods.com".

![](http://image.prntscr.com/image/f8b70b4d8a8c4470bfbc866ada093a48.png) ![](http://image.prntscr.com/image/7cf9b4b02b6b42bca103c4fd487002c0.png)

You can also just download the source by downloading the attached ZIP file on the [forum post](http://viamods.com/index.php/topic,44.0.html), this ZIP file will contain all the necessary files in order to complete this tutorial.

*Tutorial Courtesy of Byte*
