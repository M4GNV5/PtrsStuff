# PtrsStuff

Various scripts and libraries written in [PointerScript](https://github.com/M4GNV5/PointerScript)

## Libraries
- `console.ptrs`: `print(...)` and `println(...)` much like javascript's console.log
- `json.ptrs`: `json_encode(val)` much like php's `json_encode` or javascript's `JSON.stringify`
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

## Scripts
- `ncchat.ptrs`: Chat server you can connect to using netcat
- `rconcat.ptrs`: Rcon client for connecting to e.g. Minecraft servers
- `test.ptrs`: small test script for console, json and network libraries
