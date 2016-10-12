# PtrsStuff

Various scripts and libraries written in [PointerScript](https://github.com/M4GNV5/PointerScript)

## Scripts
- `ncchat.ptrs`: Chat server you can connect to using netcat
- `rconcat.ptrs`: Rcon client for connecting to e.g. Minecraft servers
- `test.ptrs`: small test script for map, list, json, http and mutex libraries
- `ircbot.ptrs`: A very basic IRC Bot that can advertise himself
- `munchkin.ptrs`: IRC bot to play Munchkin Loot Letters

## Libraries
- `map.ptrs`:
```C
struct Map
{
	//if useThrow is true getValue and remove will throw an exception if the key does not exist
	constructor(useThrow = true);

	get count; //getter returning the count of key/value pairs in the map

	getValue(key); //get a value from the map
	setValue(key, val); //set a value in the map (adding a new entry if it does not exist)
	hasValue(key); //check if a key exists in the map
	remove(key); //removes a key from the map

	operator this.key; //same as getValue(key);
	operator this[key]; //same as getValue(key);
	operator this.key = val; //same as setValue(key, val);
	operator this[key] = val; //same as setValue(key, val);
	operator this.key(args...); //same as getValue(key)(...args);
	operator this[key](args...); //same as getValue(key)(...args);

	operator key in this; //same as hasValue(key);
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
	operator this[index](args...); //get the value at 'index' and call it with 'args'

	operator index in this; //same as 'index >= 0 && index < length'
	operator sizeof this; //same as 'get length'
	operator foreach in this; //iterate over the list (yields value, index, &value)

	operator this => any; //use a list as an input for an algorithm expression
	operator val => this; //use a list as an output for an algorithm expression

	add(values...); //add values to the list
	remove(index); //remove a value from the lsit and return it
	splice(index, removeCount, insert...); //remove and/or add multiple values

	indexOf(value, start = 0, compare = (a, b) -> a == b); //get the first index of a value using a compare function
	lastIndexOf(value, start = 0, compare = (a, b) -> a == b); //get the last index of a value using a compare function

	//create a new List from an array
	static fromArray(buff, length = sizeof buff);
	//write the entries of a list to the array. Returns a malloc'ed array if 'buff' is NULL or undefined
	toArray(buff, max);
}
```

- `http.ptrs`
```C
struct HttpResponse
{
	code; //html status code (200, 404, etc.)
	message; //html status message (OK, Not Found, etc.)
	header; //Map with all received http header
	content; //body string
}

//perform a http GET reqeust, returns a HttpResponse
http_get(url, port = 80);
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

	operator val => this => any; //filter values not matching the pattern

	//test if the regexp matches the specified string
	test(str);

	//much like javascript's String.prototype.match
	//returns an allocated array (or NULL for no matches) that should be freed
	match(str, maxMatches = 16);
}
```

- `network.ptrs`:
```C
struct Socket
{
	//create a new socket connecting to host:port
	//if createSet is false the available() function will not work
	constructor(host, port, createSet = true);
	disconnect();

	//returns the integer representation of the ip
	//writes the string representation of the ip to buff optionally including the port
	getIp(buff, includePort = false);
	//returns the port
	getPort();

	available(timeout = 0); //check if data can be read (timeout in milliseconds)

	send(buff, len);
	sends(string);
	sendc(byte);

	recv(buff, len);

	readc(); //receive one byte

	//read bytes into buff until:
	//	- 'end' byte is read
	//	- 'max' bytes were read
	//	- 'awaitData' is false and no more data is available
	//returns count of bytes read
	read(buff, max, end, awaitData = true);
}

struct SocketServer
{
	constructor(port); //starts listening on port 'port'
	destructor(); //closes the server

	//checks if client is available every 'checkInterval' milliseconds
	//if 'checkInterval' <= 0 and no client is connecting it returns undefined
	accept(checkInterval);
}
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
	constructor(obj); //the obj will be thread-safely wrapped

	//locks the mutex then gets/sets/calls the property of the wrapped object
	operator this.key;
	operator this[key];
	operator this.key = value;
	operator this[key] = value;
	operator this.key(args...);
	operator this[key](args...);
};
```
