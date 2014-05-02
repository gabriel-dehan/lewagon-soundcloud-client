# Soundcloud Streamer

The idea is to develop a program that will be able to fetch sounds from soundcloud.
You can do what you want, from a command line interface to a ruby GUI application.

Be creative, try to make a nice interface, where you can maybe manage your playlists, download sounds, pause the current sound, rewind it, go forward, like a real music player, you know ?

A few years ago someone did a Command Line Interface Client for Soundcloud :

![Screen Shot 2013-01-20 at 15 37 03](https://f.cloud.github.com/assets/3432/81282/06c44c7e-630f-11e2-9a91-85c9b917835c.png)
![Screen Shot 2013-01-20 at 15 37 54](https://f.cloud.github.com/assets/3432/81281/06b05df4-630f-11e2-8b55-7f3c18126831.png)

Maybe you could do even better ?

## How to ?

**Don't put your code in the lib/ directory, he is there for external libraries, like, well, this :**

For the purpose of this exercices, I created a small library, that lives in the `lib/` directory.
Here is how to require it :

`require_relative 'lib/stream.rb`

This library provides three things :

- A way to download any Soundcloud sound in a temporary directory to stream the mp3 file
- A way to download any Soundcloud sound in a less temporary fashion so you can save the mp3 file
- A way to display a progress bar as the retrieving of the Souncloud stream is, well, progressing.

To use it :

Example 1 : Get sounds and stores them in temporary files
```ruby

# We instanciate the streamer client (don't forget to replace SOUNDCLOUD_CLIENT_ID with your real Client id)
streamer = Stream::Client.new(client_id: SOUNDCLOUD_CLIENT_ID)

# The streamer requires you to give it a Soundcloud API stream_url
streamer.resolve("https://api.soundcloud.com/tracks/147462663/stream")

# This will retrieve the previous URL's content and store it in a temporary file
file = streamer.load do |length, position, data|
  # This will display a progress_bar when downloading the file
  Stream::Helpers.progress_bar("=", ">", position)
end

puts file # => /tmp/fosdjiofw2343242.mp3

# Here you do your stuff with the file (Play it for example !)

# Delete the temporary files
streamer.end_stream
```

Example 2 : Get sounds and stores them in a directory
```ruby

# The path variables is the directory in which you want to download your soundcloud files
streamer = Stream::Client.new(client_id: @client_id, path: 'downloads')

streamer.resolve("https://api.soundcloud.com/tracks/147462663/stream")
file = streamer.load "myMusicFile" do |length, position, chunk|
  Stream::Helpers.progress_bar("=", ">", position)
end

puts file # => downloads/myMusicFile.mp3

# BE CAREFUL ! The following line will DELETE the last downloaded file, you might not want to do that
streamer.end_stream :force

# You can download as many files as you want :
streamer.resolve("https://api.soundclound.com/blabla/other_url")
file2 = streamer.load "myOtherMusicFile"
streamer.resolve("https://api.soundclound.com/blabla/yet_another_other_url")
file3 = streamer.load "myOtherMusicFile3"

```

## Howdy, I want to create a nice interface for my application !
- Want to build a NICE command line interface ? Go for curses http://ruby-doc.org/stdlib-1.9.3/libdoc/curses/rdoc/Curses.html
  - If you want to use curses, be prepared to follow a lot of tutorials, it's not that easy, but rewarding.
- Want to build a NICE GUI (Graphic User Interface) ? Go for Shoes http://shoesrb.com/

## Tips & Resources
- To retrieve playlists, sounds, users, track names and so on you will need to access the soundcloud API :
  - Go to https://developers.soundcloud.com/ and create an account and register a new application
  - Use the Ruby Soundcloud API Wrapper : https://github.com/soundcloud/soundcloud-ruby, it's really easy

- To get the the stream_url for a sound, you will need the soundcloud API for tracks : https://developers.soundcloud.com/docs/api/reference#tracks, it's really easy to get tracks with the soundcloud-ruby wrapper.

- You will want to play sounds ! To play sounds you will need the `audite` gem.
   - https://github.com/georgi/audite
   - Theres is a good example here : https://github.com/georgi/audite/blob/master/bin/audite
   - But you might want a simplier example :
```ruby
player = Audite.new

# The code inside this block will be executed when the player has finished a song.
player.events.on(:complete) do
  if !player.active
    player.close
  end
end

# You need to load a sound
player.load("downloads/myFile.mp3")

# And start it
player.start_stream

# This line might be a bit mysterious but what it does is saying : "Don't end my program until the sound is played"
player.thread.join
```

- Final tip : the code in `lib/stream` is documented, thus, if you want to read it you can.