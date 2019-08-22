## Introduction
This project aims at fuzzing operating system drivers and system calls, without presence of an agent or kernel module inside the target. This project builds on [drakvuf](https://drakvuf.com) and the famous American fuzzy lop. Drakvuf provides the functionality to inject a processs into the guest OS (read [libinjector](https://youtu.be/0Dk5RehrA-Q). We extend this to inject arbitrary function calls. We also integrate this with AFL. In all tool takes the list of functions that user wants to inject with sample input and fuzzes the input file to generate new input files.

## Architecture
architecture goes here
### Instructions to Build
1. Build drakvuf according to instruction given [here](https://drakvuf.com/)
2. cd drakvuf 
3. cd AFL and build AFL

  `make`

  `cd ..`
  
3. make two directories at a convinient location *in* and *out* ( Out of the source tree)
4. In *in* copy the sample input file. 
5. Run as following

  `sudo AFL/afl-fuzz -i <path to in> -o <path to out> -t 30000 -m 500 src/afl_injector -d <domain-name> -r <path-to-domain-rekall-profile> -f @@ -i <pid of process to be hijacked>`
  
#### Format of the input file
```
{
  "calls": [
	{
		"module-name": "DummyDriver.sys",
		"module-rekall-profile": "<path to rekall profile of module>",
		"function-name": "add1",
		"arguments": [
			{
				"type": "INTEGER",
				"value": 20000
			}
		]
	} ,
	{  
	      	"module-rekall-profile": "<path to rekall profile of module>",
		"function-name": "sub1",
		"module-name": "DummyDriver.sys",				
	      	"arguments": [
			{
		  		"type": "INTEGER",
		  		"value": 10001
			}
	      	]

	}
     
  ]
}
```
