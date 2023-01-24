# angel-broking-websocket-v2.0
stable version of angel broking websocket v2.0
This file is compiled in python 3.10 so onlyy use in the python 3.10 version



from smartapi import SmartConnect
import pyotp,time
from config import  *
from datetime import datetime
import pandas as pd
from SmartWebsocketv2 import SmartWebSocketV2

############################################## Login Process ######################################################

obj=SmartConnect(api_key=apikey)
data = obj.generateSession(username,pwd,pyotp.TOTP(token).now())
AUTH_TOKEN = data['data']['jwtToken']
AUTH_TOKEN=AUTH_TOKEN[7:]
print(AUTH_TOKEN)
refreshToken= data['data']['refreshToken']
FEED_TOKEN=obj.getfeedToken()
res = obj.getProfile(refreshToken)

#######################################################################################################################

############################################## Web Socket For The Order Feed ##########################################

correlation_id = "dft_test1"
action = 1
mode = 3
sws = SmartWebSocketV2(AUTH_TOKEN, apikey, username, FEED_TOKEN)
def on_data(wsapp, msg):
		print(msg)   # here message will come for orde feed , put your logic here and it's runing in the child thread so your main thread is complete free
def on_open(wsapp):
		print("on open")
		sws.subscribe_ord(correlation_id)
def on_error(wsapp, error):
		print("ERROR")
		print(error)
def on_close(wsapp):
		print("Close")
sws.on_open = on_open
sws.on_data = on_data
sws.on_error = on_error
sws.on_close = on_close
threading.Thread(target = sws.connect).start()
print('Control Released')
time.sleep(5)
while True:
		time.sleep(10)
		#Write your logic here and this to keep live your main thread if u are runing websocket in the child thread


##############################################################################################################################

				
