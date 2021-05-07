# Onvif IP Camera Dome annotations
Onvif PTZ IP camera dome tiny hacks

This is my experiments/annotations/effort to control some onvif PTZ IP camera dome.

## Dual Lens - 1080P - H265

![Dual lens](https://github.com/avafinger/onvif_ip_camera_dome/raw/main/cam1_dual_lens_1080P_PTZ.jpg)

**Pros**

  * RTSP with H265 + sound
  * Dual lens (4x)
  * Pre-set PTZ

**Cons**

  * There is no way to prevent someone from streaming a video from this camera if they collect your wifi credentials

**Camera info**

    Camera Model (onvif): EYEPLUS_DEV
    FirmwareVersion: 7.3.1.0114
    Video Codec: MPEG-H Part2/HEVC (H.265) (hevc)
    Audio Codec: PCM ALAW (alaw)
    Video resolution: 640x352 (preview)
    Video resolution: 1920x1080 (main)
  

**Finding Camera Services and URI**

    python ./onvifclient.py 
    Received reply from UDP discovery probe.
    Camera Services:
    Namespace: http://www.onvif.org/ver10/device/wsdl
    Namespace: http://www.onvif.org/ver10/media/wsdl
    Namespace: http://www.onvif.org/ver10/events/wsdl
    Namespace: http://www.onvif.org/ver20/imaging/wsdl
    Namespace: http://www.onvif.org/ver10/deviceIO/wsdl
    Namespace: http://www.onvif.org/ver20/analytics/wsdl
    ()
    1) mainStream 1920x1080
    2) subStream 640x352
    Select profile [1]: 2
    ()
    In VLC open network stream, enter the URI below and provide credentials when prompted:
    ()
    rtsp://192.168.***.**:554/0/av1

  **URI**
  
    Preview URI: rtsp://192.168.***.**:554/0/av1
    Main URI: rtsp://192.168.***.**:554/0/av0
  

**Streaming video (alternatives)**

  VLC, FFMPEG and ONVI-PVR (libva + opencv2) are good alternatives to stream video.
  
  * VLC (video + audio)
  
    Streaming with UDP (Wifi link) did not have good results, link is stable but camera is 20 meters away and there are some concrete walls.
  
        vlc rtsp://192.168.***.**:554/0/av0
    
    For a stable connection use RTP-TCP:
  
        vlc --rtsp-tcp rtsp://192.168.***.**:554/0/av0

  * ONVI-PVR (video)

    Streaming rtsp with tcp:
    
        ./OnvifPVR  rtsp://192.168.***.**:554/0/av0
    
  * ONVIF Device Manager (Windows)

    **Does not work with this camera**
    
    
## Single Len - 1080P - H264

![Single len](https://github.com/avafinger/onvif_ip_camera_dome/raw/main/cam2_single_len_1080P_PTZ.jpg)

**Pros**

  * Option to record in H265
  * Strong wifi signal
  * Manage the camera with ONVIF Device Manager
  
**Cons**

  * RTSP with H264 + sound
  * Stream with ONVIF Device Manager but fails to keep a stream connection, maybe it is using UDP

**Camera info**

    Camera Model (onvif): NVT IPC-model
    Firmware: V5.00.R02.0030695.10010.34
    Preview: 640x360
    Main: 1920x1080
    Video Codec: H264 - MPEG-4 AVC (part 10) (h264)
    Audio Codec: PCM ALAW (alaw)

**Streaming video (alternatives)**

  VLC, FFMPEG and ONVI-PVR (libva + opencv2) are good alternatives to stream video.
  
  * VLC media player (video + audio)
  
    Streaming with UDP (Wifi link) did not have good results, link is stable but camera is 20 meters away and there are some concrete walls.
  
        vlc rtsp://192.168.***.****:554/user=admin_password=52F8MCmn_channel=1_stream=1.sdp?real_stream
    
    For a stable connection use RTP-TCP:
  
        vlc --rtsp-tcp rtsp://192.168.***.***:554/user=admin_password=52F8MCmn_channel=1_stream=0.sdp?real_stream

  * ONVI-PVR (video)

    Streaming rtsp with tcp:
    
        ./OnvifPVR  rtsp://192.168.***.***:554/user=admin_password=52F8MCmn_channel=1_stream=1.sdp?real_stream


  * ONVIF Device manager 2.2.250

    Manage and configure the camera on Windows (C#)
    
  * FFMPEG - ffplay (video + audio)

    **ffplay** seems to be a better choice than **vlc**, vlc 3.0.8 sometimes freeze my Ubuntu box, while ffplay not.
    
        ffplay -rtsp_transport tcp rtsp://192.168.***.***:554/user=admin_password=52F8MCmn_channel=1_stream=1.sdp?real_stream


## Dissecting rtsp over tcp

Legend:

  **C**: Client (vlc or ffplay)
  
  **S**: Server (camera dual lens)
  

  **VLC**
  
    C->S: OPTIONS rtsp://192.168.***.***:554/0/av1 RTSP/1.0
    CSeq: 2
    User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2018.02.18)


    S->C: RTSP/1.0 200 OK
    CSeq: 2
    Server: TAS-Tech Streaming Server V100R001
    Public: DESCRIBE, SET_PARAMETER, SETUP, TEARDOWN, PAUSE, PLAY


    C->S: DESCRIBE rtsp://192.168.***.***:554/0/av1 RTSP/1.0
    CSeq: 3
    User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2018.02.18)
    Accept: application/sdp


    S->C: RTSP/1.0 200 OK
    CSeq: 3
    Server: TAS-Tech Streaming Server V100R001
    Content-Type: application/sdp
    Content-length: 418

    v=0
    o=StreamingServer 3331435948 1116907222000 IN IP4 192.168.***.***
    s=h264.mp4
    i=TAS-Tech Live Cast
    c=IN IP4 192.168.***.***
    t=0 0
    a=recvonly
    a=range:npt=now-
    m=video 0 RTP/AVP 99
    a=control:rtsp://192.168.***.***:554/0/video1
    a=rtpmap:99 H265/90000
    a=fmtp:99 packetization-mode=1
    a=framesize:99 1280-720
    m=audio 0 RTP/AVP 8
    a=control:rtsp://192.168.***.***:554/0/audio
    a=rtpmap:8 pcma/8000
    a=ptime:40


    C->S: SETUP rtsp://192.168.***.***:554/0/video1 RTSP/1.0
    CSeq: 4
    User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2018.02.18)
    Transport: RTP/AVP/TCP;unicast;interleaved=0-1


    S->C: RTSP/1.0 200 OK
    CSeq: 4
    Server: TAS-Tech Streaming Server V100R001
    Session: wDAfWWJi006a; timeout=60
    Transport: RTP/AVP/TCP;unicast;interleaved=0-1;mode=play


    C->S: SETUP rtsp://192.168.***.***:554/0/audio RTSP/1.0
    CSeq: 5
    User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2018.02.18)
    Transport: RTP/AVP/TCP;unicast;interleaved=2-3
    Session: wDAfWWJi006a


    S->C: RTSP/1.0 200 OK
    CSeq: 5
    Server: TAS-Tech Streaming Server V100R001
    Session: wDAfWWJi006a; timeout=60
    Transport: RTP/AVP/TCP;unicast;interleaved=2-3;mode=play


    C->S: PLAY rtsp://192.168.***.***:554/0/av1 RTSP/1.0
    CSeq: 6
    User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2018.02.18)
    Session: wDAfWWJi006a
    Range: npt=0.000-


    S->C: RTSP/1.0 200 OK
    CSeq: 6
    Server: TAS-Tech Streaming Server V100R001


    S->C: $Data follows
    S->C: $Data follows
    S->C: $Data follows
    S->C: $Data follows
    S->C: $Data follows
    C->S: TEARDOWN rtsp://192.168.***.***:554/0/av1 RTSP/1.0
    CSeq: 7
    User-Agent: LibVLC/3.0.8 (LIVE555 Streaming Media v2018.02.18)
    Session: wDAfWWJi006a


    C->S: $Data follows
    S->C: $Data follows
    S->C: RTSP/1.0 200 OK
    CSeq: 7
    Server: TAS-Tech Streaming Server V100R001


  **ffplay**
  
    C->S: OPTIONS rtsp://192.168.***.***:554/0/av1 RTSP/1.0
    CSeq: 1
    User-Agent: Lavf57.83.100


    S->C: RTSP/1.0 200 OK
    CSeq: 1
    Server: TAS-Tech Streaming Server V100R001
    Public: DESCRIBE, SET_PARAMETER, SETUP, TEARDOWN, PAUSE, PLAY


    C->S: DESCRIBE rtsp://192.168.***.***:554/0/av1 RTSP/1.0
    Accept: application/sdp
    CSeq: 2
    User-Agent: Lavf57.83.100


    S->C: RTSP/1.0 200 OK
    CSeq: 2
    Server: TAS-Tech Streaming Server V100R001
    Content-Type: application/sdp
    Content-length: 418

    v=0
    o=StreamingServer 3331435948 1116907222000 IN IP4 192.168.***.***
    s=h264.mp4
    i=TAS-Tech Live Cast
    c=IN IP4 192.168.***.***
    t=0 0
    a=recvonly
    a=range:npt=now-
    m=video 0 RTP/AVP 99
    a=control:rtsp://192.168.***.***:554/0/video1
    a=rtpmap:99 H265/90000
    a=fmtp:99 packetization-mode=1
    a=framesize:99 1280-720
    m=audio 0 RTP/AVP 8
    a=control:rtsp://192.168.***.***:554/0/audio
    a=rtpmap:8 pcma/8000
    a=ptime:40


    C->S: SETUP rtsp://192.168.***.***:554/0/video1 RTSP/1.0
    Transport: RTP/AVP/TCP;unicast;interleaved=0-1
    CSeq: 3
    User-Agent: Lavf57.83.100


    S->C: RTSP/1.0 200 OK
    CSeq: 3
    Server: TAS-Tech Streaming Server V100R001
    Session: Cw1WkBOj03a9; timeout=60
    Transport: RTP/AVP/TCP;unicast;interleaved=0-1;mode=play


    C->S: SETUP rtsp://192.168.***.***:554/0/audio RTSP/1.0
    Transport: RTP/AVP/TCP;unicast;interleaved=2-3
    CSeq: 4
    User-Agent: Lavf57.83.100
    Session: Cw1WkBOj03a9


    S->C: RTSP/1.0 200 OK
    CSeq: 4
    Server: TAS-Tech Streaming Server V100R001
    Session: Cw1WkBOj03a9; timeout=60
    Transport: RTP/AVP/TCP;unicast;interleaved=2-3;mode=play


    C->S: PLAY rtsp://192.168.***.***:554/0/av1 RTSP/1.0
    Range: npt=0.000-
    CSeq: 5
    User-Agent: Lavf57.83.100
    Session: Cw1WkBOj03a9


    S->C: RTSP/1.0 200 OK
    CSeq: 5
    Server: TAS-Tech Streaming Server V100R001


    S->C: $Data follows
    S->C: $Data follows
    S->C: $Data follows
    S->C: $Data follows
    S->C: $Data follows
    C->S: RTSP/1.0 501 Not Implemented
    S->C: $Data follows
    S->C: $Data follows
    C->S: RTSP/1.0 501 Not Implemented
  

We can see that **VLC** has a better implementation of the **rtsp** protocol, but for some reason it is not stable as **ffmpeg libs (libav)**.

## Sources

  * ONVI-PVR
     
    https://github.com/derzu/ONVIF-PVR
  
  
  * onvif-client

    https://github.com/mberest/onvif-client
    

  * ONVIF Device Manager (Windows)

    https://sourceforge.net/projects/onvifdm/
    
