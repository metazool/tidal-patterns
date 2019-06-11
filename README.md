connect jack to ffmpeg

 * ffmpeg -f jack -i ffmpeg -y out.wav

send superdirt output to jack

 * jack_connect SuperCollider:out_2 ffmpeg:input_1

send _all_ of :0.0 / :hw:0 (not what we want) to twitch via ffmpeg:

```
streaming() {
     INRES="1024*748" # input resolution
     OUTRES="1024*768" # output resolution
     FPS="15" # target FPS
     GOP="30" # i-frame interval, should be double of FPS, 
     GOPMIN="15" # min i-frame interval, should be equal to fps, 
     THREADS="2" # max 6
     CBR="1000k" # constant bitrate (should be between 1000k - 3000k)
     QUALITY="ultrafast"  # one of the many FFMPEG preset
     AUDIO_RATE="44100"
     STREAM_KEY="$1" # use the terminal command Streaming streamkeyhere to stream your video to twitch or justin
     SERVER="live-sjc" # twitch server in California, see http://bashtech.net/twitch/ingest.php to change 
     
                ffmpeg -f x11grab -s "$INRES" -r "$FPS" -i :0.0 -f alsa -i hw:0 -f flv -ac 2 -ar $AUDIO_RATE \
              -vcodec libx264 -g $GOP -keyint_min $GOPMIN -b:v $CBR -minrate $CBR -maxrate $CBR -pix_fmt yuv420p\
              -s $OUTRES -preset $QUALITY -tune film -acodec libmp3lame -threads $THREADS -strict normal \
               -bufsize $CBR "rtmp://$SERVER.twitch.tv/app/$STREAM_KEY"
}
```
