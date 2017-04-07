Meister Web Player
====

Plugable JavaScript web player - version v4.9.3.

This is the Meister core. It required additional plugins to get set up. This guide will help you get starting up and play a simple MP4 video.

Getting started
----

To get started simply include the ```Meister.js``` in your page. There are two ways of setting up the Meister Player. The usage of ```<script>``` tags and the use of ES6 modules. We will explain the ```<script>``` tag version first.

Meister on it's own can't play video's it requires at least a player plugin and a media plugin. We will show in the example below how to add these plugins to get a playing MP4 file.
We will be using the following plugins:

- [BaseMedia for Meister](https://github.com/meisterplayer/media-basemedia) (This is the plugin for MP4 playback)
- [HTML5 Player for Meister](https://github.com/meisterplayer/player-html5player) (This is the plugin to playback using HTML5)
- [Standard UI for Meister](https://github.com/meisterplayer/ui-standardui) (This is the plugin to get a different UI for Meister)


### Setting up using ```<script>``` tags

The following snippet can be used to initialize the Meister player:

``` HTML
<!DOCTYPE html>
<html>
    <head>
        <title>Meister player example</title>
        <!-- It's important that Meister.js will be loaded before the plugins -->
        <script src="Meister.js"></script>
        <script src="BaseMedia.js"></script>
        <script src="Html5Player.js"></script>
        <script src="StandardUi.js"></script>
    </head>
    <body>
        <div id="player"></div>
        <script>
            // Initialize the meister player
            // Meister uses the querySelector to get the dom element.
            var meisterPlayer = new Meister('#player', {
                // Configures Meister player to use these plugin.
                // We will later go in depth what these are for.
                BaseMedia: {},
                Html5Player: {},
                StandardUi: {},
            });

            // Configures meister to play the mp4 media item.
            meisterPlayer.setItem({
                src: 'INSERT_URL_TO_MP4_HERE',
                type: 'mp4', // Tells meister we will play an mp4 item. 
            });

            // Tells meister we are ready to load the player and start playing
            meisterPlayer.load();
        </script>
    </body>
</html>
```

This is the basic way of getting setup with Meister. We will later get in depth in what these functions exactly does and how we can configure Meister to our likings.

### Setting up using ES6 modules

The following example shows how you can use ES6 modules to load in the Meister players with the plugins mentioned above.

First install Meister through NPM:

``` Bash
npm install -S @npm-wearetriple/meisterplayer
```

And for the additional plugins you can also npm install them:

```
npm install -S @npm-wearetriple/meister-plugin-basemedia
npm install -S @npm-wearetriple/meister-plugin-html5player
npm install -S @npm-wearetriple/meister-plugin-standardui
```

``` JavaScript
import Meister from '@npm-wearetriple/meisterplayer';
import BaseMedia from '@npm-wearetriple/meister-plugin-basemedia';
import Html5Player from '@npm-wearetriple/meister-plugin-html5player';
import StandardUi from '@npm-wearetriple/meister-plugin-standardui';

// Initialize the meister player
// Meister uses the querySelector to get the dom element.
var meisterPlayer = new Meister('#player', {
    // Configures Meister player to use these plugin.
    // Uses pluginName as object name to be future proof.
    [BaseMedia.pluginName]: {},
    [Html5Player.pluginName]: {},
    [StandardUi.pluginName]: {},
});

// Configures meister to play the mp4 media item.
meisterPlayer.setItem({
    src: 'INSERT_URL_TO_MP4_HERE',
    type: 'mp4', // Tells meister we will play an mp4 item. 
});

// Tells meister we are ready to load the player and start playing
meisterPlayer.load();

```

API
----

We will explain the API here for all methods on Meister

Meister instance properties
----

### Meister(query:*String|Element*, config:*Object*):*Meister* ###

Constructor of Meister.

- query:String - The dom Query to select the ```<div>``` element you want to use for Meister
- query:Element - The dom element you want to use. This is an alternate to using query:String.
- config:Object - The config object to initialize plugins and configure them to your liking. Check each plugin for documentation to configure a plugin.

returns an instance of Meister

Example:

``` JavaScript
// Using the query:String method:
var meisterPlayer = new Meister('#player', {
    // Configuration per plugin goes here.
});

// Using the query:Element method:
var meisterPlayer = new Meister(document.getElementById('player'), {
    // Configuration per plugin goes here.
});

```

### **Methods:** ###

### setItem(item:*Object\<MediaObject\>*); ###

Sets the media item that you want to play. Configuration can differ per plugin but the basic item object has a src & type.

MediaObject:
- src:*String* : URL to the media.
- type:*String* : The type of the media. Check the plugin documentation to see what type exactly you have to input.
- ...:*any* : A MediaObject can have more options but this differs per plugin. Please check the plugin documentation for more options.

Example (requires a mp4 plugin): 

``` JavaScript
meisterInstance.setItem({
    src: 'https://example.com/video.mp4',
    type: 'mp4'
});
```

### setPlaylist(items:*Array*[*Object*:\<*MediaObject*\>]) ###

Same as setItem(Object:*MediaObject*) only this method allows for multiple items to be set. Meister will walk through the items one by one (When the end event is triggered).

Please see setItem(Object:*MediaObject*) for the documentation of *MediaObject*

Example:

``` JavaScript
meisterInstance.setPlaylist([
    {
        src: 'https://example.com/video.mp4',
        type: 'mp4'
    },
    {
        src: 'https://example.com/anotherVideo.mp4',
        type: 'mp4'
    }
]);
```

### switchItem(item:*Object:\<MediaObject\>*) ###

Allows for switching items inside of Meister. This way you can load a new item while already playing a other item.

Please see setItem(Object:*MediaObject*) for the documentation of *MediaObject*

Example:

``` JavaScript
meisterInstance.switchItem({
    src: 'https://example.com/video.mp4',
    type: 'mp4'
});
```

### on(hook:String, handler:Function(event:any), caller?:String) ###

Listens for events happening inside of Meister. For all the hooks checkout the events section. Also you can check per plugin what events are available. 

- hook:*String* : The name of the event you want to listen to.
- handler:Function(event:any) : The callback for the event. What returns is different per event. 
- caller?:String : The caller of the event. This is used for tracking if an exception is thrown so you can see where the exception occured.

Example:

``` JavaScript
// Without caller set
meisterInstance.on('playerPause', () => {
    console.log('The player is now paused');
});

meisterInstance.on('playerPlay', () => {
    console.log('The player triggered play');
}, 'MyScript');
```

### one(hook: String, handler:Function(event:any), caller?:String) ###

This is the same as **on(hook:String, handler:Function(event:any), caller?:String)**. Only difference is this function only listens one time for the event. 

Example:

``` JavaScript
// Without caller set
meisterInstance.one('playerPause', () => {
    console.log('The player is now paused');
});

meisterInstance.one('playerPlay', () => {
    console.log('The player triggered play');
}, 'MyScript');
```

### **Getters & Setters:** ###

### get version:*String* ###

Returns the current version of Meister. Yields `"vX.Y.Z"`

Example:

```JavaScript
// Outputs v4.9.6
console.log(meisterInstance.version);
```

### get/set showControls:*Boolean*  ###

- set: Hides/Shows the controls.
- get: Returns the current controls status.

Example:

``` JavaScript
// Hides the controls
meisterInstance.showControls = false;

// Shows the controls
meisterInstance.showControls = true;
```