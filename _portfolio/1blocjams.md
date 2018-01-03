---
layout: post
title: BlocJams
thumbnail-path: "img/blocjams1.png"
short-description: A digital music player like Spotify developed to learn frontend web development.

---

{:.center}
![]({{ site.baseurl }}/img/bloc_jams_logo.jpg)

## Explanation

Welcome to **Bloc Jams**, a digital music player like Spotify that I developed to learn frontend web development. My main focus was to learn responsive development using **CSS** and interactivity with **JavaScript DOM Scripting**. Once I had completed I had used the same project to learn **Angular JavaScript** framework by refactoring my existing vanilla JavaScript code.

## Problem

I needed to challenge myself technically so focused on the most demanding aspects of a music player the PLAY / PAUSE and SKIP features. My primary aims were to make the code functional, then refactor to a DRY state but also set myself up to a position where I am able to refactor into AngularJS. So clear, understandable well commented code was important.  

## Solution
Highlighted below were some stand out areas that closely matched my focus points and key concepts I wanted to achieve from developing Bloc Jams in vanilla JavaScript and AngularJS

<h4>1. Event Delegation</h4>

  Adding many event listeners to elements in a document can make the page load and execute JavaScript more slowly. To reduce the number of event listeners, I used event delegation. It allowed me to listen for an event on a parent element but target the behavior on one of its children.

```js
//call parent element
var songListContainer = document.getElementsByClassName('album-view-song-list')[0];
var playButtonTemplate = '<a class="album-song-button"><span class="ion-play"></span></a>';

window.onload = function() {
  setCurrentAlbum(albumPicasso);
  songListContainer.addEventListener('mouseover', function(event) {
// Only target individual song rows during event delegation
  if (event.target.parentElement.className === 'album-view-song-item') {
//use querySelector method because I only need to return a single element with the .song-item-number class.
  event.target.parentElement.querySelector('.song-item-number').innerHTML = playButtonTemplate;
  }
});
// For this event (mouseleave), instead of using event delegation
// because the action of leaving a cell is not something that can
// be specified as easily by listening on the parent. I will selected
// an array of every table row and looped over each to add its event listener:
  for (var i = 0; i < songRows.length; i++) {
    songRows[i].addEventListener('mouseleave', function(event) {
      this.children[0].innerHTML = this.children[0].getAttribute('data-song-number');
    });
  }
```
<h4>2. Play/Pause Method</h4>
 I needed to implement code to allow for the following actions:
* A click on a song number to play it and for the number to change to a pause button,
* When the mouse leaves, the pause button should remain,
* When a song changes, the previous playing song should revert content back to the song number.
* When we hover over other songs while the current song is playing, the play button should appear whilst not altering the current playing song.

<img src="https://bloc-global-assets.s3.amazonaws.com/images-frontend/screenshots/27-dom-scripting-play-pause-2/play_on_hover.gif" alt="play button on hover">

```js
// Store state of playing songs
var currentlyPlayingSong = null;

var clickHandler = function() {
      //pass in song item number
       var songNumber = parseInt($(this).attr('data-song-number'));

       if (currentlyPlayingSongNumber !== null) {
           // Revert to song number for currently playing song because user started playing new song.
           var currentlyPlayingCell = getSongNumberCell(currentlyPlayingSongNumber);

           currentlyPlayingCell = getSongNumberCell(currentlyPlayingSongNumber);
           currentlyPlayingCell.html(currentlyPlayingSongNumber);
       }

        if (currentlyPlayingSongNumber !== songNumber) {
            // Switch from Play -> Pause button to indicate new song is playing.
            setSong(songNumber);
            currentSoundFile.play();
            updateSeekBarWhileSongPlays();
            currentSongFromAlbum = currentAlbum.songs[songNumber - 1];

            $(this).html(pauseButtonTemplate);
            currentSongFromAlbum = currentAlbum.songs[songNumber - 1];
            updatePlayerBarSong();
        } else if (currentlyPlayingSongNumber === songNumber) {

           if (currentSoundFile.isPaused()) {
               $(this).html(pauseButtonTemplate);
               $('.main-controls .play-pause').html(playerBarPauseButton);
              currentSoundFile.play();
           } else {
               $(this).html(playButtonTemplate);
              $('.main-controls .play-pause').html(playerBarPlayButton);
               currentSoundFile.pause();
           }

       }

    };

```

<h4>3. Refactor to AngularJS</h4>

The most exciting part was putting JS to use in a framework. My biggest take aways from refactoring to AngularJS was the importance of clear and well commented code for future work. In addition learning how to use Routing and States was particularly useful when working out which functionalities would be be implemented as Services or Controllers.


```js
/**
 * @desc Buzz object audio file
 * @type {Object}
 */
 var currentBuzzObject = null;
/**
 * @function setSong
 * @desc Stops currently playing song and loads new audio file as currentBuzzObject
 * @param {Object} song
 */
var setSong = function(song) {
    if (currentBuzzObject) {
        currentBuzzObject.stop();
        currentSong.playing = null;
    }

    currentBuzzObject = new buzz.sound(song.audioUrl, {
        formats: ['mp3'],
        preload: true
    });

    currentSong = song;
 };
/**
* @function play
* @desc Play current song
* @param {Object} song
*/
 SongPlayer.play = function(song) {
       song = song || SongPlayer.currentSong;
       if (SongPlayer.currentSong !== song) {
         setSong(song);
         playSong(song);
       } else if (SongPlayer.currentSong === song) {
         if (currentBuzzObject.isPaused()) {
           playSong(song);
         }
       }
     };


```
## Result

<img src="/img/blocJams_cover.png" alt="play button on hover">
<img src="/img/lilblocjamz.png" alt="play button on hover">

## Languages and Tools Used
JavaScript, AngularJS, HTML, CSS, Git
