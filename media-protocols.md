# Media protocols

## RTSP

<https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol>

Browsers cannot see it, but VLC and `ffplay` can: <http://stackoverflow.com/questions/2245040/how-can-i-display-an-rtsp-video-stream-in-a-web-page>

Wowza hosts a live demo as of 2016:

    vlc rtsp://184.72.239.149/vod/mp4:BigBuckBunny_115k.mov

<http://stackoverflow.com/questions/4303439/what-is-the-difference-between-rtp-or-rtsp-in-a-streaming-server>

Only does initial signaling, actual data goes through other protocols like RTP + RTCP.

## Implementations

So I suppose Wowza implements an RTSP server. But it is closed source: <http://alternativeto.net/software/wowza-media-server-pro/?license=opensource>

<https://gstreamer.freedesktop.org/documentation/rtp.html> looks like a major open source implementation.

An Android implementation that streams the camera: <https://github.com/fyhertz/libstreaming-examples/tree/7db70d3bc63832d86a734580e346b8d931e6697b/example1> Follow the instructions there, find your IP with <http://android.stackexchange.com/questions/2984/how-can-i-see-what-ip-address-my-android-phone-has/130468#130468> and:

    vlc rtsp://192.168.88.141:1234

The library that implements it: <https://github.com/fyhertz/libstreaming/tree/94189f41351937e56e41e1982d4ddd441426185e/src/net/majorkernelpanic/streaming/rtsp> Looks simple.

## RTP

## RTCP

<https://en.wikipedia.org/wiki/Real-time_Transport_Protocol>

Often used together. Data goes through RTP. RTCP synchronizes it.

RTP implementation: <https://github.com/fyhertz/libstreaming/tree/94189f41351937e56e41e1982d4ddd441426185e/src/net/majorkernelpanic/streaming/rtp>

## RTMP

<https://en.wikipedia.org/wiki/Real_Time_Messaging_Protocol>

Ex Macromedia proprietary, bought by Adobe, and then semi-released.
