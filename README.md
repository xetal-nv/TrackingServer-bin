# Firmware update  

To update the firmware follow the procedure below.  
NOTE: Proceed only if you know what you are doing. Contact Xetal in case of doubt.

# Edison Version

On the edison board  
  ssh root@xetal.....  
  cd /TrackingServer  
  killall TrackingServer && rm TrackingServer  
  exit  
  
On the host
  scp TrackingServer root@xetal...:TrackingServer/.  
  
# Xetal7688  
  
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
