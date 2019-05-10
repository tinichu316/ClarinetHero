# Clarinet Hero
##### A Game by William Wang

## Introduction
Clarinet Hero is a Beatmania-style rhythm game based around the clarinet that focuses around playing more difficult songs as you progress.

## How to Play
It is recommended to download the standalone version at https://tinichu.itch.io/clarinet-hero for the latest updates.

Alternatively, go to https://tinichu316.github.io/ on a WebGL supported browser and click on the title Clarinet Hero (2019). However, it may run slowly.

## Current Development
Much of the main content of the game is complete. Additional future updates would include more polish and more effects, as well as potential fine-tuning of the game mechanics. Updates further down the road could include additional songs or other features but there is currently no plan of action for those.

## Inspiration and Development
The game is heavily inspired from similar rhythm games such as stepmania, flash flash revolution, and of course, guitar hero. Much of the game mechanics are taken from those games, such as the combo and multiplier system, as well as the grading and life bar system. The combo and multiplier effects were also inspired by games such as super crazy guitar maniac deluxe in that, as the combo chain grew, so too would the intensity and absurdity of the effects.

The main impetus for creating this game was to have a rhythm game based on John Coltrane’s solo on Giant Steps. As far as I knew, there weren’t many rhythm games based on jazz music, and the ones that had jazz songs were limited and far outnumbered by other genres. I also set out initially to have the player press 12 keys instead of only the 4 of stepmania or the 5 of guitar hero, partially to convey the complexity and skill required to play more than 5 notes, but also to add a sense of challenge and to make it easier to convert midi files into beat maps.
However, I soon realized that it was too difficult to have 12 keys for only 8 (10 including thumbs) fingers as you would have to often shift your hands left and right which could disorient you. I instead reduced the maximum number of keys to 8, but it was still too difficult to hit the note exactly on the keys. In response, I increased the hitbox of the buttons while tracking the distance that each note was hit in order to assign a score based on how close it was to the activator. Anti-spam measures were introduced such that if the notes were hit too far away from the activator, they would instead be counted as a missed note even though they are within the hitbox. My goal with the game is not to make it impossible or incredibly difficult, but to make simple progression feasible while also encouraging and rewarding mastery.

Throughout the game, you will obtain various emails that explain and encourage your progress. I felt that this was beneficial to explain some of the reasoning and story behind the song choices which would be difficult to convey otherwise through just a simple song title and subtitle.

### Midi to Beatmap Converter

While working on this project, I did not want to create the beatmaps by hand as it would be quite tedious and not easily scalable to include other songs. Instead, I looked to develop a means of converting a midi melody line into a beatmap file such that it was a reasonable approximation of the actual notes being played. However, it was not as simple as spreading out the notes into 8 'buckets' based on the pitches of the note so that each key would correspond to a small range of notes. I wanted to preserve as much as possible the relative direction between each note.

The main pipeline process goes roughly like this:
* split the song into phrases based on 16 bars or if there's a long reset
* for each phrase, place the notes into each key 'bucket' based on pitch.
* check that if the previous note is higher pitch, this note will also be a higher key.
* fix any out of bounds keys by reducing jumps in the keys (e.x. keys 1 2 7 8 9 --> 1 2 6 7 8) to still preserve relative direction
* in the cases where there are many notes in a row ascending or descending (such as in a chromatic scale), we have to roll over the top notes in an intuitive manner. The chosen method was to roll over such that (1 2 3 4 5 6 7 8 9 10 11 12 --> 1 2 3 4 5 6 7 8 5 6 7 8).
* note that the order for doing rolling over and fixing out of bounds keys is important and that you can both fix out of bounds keys by raising and lowering, and that you can roll over ascending and descending.


## Development
The main challenge of developing a rhythm game was keeping everything in time. Initially, I had attempted to lock Unity's framerate and use that as a basis for calculating the distance each note needs to move per frame, as well as the timing that each note would spawn at, based on the bpm. However, this resulted in inconsistency and out of sync behaviour between the note spawning frequencies, the note movements, and the actual sound itself. Some research led me to find this helpful guide: http://ludumdare.com/compo/2014/09/09/an-ld48-rhythm-game-part-2/ which suggested to keep everything in sync based on the playhead position of the audio file. Although it was not possible to check to spawn each subbeat (there are 12 subbeats per beat) in each frame as each subbeat was much shorter than the duration of a frame, it was possible to spawn each beat in accordance with the playhead position and have an internal clock spawn each individual subbeat. A downside is that the subbeats may not line up exactly with the beats (horizontal bars). In this game, the note height is constant and the falling speed is set such that it is the slowest falling speed possible that leaves a 1 note gap between each subbeat, such that the notes do not overlap.

Another challenge was the long note hit detection, particularly in terms of combining the long note with hitboxes as originally each long note was made of a note head and many held notes. In the end, each long note was assigned its own object with a head, tail, and middle notes. Both the head and tail signaled to the activator whether or not the long note was hit to determine if the middle notes could be destroyed or if they had to be passed through. For example, if the head of the long note was not hit, the held notes would turn dark and be unable to be hit. Otherwise, if the head was hit but the button was released partway through the notes, the remaining held notes should turn dark and no longer be active.

Experience was gained using Unity's post processing manager and UI system, as well as with Musescore and python to create the midis and beatmaps ultimately used in the song. The game itself and the core concepts were developed mainly within a week. However additional polish, refactoring, playesting, tweaking, and adjustments were done over another period of two weeks, a few months after the initial code had been written.

The focus of development was to create an intuitive rhythm game system that feels good, fair, and also challenging. I had also wanted to give the game some creative flair in the form of emails and descriptions.

## Controls
The controls are relatively simple.
* ASDFJKL; controls each of the 8 buttons. Hit the buttons as the notes are over the activator to hit the note.
If you hit the note too far from the activator, it will count as a missed note. You can turn this off by selecting "Easy Mode" in the options menu.

## Contributors
Thank you in particular to Jacob for helping me get started with converting midis to beatmaps, Ian for the various sprites, Gordon for the many gameplay suggestions and improvement ideas, and Emily for the wonderful clarinet squeaks that you hear upon a wrong note.
Thank you to the many playtesters who gave their time and feedback on the various mechanics of the game.

## Song Suggestions
If you have any songs that you would like to see in this game, please send me a midi, ideally one with a melody part already isolated. As of now, the midi to beatmap converter is limited in that it cannot account for songs that have changes in tempo, nor be able to accurately parse chords or multiple notes being played at once (just like how a real clarinet can only play one note at a time).
