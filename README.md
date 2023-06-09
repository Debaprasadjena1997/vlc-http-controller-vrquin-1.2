# vlc-http-controller
An ExpressJS that starts VLC (VideoLAN) and presents a controller as HTTP endpoints.

The project builds on running VLC in "interactive RC mode". Read more here: https://wiki.videolan.org/Documentation:Modules/rc/

When the server runs, it launches a "remote control" interface at localhost. If can, however, also be address by any other program or script using standard HTTP protocol.

![The "remote control" running in browser](./browser-controller.png)

BEWARE: Some of the functions require callbacks (e.g. "get_time", "get_length", "playlist" and more), and I have written some promise-based code to await these before sending a response to the HTTP request. HOWEVER, this assumes that VLC handles these functions in a queue using FIFO, and that they're not running synchronously. I haven't stress tested this nor read documentation, so it might cause errors in the values that are returned.

## Requirements

Requires NodeJS, ExpressJS, VLC and some media to play.

It should be able to run on a small computer, e.g. a Raspberry PI or similar, and thereby provide basic mediaplayer functionality.

Tested on Ubuntu.

## How to configure

Use "library.json" to specify your media library.

Each media object requires a unique ID, a title (to be displayed) and the path of the media (accessible to VLC).

## How to run

Run 

    git clone https://github.com/Debaprasadvrquin/vlc-http-controller-vrquin-1.2.git

    cd vlc-http-controller-vrquin-1.2

    npm install express
    
    sudo pip install screen-brightness-control

    node index.js

Then visit:

    http://localhost:8000

where the "remote control" is live.

Otherwise use routes directly as specified in index.js

## VLC Commands (as output by 'help')

    +----[ CLI commands ]
    | add XYZ  . . . . . . . . . . . . . . . . . . . . add XYZ to playlist
    | enqueue XYZ  . . . . . . . . . . . . . . . . . queue XYZ to playlist
    | playlist . . . . . . . . . . . . .  show items currently in playlist
    | search [string]  . .  search for items in playlist (or reset search)
    | delete [X] . . . . . . . . . . . . . . . . delete item X in playlist
    | move [X][Y]  . . . . . . . . . . . . move item X in playlist after Y
    | sort key . . . . . . . . . . . . . . . . . . . . . sort the playlist
    | sd [sd]  . . . . . . . . . . . . . show services discovery or toggle
    | play . . . . . . . . . . . . . . . . . . . . . . . . . . play stream
    | stop . . . . . . . . . . . . . . . . . . . . . . . . . . stop stream
    | next . . . . . . . . . . . . . . . . . . . . . .  next playlist item
    | prev . . . . . . . . . . . . . . . . . . . .  previous playlist item
    | goto, gotoitem . . . . . . . . . . . . . . . . .  goto item at index
    | repeat [on|off]  . . . . . . . . . . . . . .  toggle playlist repeat
    | loop [on|off]  . . . . . . . . . . . . . . . .  toggle playlist loop
    | random [on|off]  . . . . . . . . . . . . . .  toggle playlist random
    | clear  . . . . . . . . . . . . . . . . . . . . .  clear the playlist
    | status . . . . . . . . . . . . . . . . . . . current playlist status
    | title [X]  . . . . . . . . . . . . . . set/get title in current item
    | title_n  . . . . . . . . . . . . . . . .  next title in current item
    | title_p  . . . . . . . . . . . . . .  previous title in current item
    | chapter [X]  . . . . . . . . . . . . set/get chapter in current item
    | chapter_n  . . . . . . . . . . . . . .  next chapter in current item
    | chapter_p  . . . . . . . . . . . .  previous chapter in current item
    | 
    | seek X . . . . . . . . . . . seek in seconds, for instance `seek 12'
    | pause  . . . . . . . . . . . . . . . . . . . . . . . .  toggle pause
    | fastforward  . . . . . . . . . . . . . . . . . . set to maximum rate
    | rewind . . . . . . . . . . . . . . . . . . . . . set to minimum rate
    | faster . . . . . . . . . . . . . . . . . .  faster playing of stream
    | slower . . . . . . . . . . . . . . . . . .  slower playing of stream
    | normal . . . . . . . . . . . . . . . . . .  normal playing of stream
    | rate [playback rate] . . . . . . . . . .  set playback rate to value
    | frame  . . . . . . . . . . . . . . . . . . . . . play frame by frame
    | fullscreen, f, F [on|off]  . . . . . . . . . . . . toggle fullscreen
    | info [X] . .  information about the current stream (or specified id)
    | stats  . . . . . . . . . . . . . . . .  show statistical information
    | get_time . . . . . . . . .  seconds elapsed since stream's beginning
    | is_playing . . . . . . . . . . . .  1 if a stream plays, 0 otherwise
    | get_title  . . . . . . . . . . . . . the title of the current stream
    | get_length . . . . . . . . . . . .  the length of the current stream
    | 
    | volume [X] . . . . . . . . . . . . . . . . . .  set/get audio volume
    | volup [X]  . . . . . . . . . . . . . . .  raise audio volume X steps
    | voldown [X]  . . . . . . . . . . . . . .  lower audio volume X steps
    | achan [X]  . . . . . . . . . . . .  set/get stereo audio output mode
    | atrack [X] . . . . . . . . . . . . . . . . . . . set/get audio track
    | vtrack [X] . . . . . . . . . . . . . . . . . . . set/get video track
    | vratio [X] . . . . . . . . . . . . . . .  set/get video aspect ratio
    | vcrop, crop [X]  . . . . . . . . . . . . . . . .  set/get video crop
    | vzoom, zoom [X]  . . . . . . . . . . . . . . . .  set/get video zoom
    | vdeinterlace [X] . . . . . . . . . . . . . set/get video deinterlace
    | vdeinterlace_mode [X]  . . . . . . .  set/get video deinterlace mode
    | snapshot . . . . . . . . . . . . . . . . . . . . take video snapshot
    | strack [X] . . . . . . . . . . . . . . . . .  set/get subtitle track
    | 
    | vlm  . . . . . . . . . . . . . . . . . . . . . . . . .  load the VLM
    | description  . . . . . . . . . . . . . . . . .  describe this module
    | help, ? [pattern]  . . . . . . . . . . . . . . . . .  a help message
    | longhelp [pattern] . . . . . . . . . . . . . . a longer help message
    | lock . . . . . . . . . . . . . . . . . . . .  lock the telnet prompt
    | logout . . . . . . . . . . . . . .  exit (if in a socket connection)
    | quit . . . . . . . .  quit VLC (or logout if in a socket connection)
    | shutdown . . . . . . . . . . . . . . . . . . . . . . .  shutdown VLC
