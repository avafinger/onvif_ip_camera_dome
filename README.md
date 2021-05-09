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
    

  **PTZ Control**
  
  This is the 4 axis movement, the camera responds at Port 80 with soap+xml.
  
  Grab Movement info:
  
  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
      xmlns:xsd = "http://www.w3.org/2001/XMLSchema">
      <GetMoveOptions xmlns = "http://www.onvif.org/ver20/imaging/wsdl">
       <VideoSourceToken>VideoSource_1</VideoSourceToken>
      </GetMoveOptions>
     </s:Body>
    </s:Envelope>

  *S->C*
  
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <trt:GetProfilesResponse>
       <trt:Profiles token="Profile_1" fixed="true">
        <tt:Name>mainStream</tt:Name>
        <tt:VideoSourceConfiguration token="VideoSourceToken">
         <tt:Name>VideoSourceConfig</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:SourceToken>VideoSource_1</tt:SourceToken>
         <tt:Bounds x="0" y="0" width="1920" height="1080"></tt:Bounds>
        </tt:VideoSourceConfiguration>
        <tt:AudioSourceConfiguration token="AudioSourceConfigToken">
         <tt:Name>AudioSourceConfig</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:SourceToken>AudioSourceChannel1</tt:SourceToken>
        </tt:AudioSourceConfiguration>
        <tt:VideoEncoderConfiguration token="VideoEncoderToken_1">
         <tt:Name>VideoEncoder_1</tt:Name>
         <tt:UseCount>1</tt:UseCount>
         <tt:Encoding>H264</tt:Encoding>
         <tt:Resolution>
          <tt:Width>1920</tt:Width>
          <tt:Height>1080</tt:Height>
         </tt:Resolution>
         <tt:Quality>10.000000</tt:Quality>
         <tt:RateControl>
          <tt:FrameRateLimit>0</tt:FrameRateLimit>
          <tt:EncodingInterval>1</tt:EncodingInterval>
          <tt:BitrateLimit>0</tt:BitrateLimit>
         </tt:RateControl>
         <tt:H264>
          <tt:GovLength>100</tt:GovLength>
          <tt:H264Profile>Baseline</tt:H264Profile>
         </tt:H264>
         <tt:Multicast>
          <tt:Address>
           <tt:Type>IPv4</tt:Type>
           <tt:IPv4Address >224.1.2.3</tt:IPv4Address >
          </tt:Address>
          <tt:Port>10000</tt:Port>
          <tt:TTL>1</tt:TTL>
          <tt:AutoStart>false</tt:AutoStart>
         </tt:Multicast>
         <tt:SessionTimeout>PT5S</tt:SessionTimeout>
        </tt:VideoEncoderConfiguration>
        <tt:PTZConfiguration token="PTZToken">
         <tt:Name>PTZ</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:NodeToken>PTZNODETOKEN</tt:NodeToken>
         <tt:DefaultAbsolutePantTiltPositionSpace>http://www.onvif.org/ver10/tptz/PanTiltSpaces/PositionGenericSpace</tt:DefaultAbsolutePantTiltPositionSpace>
         <tt:DefaultAbsoluteZoomPositionSpace>http://www.onvif.org/ver10/tptz/ZoomSpaces/PositionGenericSpace</tt:DefaultAbsoluteZoomPositionSpace>
         <tt:DefaultRelativePanTiltTranslationSpace>http://www.onvif.org/ver10/tptz/PanTiltSpaces/TranslationGenericSpace</tt:DefaultRelativePanTiltTranslationSpace>
         <tt:DefaultRelativeZoomTranslationSpace>http://www.onvif.org/ver10/tptz/ZoomSpaces/TranslationGenericSpace</tt:DefaultRelativeZoomTranslationSpace>
         <tt:DefaultContinuousPanTiltVelocitySpace>http://www.onvif.org/ver10/tptz/PanTiltSpaces/VelocityGenericSpace</tt:DefaultContinuousPanTiltVelocitySpace>
         <tt:DefaultContinuousZoomVelocitySpace>http://www.onvif.org/ver10/tptz/ZoomSpaces/VelocityGenericSpace</tt:DefaultContinuousZoomVelocitySpace>
         <tt:DefaultPTZSpeed>
          <tt:PanTilt x="0.100000" y="0.100000" space="http://www.onvif.org/ver10/tptz/PanTiltSpaces/GenericSpeedSpace"/>
          <tt:Zoom x="1.000000" space="http://www.onvif.org/ver10/tptz/ZoomSpaces/ZoomGenericSpeedSpace"/>
         </tt:DefaultPTZSpeed>
         <tt:DefaultPTZTimeout>PT1S</tt:DefaultPTZTimeout>
         <tt:PanTiltLimits>
          <tt:Range>
           <tt:URI>http://www.onvif.org/ver10/tptz/PanTiltSpaces/PositionGenericSpace</tt:URI>
           <tt:XRange>
            <tt:Min>-INF</tt:Min>
            <tt:Max>INF</tt:Max>
           </tt:XRange>
           <tt:YRange>
            <tt:Min>-INF</tt:Min>
            <tt:Max>INF</tt:Max>
           </tt:YRange>
          </tt:Range>
         </tt:PanTiltLimits>
         <tt:ZoomLimits>
          <tt:Range>
           <tt:URI>http://www.onvif.org/ver10/tptz/ZoomSpaces/PositionGenericSpace</tt:URI>
           <tt:XRange>
            <tt:Min>-INF</tt:Min>
            <tt:Max>INF</tt:Max>
           </tt:XRange>
          </tt:Range>
         </tt:ZoomLimits>
        </tt:PTZConfiguration>
        <tt:MetadataConfiguration token="MetaDataToken">
         <tt:Name>metaData</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:PTZStatus>
          <tt:Status>true</tt:Status>
          <tt:Position>true</tt:Position>
         </tt:PTZStatus>
         <tt:Analytics>false</tt:Analytics>
         <tt:Multicast>
          <tt:Address>
           <tt:Type>IPv4</tt:Type>
           <tt:IPv4Address>224.1.2.3</tt:IPv4Address>
          </tt:Address>
          <tt:Port>10000</tt:Port>
          <tt:TTL>1</tt:TTL>
          <tt:AutoStart>false</tt:AutoStart>
         </tt:Multicast>
         <tt:SessionTimeout>PT0S</tt:SessionTimeout>
        </tt:MetadataConfiguration>
       </trt:Profiles>
       <trt:Profiles token="Profile_2" fixed="true">
        <tt:Name>subStream</tt:Name>
        <tt:VideoSourceConfiguration token="VideoSourceToken">
         <tt:Name>VideoSourceConfig</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:SourceToken>VideoSource_1</tt:SourceToken>
         <tt:Bounds x="0" y="0" width="1920" height="1080"></tt:Bounds>
        </tt:VideoSourceConfiguration>
        <tt:AudioSourceConfiguration token="AudioSourceConfigToken">
         <tt:Name>AudioSourceConfig</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:SourceToken>AudioSourceChannel1</tt:SourceToken>
        </tt:AudioSourceConfiguration>
        <tt:VideoEncoderConfiguration token="VideoEncoderToken_2">
         <tt:Name>VideoEncoder_2</tt:Name>
         <tt:UseCount>1</tt:UseCount>
         <tt:Encoding>H264</tt:Encoding>
         <tt:Resolution>
          <tt:Width>640</tt:Width>
          <tt:Height>352</tt:Height>
         </tt:Resolution>
         <tt:Quality>10.000000</tt:Quality>
         <tt:RateControl>
          <tt:FrameRateLimit>0</tt:FrameRateLimit>
          <tt:EncodingInterval>1</tt:EncodingInterval>
          <tt:BitrateLimit>0</tt:BitrateLimit>
         </tt:RateControl>
         <tt:H264>
          <tt:GovLength>100</tt:GovLength>
          <tt:H264Profile>Baseline</tt:H264Profile>
         </tt:H264>
         <tt:Multicast>
          <tt:Address>
           <tt:Type>IPv4</tt:Type>
           <tt:IPv4Address >224.1.2.3</tt:IPv4Address >
          </tt:Address>
          <tt:Port>10000</tt:Port>
          <tt:TTL>1</tt:TTL>
          <tt:AutoStart>false</tt:AutoStart>
         </tt:Multicast>
         <tt:SessionTimeout>PT5S</tt:SessionTimeout>
        </tt:VideoEncoderConfiguration>
        <tt:PTZConfiguration token="PTZToken">
         <tt:Name>PTZ</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:NodeToken>PTZNODETOKEN</tt:NodeToken>
         <tt:DefaultAbsolutePantTiltPositionSpace>http://www.onvif.org/ver10/tptz/PanTiltSpaces/PositionGenericSpace</tt:DefaultAbsolutePantTiltPositionSpace>
         <tt:DefaultAbsoluteZoomPositionSpace>http://www.onvif.org/ver10/tptz/ZoomSpaces/PositionGenericSpace</tt:DefaultAbsoluteZoomPositionSpace>
         <tt:DefaultRelativePanTiltTranslationSpace>http://www.onvif.org/ver10/tptz/PanTiltSpaces/TranslationGenericSpace</tt:DefaultRelativePanTiltTranslationSpace>
         <tt:DefaultRelativeZoomTranslationSpace>http://www.onvif.org/ver10/tptz/ZoomSpaces/TranslationGenericSpace</tt:DefaultRelativeZoomTranslationSpace>
         <tt:DefaultContinuousPanTiltVelocitySpace>http://www.onvif.org/ver10/tptz/PanTiltSpaces/VelocityGenericSpace</tt:DefaultContinuousPanTiltVelocitySpace>
         <tt:DefaultContinuousZoomVelocitySpace>http://www.onvif.org/ver10/tptz/ZoomSpaces/VelocityGenericSpace</tt:DefaultContinuousZoomVelocitySpace>
         <tt:DefaultPTZSpeed>
          <tt:PanTilt x="0.100000" y="0.100000" space="http://www.onvif.org/ver10/tptz/PanTiltSpaces/GenericSpeedSpace"/>
          <tt:Zoom x="1.000000" space="http://www.onvif.org/ver10/tptz/ZoomSpaces/ZoomGenericSpeedSpace"/>
         </tt:DefaultPTZSpeed>
         <tt:DefaultPTZTimeout>PT1S</tt:DefaultPTZTimeout>
         <tt:PanTiltLimits>
          <tt:Range>
           <tt:URI>http://www.onvif.org/ver10/tptz/PanTiltSpaces/PositionGenericSpace</tt:URI>
           <tt:XRange>
            <tt:Min>-INF</tt:Min>
            <tt:Max>INF</tt:Max>
           </tt:XRange>
           <tt:YRange>
            <tt:Min>-INF</tt:Min>
            <tt:Max>INF</tt:Max>
           </tt:YRange>
          </tt:Range>
         </tt:PanTiltLimits>
         <tt:ZoomLimits>
          <tt:Range>
           <tt:URI>http://www.onvif.org/ver10/tptz/ZoomSpaces/PositionGenericSpace</tt:URI>
           <tt:XRange>
            <tt:Min>-INF</tt:Min>
            <tt:Max>INF</tt:Max>
           </tt:XRange>
          </tt:Range>
         </tt:ZoomLimits>
        </tt:PTZConfiguration>
        <tt:MetadataConfiguration token="MetaDataToken">
         <tt:Name>metaData</tt:Name>
         <tt:UseCount>2</tt:UseCount>
         <tt:PTZStatus>
          <tt:Status>true</tt:Status>
          <tt:Position>true</tt:Position>
         </tt:PTZStatus>
         <tt:Analytics>false</tt:Analytics>
         <tt:Multicast>
          <tt:Address>
           <tt:Type>IPv4</tt:Type>
           <tt:IPv4Address>224.1.2.3</tt:IPv4Address>
          </tt:Address>
          <tt:Port>10000</tt:Port>
          <tt:TTL>1</tt:TTL>
          <tt:AutoStart>false</tt:AutoStart>
         </tt:Multicast>
         <tt:SessionTimeout>PT0S</tt:SessionTimeout>
        </tt:MetadataConfiguration>
       </trt:Profiles>
      </trt:GetProfilesResponse>
     </env:Body>
    </env:Envelope>

  Now the 4 axis movement in each direction:
  
  **Down**
  
  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
      xmlns:tt="http://www.onvif.org/ver10/schema">
      <tptz:ContinuousMove>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:Velocity>
        <tt:PanTilt x="0.000000" y="-0.200000"/>
        <tt:Zoom x="0.000000"/>
       </tptz:Velocity>
      </tptz:ContinuousMove>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:ContinuousMoveResponse/>
     </env:Body>
    </env:Envelope>


  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl">
      <tptz:Stop>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:PanTilt>true</tptz:PanTilt>
       <tptz:Zoom>false</tptz:Zoom>
      </tptz:Stop>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:StopResponse/>
     </env:Body>
    </env:Envelope>

  **Left**

  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
      xmlns:tt="http://www.onvif.org/ver10/schema">
      <tptz:ContinuousMove>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:Velocity>
        <tt:PanTilt x="-0.200000" y="0.000000"/>
        <tt:Zoom x="0.000000"/>
       </tptz:Velocity>
      </tptz:ContinuousMove>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:ContinuousMoveResponse/>
     </env:Body>
    </env:Envelope>


  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl">
      <tptz:Stop>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:PanTilt>true</tptz:PanTilt>
       <tptz:Zoom>false</tptz:Zoom>
      </tptz:Stop>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:StopResponse/>
     </env:Body>
    </env:Envelope>

  **Down**
  
  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
      xmlns:tt="http://www.onvif.org/ver10/schema">
      <tptz:ContinuousMove>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:Velocity>
        <tt:PanTilt x="0.000000" y="0.200000"/>
        <tt:Zoom x="0.000000"/>
       </tptz:Velocity>
      </tptz:ContinuousMove>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:ContinuousMoveResponse/>
     </env:Body>
    </env:Envelope>


  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl">
      <tptz:Stop>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:PanTilt>true</tptz:PanTilt>
       <tptz:Zoom>false</tptz:Zoom>
      </tptz:Stop>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:StopResponse/>
     </env:Body>
    </env:Envelope>

  **Right**
  
  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
      xmlns:tt="http://www.onvif.org/ver10/schema">
      <tptz:ContinuousMove>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:Velocity>
        <tt:PanTilt x="0.200000" y="0.000000"/>
        <tt:Zoom x="0.000000"/>
       </tptz:Velocity>
      </tptz:ContinuousMove>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:ContinuousMoveResponse/>
     </env:Body>
    </env:Envelope>


  *C->S*

    <s:Envelope
     xmlns:s="http://www.w3.org/2003/05/soap-envelope">
     <s:Body
      xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl">
      <tptz:Stop>
       <tptz:ProfileToken>Profile_1</tptz:ProfileToken>
       <tptz:PanTilt>true</tptz:PanTilt>
       <tptz:Zoom>false</tptz:Zoom>
      </tptz:Stop>
     </s:Body>
    </s:Envelope>


  *S->C*

    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope
     xmlns:env="http://www.w3.org/2003/05/soap-envelope"
     xmlns:soapenc="http://www.w3.org/2003/05/soap-encoding"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:xs="http://www.w3.org/2001/XMLSchema"
     xmlns:tt="http://www.onvif.org/ver10/schema"
     xmlns:tds="http://www.onvif.org/ver10/device/wsdl"
     xmlns:trt="http://www.onvif.org/ver10/media/wsdl"
     xmlns:timg="http://www.onvif.org/ver20/imaging/wsdl"
     xmlns:tev="http://www.onvif.org/ver10/events/wsdl"
     xmlns:tptz="http://www.onvif.org/ver20/ptz/wsdl"
     xmlns:tan="http://www.onvif.org/ver20/analytics/wsdl"
     xmlns:tst="http://www.onvif.org/ver10/storage/wsdl"
     xmlns:ter="http://www.onvif.org/ver10/error"
     xmlns:dn="http://www.onvif.org/ver10/network/wsdl"
     xmlns:tns1="http://www.onvif.org/ver10/topics"
     xmlns:tmd="http://www.onvif.org/ver10/deviceIO/wsdl"
     xmlns:gte="http://www.onvif.org/ver10/gtextension/wsdl"
     xmlns:wsdl="http://schemas.xmlsoap.org/wsdl"
     xmlns:wsoap12="http://schemas.xmlsoap.org/wsdl/soap12"
     xmlns:http="http://schemas.xmlsoap.org/wsdl/http"
     xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
     xmlns:wsadis="http://schemas.xmlsoap.org/ws/2004/08/addressing"
     xmlns:xop="http://www.w3.org/2004/08/xop/include"
     xmlns:wsnt="http://docs.oasis-open.org/wsn/b-2"
     xmlns:wsa="http://www.w3.org/2005/08/addressing"
     xmlns:wstop="http://docs.oasis-open.org/wsn/t-1"
     xmlns:wsrf-bf="http://docs.oasis-open.org/wsrf/bf-2"
     xmlns:wsntw="http://docs.oasis-open.org/wsn/bw-2"
     xmlns:wsrf-rw="http://docs.oasis-open.org/wsrf/rw-2"
     xmlns:wsaw="http://www.w3.org/2006/05/addressing/wsdl"
     xmlns:wsrf-r="http://docs.oasis-open.org/wsrf/r-2"
     xmlns:tnsn="http://www.eventextension.com/2011/event/topics">
     <env:Body>
      <tptz:StopResponse/>
     </env:Body>
    </env:Envelope>


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
    
