# transaction-manager
Simple transaction manager for json messages

## Instalation

```
npm install --save transaction-manager
```

## Usage example

```
const TransactionManager = require("../index.js");

//Use websocket as transport
var tm = new TransactionManager(websocket);

//Add listeners for commands
tm
	.on("cmd",(cmd)=> {
		console.log("got cmd", cmd.name);
		switch(cmd.name) 
		{
			case "..." :
				cmd.accept(...);
				break;
			case "..." :
				cmd.reject(...);
				break;
		}
	})
	.on("event",(event)=> {
		console.log("got event", event.name);
	});

//Send new command
tm.cmd("cmd_name", cmd_data)
	.then((data) => {
		console.log("command accepted with data", data);
	})
	.reject((data) => {
		console.log("command accepted with data", data);
	});

tm.event("event_name");

```


## Wire protocol
The transaction manager uses a JSON based message format to exchange data between both ends (using WebSockets for example).

The base message will be a json object with a type property:

```
{
	"type" : "cmd"
}
```

There are three different message types allowed in this specification: cmd, response and event.

### Command message
The command message is a json object which type is “cmd”.It is used when the sending side expect a response from the other side as a result of this command.

Appart of the cmd attribute, it also contain the following attributes:

```
{
	"type"   : "cmd",
	"transId": 0,
	"name"   : "command_name",
	"data"   : ...
}
```

- type (String): “cmd”
- transId (Integer) : Unique id for this command transaction
- name (String): Command name
- data  (String | Date | Array | Object) : custom command data

### Response message
The command message is a json object which type is “response”. It is used to reply a command send by the other peer, either accepting or rejecting it and pass some custom data associated to the response.

Apart of the cmd attribute, it also contain the following attributes:

```
{
	"type"    : "response",
	"transId" : 0,
	"accepted": true,
	"data"    : ...
}
```

- type (String): “response”
- transId (Integer) : value from the command this response belongs to
- accepted (Boolean): if the command was successful or not.
- data  (String | Date | Array | Object) : custom response data

For each command, there must be a single response message matching the transaction id of the command.

### Event message
Event messages are used when the sending side does not expect any kind of response back and has a type of “event”. It has the following attributes:

```
{
	"type": "event",
	"name": "event_name",
	"data": ... 
}
```

- type (String): “event”
- name (String): Event name
- data  (String | Date | Array | Object) : custom event data

Note that the event message does not have any transaction id as it is not meant to be acknowledged by the receiving side.

## License

MIT