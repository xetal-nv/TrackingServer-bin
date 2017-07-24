# TrackingServer-bin

# Firmware update

To update the firmware follow the procedure below. 

Do not't replace TrackingServer.conf present on your device unless you know what you are doing!


# Edison Version


->edison

ssh root@xetal.....

cd /TrackingServer

killall TrackingServer && rm TrackingServer

exit


->host

scp TrackingServer root@xetal...:TrackingServer/.



# Xetal7688


->xetal7688

ssh root@xetal.....

service xetal stop

exit


->host

scp TrackingServer root@xetal...:TrackingServer/.


->xetal7688

ssh root@xetal.....

service xetal start

exit
