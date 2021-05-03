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
  
  * VLC (video + audio)
  
    Streaming with UDP (Wifi link) did not have good results, link is stable but camera is 20 meters away and there are some concrete walls.
  
        vlc rtsp://user:password@192.168.***.****:554/user=admin_password=52F8MCmn_channel=1_stream=1.sdp?real_stream
    
    For a stable connection use RTP-TCP:
  
        vlc --rtsp-tcp rtsp://user:password@192.168.***.***:554/user=admin_password=52F8MCmn_channel=1_stream=0.sdp?real_stream

  * ONVI-PVR (video)

    Streaming rtsp with tcp:
    
        ./OnvifPVR  rtsp://user:password@192.168.***.***:554/user=admin_password=52F8MCmn_channel=1_stream=1.sdp?real_stream


  * ONVIF Device manager 2.2.250

    Manage and configure the camera on Windows (C#)
   
## Sources

  *  ONVI-PVR
     
     https://github.com/derzu/ONVIF-PVR
  
  
  * onvif-client

    https://github.com/mberest/onvif-client
    

  * ONVIF Device Manager (Windows)

    https://sourceforge.net/projects/onvifdm/
    
