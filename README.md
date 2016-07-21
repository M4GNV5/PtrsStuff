# PtrsStuff

Various scripts and libraries written in [PointerScript](https://github.com/M4GNV5/PointerScript)

## Scripts
- `ncchat.ptrs`: Chat server you can connect to using netcat
- `rconcat.ptrs`: Rcon client for connecting to e.g. Minecraft servers
- `test.ptrs`: small test script for console, json and network libraries
- `ircbot.ptrs`: A very basic IRC Bot that can advertise himself
- `munchkin.ptrs`: IRC bot to play Munchkin Loot Letters

## Libraries
- `map.ptrs`:
```C
struct Map
{
	//returns a pointer to a value that can be set/read
	operator this.key;
	operator this[key];
	get(key);

	has(key); //check if a key exists in the map
	remove(key); //removes a key from the map
}
```

- `http.ptrs`
```C
struct HttpResponse
{
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
	connect(host, port);
	disconnect();

	//returns the integer representation of the ip
	//writes the string representation of the ip to buff optionally including the port
	getIp(buff, includePort);
	getPort();

	available(timeout); //check if data can be read

	send(buff, len);
	sends(string);
	sendc(byte);

	recv(buff, len);

	readc(); //receive one byte

	//read bytes into buff until 'end' byte is read or 'max' bytes were read
	//returns count of bytes read
	read(buff, max, end);

	//read bytes into buff until no data is available or max bytes were read
	//returns count of bytes read
	readAvailable(buff, max);
}

struct SocketServer
{
	constructor(port); //starts listening on port 'port'

	//checks if client is available every 'checkInterval' microseconds
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
