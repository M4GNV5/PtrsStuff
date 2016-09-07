# PtrsStuff

Various scripts and libraries written in [PointerScript](https://github.com/M4GNV5/PointerScript)

## Scripts
- `ncchat.ptrs`: Chat server you can connect to using netcat
- `rconcat.ptrs`: Rcon client for connecting to e.g. Minecraft servers
- `test.ptrs`: small test script for map, list, json and http libraries
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
	has(key); //check if a key exists in the map
	remove(key); //removes a key from the map

	operator this.key; //same as getValue(key);
	operator this[key]; //same as getValue(key);
	operator this.key = val; //same as setValue(key, val);
	operator this[key] = val; //same as setValue(key, val);

	operator sizeof this; //same as Map.count
	operator in this; //iterate over the list (yields key, value, &value)
}
```

- `list.ptrs`
```C
struct List
{
	get length; //current length of the list
	operator sizeof this;

	operator this[index]; //get a pointer to the value at index
	operator in this; //iterate over the list (yields value, index, &value)

	add(values...); //add values to the list
	remove(index); //remove a value from the lsit and return it
	splice(index, removeCount, insert...); //remove and/or add multiple values

	indexOf(value, start = 0, compare = (a, b) -> a == b); //get the first index of a value using a compare function
	lastIndexOf(value, start = 0, compare = (a, b) -> a == b); //get the last index of a value using a compare function

	//create a new List from an array. This can be used without an instance e.g. List.fromArray([1, 2, 3]);
	fromArray(buff, length = sizeof(buff));
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
json_encode(val)

//much like php's json_decode or javascript's JSON.parse
//parses the json into a Map (see map.ptrs)
json_decode(str)
```

- `regexp.ptrs`:
```C
struct RegExp
{
	constructor(str, ignoreCase, multiLine); //compile regexp string str
	free(); //free the regex and the struct self
	test(str); //test if the regexp matches the specified string

	//much like javascript's String.prototype.match
	//returns an allocated array (or NULL for no matches) that should be freed
	//maxMatches default value is 16
	match(str, maxMatches);
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
	getIp(buff, includePort);
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

	//checks if client is available every 'checkInterval' milliseconds
	//if 'checkInterval' <= 0 and no client is connecting it returns undefined
	accept(checkInterval);

	close(); //frees all memory including itself
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
