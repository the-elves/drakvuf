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

####Format of the input file
`
{
  "calls": [
			{
				"module-name": "DummyDriver.sys",
				"module-rekall-profile": "/home/illuminati/College/gsoc19/pdbs/DummyDriver/DummyDriver.rekall.json",
        "function-name": "add1",
        "arguments": [
          {
            "type": "INTEGER",
            "value": 20000
          }
        ]
    } ,
    {  
      "module-rekall-profile": "/home/illuminati/College/gsoc19/pdbs/DummyDriver/DummyDriver.rekall.json",
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
`
