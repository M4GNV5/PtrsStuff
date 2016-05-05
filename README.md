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

	available(timeout);

	send(buff, len);
	sends(string);
	sendc(byte);

	recv(buff, len);
	read(buff, max, end);
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
- `rconcat.ptrs`: Rcon client for connecting to e.g. Minecraft servers
- `test.ptrs`: small test script for console, json and network libraries
