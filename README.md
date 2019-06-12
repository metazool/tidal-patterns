run jack

 * jackd -R -d alsa -r 44100 -P

send superdirt output to jack (after starting ffmpeg)

 * jack_connect SuperCollider:out_2 ffmpeg:input_1

streaming via ffmpeg

```
streaming() {
     INRES="1440*900" # input resolution
     OUTRES="640*480" # output resolution
     FPS="10" # target FPS
     GOP="20" # i-frame interval, should be double of FPS, 
     GOPMIN="10" # min i-frame interval, should be equal to fps, 
     THREADS="4" # max 6
     CBR="1000k" # constant bitrate (should be between 1000k - 3000k)
     QUALITY="ultrafast"  # one of the many FFMPEG preset
     AUDIO_RATE="44100"
     STREAM_KEY=$STREAM_KEY # Twitch stream key
     SERVER="live-lhr" # twitch server in London, see http://bashtech.net/twitch/ingest.php to change 


	 ffmpeg -f x11grab -s "$INRES" -r "$FPS" -i :0.0 -f jack -i ffmpeg -f flv -ac 2 -ar $AUDIO_RATE \
	  -vcodec libx264 -g $GOP -keyint_min $GOPMIN -b:v $CBR -minrate $CBR -maxrate $CBR -pix_fmt yuv420p\
	  -s $OUTRES -preset $QUALITY -tune film -acodec aac -threads $THREADS -strict normal \
	   -bufsize $CBR "rtmp://$SERVER.twitch.tv/app/$STREAM_KEY" 
}
```

