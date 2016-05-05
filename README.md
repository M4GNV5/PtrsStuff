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

	available(timeout); //check if data can be read

	send(buff, len);
	sends(string);
	sendc(byte);

	recv(buff, len);

	//read bytes into buff until 'end' byte is read or 'max' bytes were read
	//returns count of bytes read
	read(buff, max, end);
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

## Scripts
- `ncchat.ptrs`: Chat server you can connect to using netcat
- `rconcat.ptrs`: Rcon client for connecting to e.g. Minecraft servers
- `test.ptrs`: small test script for console, json and network libraries
