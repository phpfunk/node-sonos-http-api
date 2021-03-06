SONOS HTTP API
==============

A simple http based API for controlling your Sonos system. I try to follow compatibility versioning between this and sonos-discovery, which means that 0.3.x requires 0.3.x of sonos-discovery.

USAGE
-----

Start by fixing your dependencies. Invoke the following command:

`npm install`

This will download the necessary dependencies if possible (you will need git for this)

start the server by running

`node server.js`

Now you can control your system by invoking the following commands:

`http://localhost:5005/zones`
`http://localhost:5005/lockvolumes`
`http://localhost:5005/unlockvolumes`
`http://localhost:5005/pauseall[/{timeout in minutes}]`
`http://localhost:5005/resumeall[/{timeout in minutes}]`
`http://localhost:5005/preset/{JSON preset}`
`http://localhost:5005/preset/{predefined preset name}`
`http://localhost:5005/{room name}/{action}[/{parameter}]`
`http://localhost:5005/reindex`

Example:

`http://localhost:5005/living room/volume/15`
(will set volume for room Living Room to 15%)

`http://localhost:5005/living room/volume/+1`
(will increase volume by 1%)

`http://localhost:5005/living room/next`
(will skip to the next track on living room, unless it's not a coordinator)

`http://localhost:5005/living room/pause`
(will pause the living room)

`http://localhost:5005/living room/favorite/mysuperplaylist`
(will replace queue with the favorite called "mysuperplaylist")

`http://localhost:5005/living room/repeat/on`
(will turn on repeat mode for group)


The actions supported as of today:

* play
* pause
* playpause (toggles playing state)
* volume (parameter is absolute or relative volume. Prefix +/- indicates relative volume)
* groupVolume (parameter is absolute or relative volume. Prefix +/- indicates relative volume)
* mute / unmute
* groupMute / groupUnmute
* seek (parameter is queue index)
* trackseek (parameter is in seconds, 60 for 1:00, 120 for 2:00 etc)
* next
* previous
* state (will return a json-representation of the current state of player)
* favorite
* lockvolumes / unlockvolumes (experimental, will enforce the volume that was selected when locking!)
* repeat (on/off)
* shuffle (on/off)
* pauseall (with optional timeout in minutes)
* resumeall (will resume the ones that was pause on the pauseall call. Useful for doorbell, phone calls, etc. Optional timeout)
* say

State
-----

Example of a state json:

	{
		"currentTrack": {
			"artist":"The Low Anthem",
			"title":"Charlie Darwin",
			"album":"Rough Trade - Counter Culture 2008",
			"duration":270
		},
		"relTime":29,
		"stateTime":1363559099394,
		"volume":11,
		"currentState":"PAUSED_PLAYBACK"
	}


Preset
------

A preset is a predefined grouping of players with predefined volumes, that will start playing whatever is in the coordinators queue.

Example preset (state and uri are optional):

	{
	  "players": [
	    { "roomName": "room1", "volume": 15},
	    {"roomName": "room2", "volume": 25}
	  ],
	  "state": "stopped",
	  "favorite": "my favorite name",
	  "uri": "x-rincon-stream:RINCON_0000000000001400",
	  "playMode": "SHUFFLE"
	}

The first player listed in the example, "room1", will become the coordinator. It will loose it's queue when ungrouped but eventually that will be fixed in the future. Playmodes are the ones defined in UPnP, which are: NORMAL, REPEAT_ALL, SHUFFLE_NOREPEAT, SHUFFLE
Favorite will have precedence over a uri. Playmode requires 0.4.2 of sonos-discovery to work.

preset.json
-----------

You can create a file with pre made presets, called presets.json. I've included a sample file based on my own setup. In the example, there is one preset called `all`, which you can apply by invoking:

`http://localhost:5005/preset/all`

settings.json
-------------

If you want to change port or the cache dir for tts files, you can create a settings.json file and put in the root folder. The defaults are:

{
  port: 5005,
  cacheDir: './cache'
}

Override as it suits you.

Favorites
---------

It now has support for starting favorites. Simply invoke:

`http://localhost:5005/living room/favorite/[favorite name]`

and it will replace the queue with that favorite. Bear in mind that favorites may share name, which might give unpredictable behavior at the moment.


Say (TTS support)
-----------------

Experimental support for TTS. Action is:

/[Room name]/say/[phrase][/[language_code]]

Example:

/Office/say/Hello, dinner is ready
/Office/say/Hej, maten är klar/sv