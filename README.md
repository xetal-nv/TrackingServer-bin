# TrackingServer


This is the main core software of Xetal Kinsei Technology.


## Main Features


-People localization with accuracy up to 30cm

-Configurable Thermal Map

-Probability map

-People Counting

-Supports any room shape and up to 10 kinsei sensors

-Online on the fly tuning

-Socket tcp Interfaces (tuning and data )


## Configuration File Details


The Server configuration file is in TrackingServer.conf


    ONLINE =0|1

If 0 the server uses data.raw as values otherwise if 1 uses the sensor values in realtime

In order to use the offline mode data.raw should be present in the execution folder



    CHANNEL=1....64

It indicates the wireless channel used

Not needed if the sensors are wired (Sound Bar ...)


    THERMALMAP=0|1

If 1 the TrackingServer runs the algorithms to estimate the thermal map for the given room


    SENSOR_SAMPLING= (>=100ms)

It is the sensors sampling period, if not present 330ms will be used


    SAMPLES_AVERAGE= (>=1)

Used to reduce the sensors noise avaraging the values

The internal update period is SENSOR_SAMPLING * SAMPLES_AVERAGE


    COMPORT= ...

Specifies the serial port where the host sensor is connected.

For example /dev/tty.. in linux or COMx(BUG:no more than 9) in Windows

If changed to a wrong one the system will stop to work


    SERVERPORT=2005

It is Network Port where the server listen waiting for data clients 

Most of the clients connect to 2005 by default, change it only if you know what you are doing


    CONFIG_SERVERPORT=6666

It is Network Port where the server listen waiting for tuning client 

Most of the clients connect to 6666 by default, change it only if you know what you are doing


    MONITORED_AREA=corners*x:y*x:y*..

Specifies the monitored area as a polygon on the cartesian plane

It is usually the room monitored


    PZONE=corners*x:y*x:y*..

Specifies the entry/exit zone as a polygon on the cartesian plane

This is a special part of the monitored area where persons are supposed to enter and exit the virtual #room


    CZONE=x:y*radius

Specifies the entry/exit zone as a circle on the cartesian plane

This is a special part of the monitored area where persons are supposed to enter and exit the virtual #room


    NOTMONITORED_PZONE=corners*x:y*x:y*..

Specifies the not monitored area as a polygon on the cartesian plane


    NOTMONITORED_CZONE=x:y*radius

Specifies the not monitored area as a circle on the cartesian plane


    TRES=8

This is a sensor parameter do not change


    TVIEW=5

This is a sensor parameter do not change


    NSENS=n

It indicates the number of sensors to be initialized


    SENSORANGLE_NR=N|R * NSENS

It indicates the direction of the sensors


    SEN=address*x*y*angle

Used to specify the sensor position and physical address

Be carefull changing this 


    BACKGROUND_RESET_DELAY

Specifies the delay in Seconds before a background Reset in case of lost of communication between #sensors and system

Only for wireless sensors, Sound Bar not affected in normal operation


    BACKGROUND_ALFA= 0 .... 1

...


    BACKGROUND_THRESHOLD=0 ... 1

...


    BACKGROUND_TEMPERATURE_THRESHOLD=

...


    FUSION_BACKGROUND_THRESHOLD=

This value indicates the values difference to be considered normal in the fusion

The threshold must be enough high to clean the fusion


    FUSION_BACKGROUND_MAX_TEMP=

Max temperature value allowed for the background of the fusion level


    FUSION_CONSENSUM_FACTOR=0 ... 1 

Higher it is, higher is the priority that is given to positions with higher sensors consensum


    FUSION_THRESHOLD=

The fusion calculated value and the background must have a difference of at least this threshold


    TRACKING_PERSON_MAXSPEED=

Specifies in m/s the maximum speed of a person


    TRACKING_MODE=0|1

If 0 uses a circle with the radius calculated from TRACKING_PERSON_MAXSPEED

If 1 uses a Gaussian Bell calculated from TRACKING_PERSON_MAXSPEED


    TRACKING_CONSENSUM_FACTOR=

In presence of more than one person occlusion is possible and this parameter increase the priority of possitions not occluded


    TRACKING_EDGE_DISTANCE=

Minimum distance of a person from the wall in cm


    TRACKING_MIN_DISTANCE_PERSONS

Minimum distance between persons to be considered two (distance between centrum of a persons)


    TRACKING_MAX_#PERSONS=

Max number of persons to be tracked, it currently works quite accuratly with 1 and 2  and with good results up to 4


    TRACKING_FALLBACK=0|1

If enabled it permit the system to recover from a lost person

Needed at 1 on the KinseiBar where no entry and exit zones are present as default


    TRACKING_LOW_FALLBACK_DELAY=

If the tracker shows a number of persons higher than the counter + TRACKING_FALLBACK_TH_DOWN wait this dalay in ms before a fallback occurs


    TRACKING_HIGH_FALLBACK_DELAY=

If the tracker shows a number of persons lower than the counter + TRACKING_FALLBACK_TH_UP wait this dalay in ms before a fallback occurs


    TRACKING_FALLBACK_TH_UP=

used by TRACKING_HIGH_FALLBACK_DELAY


    TRACKING_FALLBACK_TH_DOWN=float

used by TRACKING_LOW_FALLBACK_DELAY



## Data Interface Details


The data interface is continuously listening on all the ip addresses of the host machine on the port specified inside the TrackingServer.conf file (Default is 2005).

In order to communicate with it, a tcp connection can be used and the following commands (only) will be accepted. Any other command will result in a generic error.

IMPORTANT: all position values must be divided by 10 to get the float number


Command:

    Direction

    command code

    Direction

    message (in bytes)
    
    
Room Size:
    
    Client -> MOCA
    
    0x73 
    
    MOCA -> Client
    
    0x73 Xsize(2) Ysize(2) #corners(1) {corner_X(2) corner_Y(2)}*#corners
    
    
Entry/Exit Zones
    
    Client -> MOCA
    
    0x78 
    
    MOCA -> Client
    
    0x78 #zones(1) {zonetype(1)(Circle = 0, Polygon =1) if circle -> center_X(2) center_Y(2) radius(2) elseif polygon -> #corners [corner_X(2) corner_Y(2)]*#corners}*#zones

    
Number Of Persons (float number)
    
    Client -> MOCA
    
    0x6E
    
    MOCA -> Client
    
    0x6E npersons(1) (this value is an unsigned int divide by 10 to get the float number) 
 
 
Number Of Persons (Fixed number used by the system)
    
    Client -> MOCA
    
    0x6D
    
    MOCA -> Client
    
    0x6D #persons(1) 
	
    
Persons Position (it is independent from the number of persons but if different (fixed one) it will fallback after a while)

    Client -> MOCA
    
    0x64 
    
    MOCA -> Client
    
    0x64 #positions(1) {index(2) X(2) Y(2)}*#positions (when a position is not valid the index will be 0xffff) 

    
Online Check
    
    Client -> MOCA
    
    0x74
    
    MOCA -> Client
    
    0x74 online(1) (1 if online)
    
    
Online Sensors Check
    
    Client -> MOCA
    
    0x7A
    
    MOCA -> Client
    
    0x7A #sensors(1) {online(1) X(2) Y(2)}*#sensors
    
    
Sensors Battery Level
    
    Client -> MOCA
    
    0x76
    
    MOCA -> Client
    
    0x76 #sensors(1) {Voltage(2)}*#sensors //the voltage must be divided by 100
    
    
Fusion Values
    
    Client -> MOCA
    
    0x66
    
    MOCA -> Client
    
    0x66 #cells(1) {index(2) center_x(2) center_y(2) value(1)}*#cells //the value must be divided by 10

    
Pixel Resolution and pixel size for the thermal map
    
    Client -> MOCA
    
    0x1A
    
    MOCA -> Client
    
    0x1A PixelNumber_X PixelNumber_Y PixelSizeInCm

    
Thermal map, pixel temperature (multiplied by 10 and truncated)
    
    Client -> MOCA
    
    0x1B
    
    MOCA -> Client
    
    0x1B #PixelNumber(2) {pixeltemp(2)}*#PixelNumber
    
    
Thermistors temperature (multiplied by 10 and truncated)
    
    Client -> MOCA
    
    0x1C
    
    MOCA -> Client
    
    0x1C #Thermistors(1) {temperature(2)}*#Thermistors
    
    
Error generic
    
    MOCA -> Client     
    
    0x65

    
## Tuning Interface Details

The tuning interface is continuously listening on all the ip addresses of the host machine on the port specified inside the TrackingServer.conf file (Default is 6666).

In order to communicate with it, a tcp connection can be used and the following commands (only) will be accepted. Any other command will result in a generic error.

The tuning interface allows one single client (at a time) to be connected.
 
The protocol is completely custom and it accepts a first byte in order to discern the kind of message followed by other info if required.

The system will answer with a message that starts with the same byte followed by the required info.
 
IMPORTANT:  All values are to be given and are receoved multiplier by a factor 100 and represented over two bytes (unsigned shorts)
Odd commands are used to get variables. Even commands are used to set variables

Command:


    Direction
    
    command code
    
    Direction
    
    message (in bytes)


Reset Background

    Client -> MOCA

    0x00
    
    MOCA -> Client

    0x00

    
Get Background Alfa

    Client -> MOCA
    
    0x01
    
    MOCA -> Client
    
    0x01 0xYY 0xYY

    
Set Background Alfa
    
    Client -> MOCA
    
    0x02 0xYY 0xYY
    
    MOCA -> Client

    0x02 0xYY 0xYY

    
Get Background Threshold
    
    Client -> MOCA
    
    0x03

    MOCA -> Client

    0x03 0xYY 0xYY

    
Set Background Threshold
    
    Client -> MOCA

    0x04 0xYY 0xYY

    MOCA -> Client

    0x04 0xYY 0xYY

    
Get Background Temperature Threshold

    Client -> MOCA

    0x05

    MOCA -> Client

    0x05 0xYY 0xYY


Set Background Temperature Threshold

    Client -> MOCA

    0x06 0xYY 0xYY

    MOCA -> Client

    0x06 0xYY 0xYY


Get Fusion Background Threshold
    
    Client -> MOCA

    0x07

    MOCA -> Client

    0x07 0xYY 0xYY

    
Set Fusion Background Threshold
    
    Client -> MOCA

    0x08 0xYY 0xYY

    MOCA -> Client

    0x08 0xYY 0xYY


Get Fusion Consensum Factor
    
    Client -> MOCA

    0x09

    MOCA -> Client

    0x09 0xYY 0xYY

    
Set Fusion Consensum Factor
    
    Client -> MOCA

    0x10 0xYY 0xYY

    MOCA -> Client

    0x10 0xYY 0xYY

    
Get Fusion Threshold
    
    Client -> MOCA

    0x11

    MOCA -> Client

    0x11 0xYY 0xYY

    
Set Fusion Threshold
    
    Client -> MOCA

    0x12 0xYY 0xYY

    MOCA -> Client

    0x12 0xYY 0xYY

    
Save override
    
    Client -> MOCA

    0xfe

    MOCA -> Client

    0xfe

    
Delete override
    
    Client -> MOCA

    0xff

    MOCA -> Client

    0xff

    
Reset Offset
    
    Client -> MOCA

    0xfd

    MOCA -> Client

    0xfd

## Software update  

To update the firmware follow the procedure below.  
NOTE: Proceed only if you know what you are doing. Contact Xetal in case of doubt.


### Xetal7688
    
    On the xetal7688 board

    ssh root@xetal.....  
    service xetal stop  
    exit  
    
    On the host 

    scp TrackingServer root@xetal...:TrackingServer/.  
    
    On the xetal7688 board

    ssh root@xetal.....  
    service xetal start  
    exit  


### Edison Version

    On the edison board

    ssh root@xetal.....  
    cd /TrackingServer  
    killall TrackingServer && rm TrackingServer  
    exit  
    
    On the host

    scp TrackingServer root@xetal...:TrackingServer/.  
    
