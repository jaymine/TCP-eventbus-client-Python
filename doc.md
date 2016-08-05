
**Python TCP eventbus client** is the Vert.x TCP eventbus client module for Python. I hope you are familiar with Vert.x TCP eventbus bridge. If not get more details from [here](http://vertx.io/docs/vertx-tcp-eventbus-bridge/java/ "http://vertx.io/docs/vertx-tcp-eventbus-bridge/java/"). 

- [Open project on Github](https://github.com/jaymine/TCP-eventbus-client-Python)
- [Download module](https://pypi.python.org/pypi/vertx-eventbus/)

## Implementation ##

Under Eventbus, 2 threads are running for input and output streams.

![Complete Design](https://raw.githubusercontent.com/jaymine/TCP-eventbus-client-Python/gh-pages/2.png)

TCP eventbus bridge has 2 special features

- LV messages 
- Json schema

![LV Message](https://raw.githubusercontent.com/jaymine/TCP-eventbus-client-Python/gh-pages/3.png)

Every message consists of a json message and the length of the json message. Length of a json message is an unsigned integer. It means 4 bytes. But the module is done that work for you. You just have to focus on the json message.


### Create Eventbus ###

to import eventbus,


	import Eventbus.Eventbus as Eventbus

to create eventbus,

	eb=Eventbus.Eventbus(TimeClient(),'localhost', 7000, 0.1 , 10)
or 

	eb=Eventbus.Eventbus(TimeClient(),'localhost', 7000)

Eventbus constructor takes 5 arguments

- Class Object - where eventbus has been deployed eg: TimeClient()
- Host - default is "localhost"
- Port - default is 7000
- Time Out - Default is 0.1 seconds. Socket is using some kind of polling method to establish the asynchronous environment. Time out is polling time period.
- Time Interval - Default is 10 seconds. More details will be discussed under send messages. 

### Addressing ###

Every eventbus message has an address and the client will handle the message according to its address. An address can be any string.

	eg: "vertx", "pink pig", "x.y.z"

### Handling Messages ###

To handle a message, handlers are used. Basically handlers are functions. A function with 2 arguments.

to implement a handler,

	class TimeClient:
			
	def myHandler(self,message):
		if message != None:
			print(message['body'])


A handler only takes 2 argument 

- self - Its class object
- json message - A json message which is loaded into a dictionary. 
	

### Register Handlers ###

In order to handle messages, Handlers should be registered under an address.

to register a handler,

	eb.registerHandler('Get',TimeClient.myHandler); 

After that any message that comes to the address will be sent to the handler.

### Unregister Handlers ###

to unregister a handler,

	eb.unregisterHandler('Get'); 

### DeliveryOptions ###

DeliveryOption is the class object that handles headers, reply address and time out for each message. 

to import,

	import Eventbus.DeliveryOption as DeliveryOption

to create,

	do=DeliveryOption.DeliveryOption();
	do.addHeader('type','text')
	do.addHeader('size','small')
	do.addReplyAddress('Date')
	do.setTimeInterval(10) 

DeliveryOption.addHeader(key, value) - add headers to message
DeliveryOption.deleteHeader(key, value) - delete headers to message
DeliveryOption.addReplyAddress(address) - add reply address
DeliveryOption.deleteReplyAddress() - delete reply address
DeliveryOption.setTimeInterval(seconds)  - set time interval for replyHandlers.
ReplyHandlers will be discussed under send.

### Send ###

Message can be sent in different ways.

	
	body={'msg':'get time',}
	eb.send('Get',body)
	
	eb.send('Get',body,do)

	eb.send('Get',body,TimeClient.myreplyHandler)

	eb.send('Get',body,do,TimeClient.myreplyHandler)

### ReplyHandlers ###

ReplyHandlers are used to handle reply messages if there is a reply. ReplyHandler waits DeliveryOption.timeInterval or eb.timeInterval seconds for the reply. If it did not come then replyHandler will be called with an error (TIMEOUT ERROR).

ReplyHandlers are functions that take 2 arguments.

	def myreplyHandler(self,error,message):
		if error != None:
			print(error)
		if message != None:
			print(message)

### Publish ###

Messages can be published in 2 ways.

	eb.publish('Get',body)

	eb.publish('Get',body,do)

### Close Connection ###

You must close the connection end of the program. If not program wont close at the end.

	eb.closeConnection(2)

closeConnection(time) - time in seconds. After that connection will be closed.

## Examples ###

You can get examples from Github

* [Simple Example with server code](https://github.com/jaymine/TCP-eventbus-client-Python/tree/master/pythonClient/example/Simple%20Example "client and server code on github")
* [TimeKeeper with server code](https://github.com/jaymine/TCP-eventbus-client-Python/tree/master/pythonClient/example/Time%20Keeper "client and server code on github")

