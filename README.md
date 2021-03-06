# PtrsStuff

Various scripts and libraries written in [PointerScript](https://github.com/M4GNV5/PointerScript)

## Scripts
- `ncchat.ptrs`: Chat server you can connect to using netcat
- `rconcat.ptrs`: [Rcon](https://developer.valvesoftware.com/wiki/Source_RCON_Protocol) client
- `test.ptrs`: small test script for map, list, json, http and mutex libraries
- `ircbot.ptrs`: A very basic IRC Bot that can advertise himself
- `munchkin.ptrs`: IRC bot to play Munchkin Loot Letters

## Libraries
- `map.ptrs`:
```C
struct Map
{
	get count; //getter returning the count of key/value pairs in the map

	remove(key); //removes a key from the map

	operator this.key; //get a value
	operator this[key]; //get a value
	operator this.key = val; //set a value
	operator this[key] = val; //set a value
	operator &this.key; //get the pointer of a value
	operator &this[key]; //get the pointer of a value
	operator this.key(args...); //call a value
	operator this[key](args...); //call a value

	operator key in this; //check if a key exists
	operator sizeof this; //same as 'get count'
	operator foreach in this; //iterate over the map (yields key, value, &value)
}
```

- `list.ptrs`
```C
struct List
{
	get length; //current length of the list

	operator this[index]; //get the value at 'index'
	operator this[index] = value; //set the value at 'index' to 'value'
	operator &this[index]; //get the pointer to the value at 'index'
	operator this[index](args...); //get the value at 'index' and call it with 'args'

	operator index in this; //same as 'index >= 0 && index < length'
	operator sizeof this; //same as 'get length'
	operator foreach in this; //iterate over the list (yields index, value, &value)

	add(values...); //add values to the list
	removeAt(index); //removes value at 'index' and returns it
	remove(value, compare = (a, b) -> a === b); //removes the first occurence of 'value' from the list
	splice(index, removeCount, insert...); //remove and/or add multiple values
	reverse(); //reverses the list

	indexOf(value, start = 0, compare = (a, b) -> a === b); //get the first index of a value using a compare function
	lastIndexOf(value, start = 0, compare = (a, b) -> a === b); //get the last index of a value using a compare function

	//sorts 'toArray(buff, max)' using qsort. returns the array
	sort(buff, max, compare = (a, b) -> a - b);

	//create a new List from an array
	static fromArray(buff, length = sizeof buff);
	//write the entries of a list to the array. If 'buff' is NULL or undefined it returns a malloc'ed array
	toArray(buff, max);
}
```

- `array.ptrs` provides two array classes for C interop
```C
struct StructArray
{
	get memory; //returns the start pointer of the array

	//create a new array of type `typ[]`. `mem` can either be a pointer to memory
	//or an integer. If its an integer a new array `typ[mem]` is created.
	//if `useHeap` is true structs returned by `operator this[index]` are created
	//on the heap using `cast<typ>` otherwise on the stack using `cast_stack<typ>`
	//if deleteMem is true, `mem` will be deleted in the constructor
	constructor(mem, typ, useHeap = false, deleteMem = typeof mem == type<int>);

	operator this[index]; //get the struct at `index`
	operator &this[index]; //get the address to the struct at `index`
	operator index in this; //boundary check
	operator sizeof this; //get the size of the array
	operator foreach in this; //iterate over all structs in the array
};

struct TypedArray
{
	get memory; //returns the start pointer of the array

	//create a new array of type `typ[]`. Typ should be the name of the native type
	//e.g. "long", "single" or "native". `mem` can either be a pointer to memory
	//or an integer. If its an integer a new array `typ[mem]` is created.
	constructor(mem, typ);

	operator this[index]; //get the value at `index`
	operator this[index] = val; //set the value at `index` to `val`
	operator &this[index]; //get the address to the value at `index`
	operator index in this; //boundary check
	operator sizeof this; //get the size of the array
	operator foreach in this; //iterate over all values in the array
};
```

- `arraylist.ptrs`
```C
struct ArrayList
{
	get length; //retrieve current length

	constructor(arraylen = 127); //arraylen specifies the length of single arrays - 1

	operator this[index]; //get the value at 'index'
	operator this[index] = value; //set the value at 'index' to 'value'
	operator &this[index]; //get the pointer to the value at 'index'
	operator this[index](args...); //get the value at 'index' and call it with 'args'

	operator index in this; //same as 'index >= 0 && index < length'
	operator sizeof this; //same as 'get length'
	operator foreach in this; //iterate over the list (yields index, value)

	add(values...); //add values to the list
	splice(index, removeCount); //remove 'removeCount' values at 'index'
}
```

- `curl.ptrs`:
```C
struct HTTP
{
	code; //http response code
	header; //response header Map (from libs/map.ptrs)
	body; //response body string

	//shorthand functions for performing requests
	//return a new 'HTTP' instance
	//'reqHeader' is an optional foreach-able map of request headers
	static GET(url, reqHeader);
	static POST(url, data, reqHeader);
};
```

- `json.ptrs`:
```C
//much like php's json_encode or javascript's JSON.stringify
//returns a malloc'ed string that should be passed to free
json_encode(val);

//much like php's json_decode or javascript's JSON.parse
//parses the json into a Map (see map.ptrs)
json_decode(str);

//destroys nested Maps/Lists generated by json_decode
json_destroy(value);
```

- `regexp.ptrs`:
```C
struct RegExp
{
	constructor(str, ignoreCase, multiLine); //compile regexp string str
	destructor(); //free the regex

	//test if the regexp matches the specified string
	test(str);

	//much like javascript's String.prototype.match
	//returns an allocated array (or NULL for no matches) that should be freed
	match(str, maxMatches = 16);
}
```

- `timeout.ptrs`: Note, this library does not create another thread, instead it
interrupts the current thread and resumes to it when `func` returns.
```js
//executes 'func(arg)' after 'timeout' milliseconds
//returns a 'job' that can be used with 'clearTimeout'
function setTimeout(func, timeout, arg) { ... }

//deletes the timeout 'job' (a value returned by 'setTimeout')
function clearTimeout(job) { ... }

//executes 'func(arg)' every 'interval' milliseconds
//returns a 'job' that can be used with clearInterval
function setInterval(func, interval, arg) { ... }

//deletes the interval 'job' (a value returned by 'setInterval')
function clearInterval(job) { ... }
```

- `socket.ptrs`:
```C
//AF constants (see `man socket` for more information)
//AF_UNSPEC, AF_UNIX, AF_LOCAL, AF_INET, AF_INET6, AF_IPX, AF_NETLINK, AF_X25,
//	AF_AX25, AF_ATMPVC, AF_APPLETALK, AF_PACKET

//SOCK constants (see `man socket` for more information)
//SOCK_STREAM, SOCK_DGRAM, SOCK_RAW, SOCK_RDM, SOCK_SEQPACKET


struct Socket
{
	//connects to 'host':'port' using the first suitable protocol that can be specified
	//with 'family' (one of the AF_ constants) and 'socktype' (one of the SOCK_ constants)
	constructor(host, port, family = AF_UNSPEC, socktype = SOCK_STREAM);

	//closes the connection
	destructor();

	getLocalAddress(buff); //puts the string representation of the local address into 'buff'
	getRemoteAddress(buff); //puts the string representation of the remote address into 'buff'

	//check if data can be read (timeout in milliseconds)
	//negative timeout means infinite waiting
	available(timeout = 0);

	send(buff, len); //send 'len' bytes from 'buff'
	sends(str); //send 'strlen(str)' bytes from 'str'
	sendc(byte); //send one byte

	//receive 'len' bytes, function will not return until 'len' bytes
	//were received or the remote closes the connection
	//returns count of bytes received
	recv(buff, len);

	//receive one byte
	readc();

	//read bytes into buff until:
	//	- 'max' bytes were read
	//	- 'end' byte is read
	//returns count of bytes read
	read(buff, max, end = 0);
}:

struct SocketServer
{
	//starts listening on port 'port'
	//	- 'backlog' specifies the maximum number of pending connections
	//	- 'family' should be one of the AF_* constants
	//	- 'socktype' should be one of the SOCK_* constants
	constructor(port, backlog = 16, family = AF_UNSPEC, socktype = SOCK_STREAM);

	//closes the server
	destructor();

	//puts the string representation of the local address into 'buff'
	getLocalAddress(buff);

	//waits for a pending connections 'timeout' milliseconds (negative means infinite)
	//returns an instance of 'Socket' or undefined if no client connected
	accept(timeout = -1);
};

struct SocketSet
{
	get count; //current length
	get capaticity; //current capaticity (autmatically increased when full and calling 'add')

	constructor(startMax = 16); //'startMax' defines the starting capaticity
	destructor(); //frees up the array of sockets

	operator foreach in this; //iterate over all Socket's and SocketServer's in the set

	add(val); //add a Socket, SocketServer or file descriptor
	remove(value); //remove a value from the set

	check(timeout = -1); //wait for available data on one of the sockets and return that socket
};
```

- `websocket.ptrs`
```C
//type passed to callbacks of a WebSocketServer, do not construct this yourself
struct libwebsock_client
{
	userdata : pointer; //can be used to store a pointer to your own data

	get server; //retrieve the WebSocketServer a client is connected to

	send(text); //send text to the client
	sendBinary(buff, len); //send binary data to the client
	close(); //close the connection to the client
};

struct WebSocketServer
{
	constructor(port = "6060", host = "0.0.0.0"); //construct a new websocket server

	//the following callbacks are called when a client (dis-)connects or sends a message
	//	'client' is of type libwebsock_client
	onopen; //arguments: (client)
	onclose; //arguments: (client)
	onmessage; //arguments: (client, message, len)

	userdata = undefined; //can be used to store a pointer to your own data

	broadcast(msg); //broadcasts msg to all connected clients
	listen(); //starts listening, looping infinitly
};
```

- `rcon.ptrs`:
```C
struct Rcon
{
	error;
	contructor(host, port, password);
	command(cmd);
	end();
}
```

- `mutex.ptrs`
```C
struct MutexWrap
{
	mutex{}; //memory for a pthread_mutex

	//	'obj' will be thread-safely wrapped
	//	if 'allowAddr' is false the &this.key and &this[key] will throw an exception
	constructor(obj, allowAddr = false);

	//locks the mutex then gets/sets/calls the property of the wrapped object
	operator this.key;
	operator this[key];
	operator this.key = value;
	operator this[key] = value;
	operator &this.key; //note: see 'allowAddr' above
	operator &this[key]; //note: see 'allowAddr' above
	operator this.key(args...);
	operator this[key](args...);
};
```
