## Introduction
This project aims at fuzzing operating system drivers and system calls, without presence of an agent or kernel module inside the target. This project builds on [drakvuf](https://drakvuf.com) and the famous American fuzzy lop. Drakvuf provides the functionality to inject a processs into the guest OS (read [libinjector](https://youtu.be/0Dk5RehrA-Q). We extend this to inject arbitrary function calls. We also integrate this with AFL. In all tool takes the list of functions that user wants to inject with sample input and fuzzes the input file to generate new input files.

## Architecture
architecture goes here

### Environment Setup
Fuzzer injects multiple functions and may cause blue screen of death. In such a event we need to restart the VM which reduces speed of fuzzing. To reduce the waiting time during the booting up of VM we save a VM once it starts and restore it when a restart is required. We provide restore_script.sh for this. restore_script.sh requires a saved VM and certain environment variables to be set. Follow the procedure given below to set it up
1. Build drakvuf according to instruction given [here](https://drakvuf.com/)
2. Start a vm and waitfor the OS to boot up completely
```
#xl create win.cfg
```
3. Save the state of VM to file \<vm-save-file\>
```
#xl save <name of vm> <vm-save-file>
```
4. Set environment variable **OS_FUZZ_XEN_SAV_FILE** the complete path of \<vm-save-file\>
```
export OS_FUZZ_XEN_SAV_FILE=<vm-save-file>
```
5. Set environment variable **OS_FUZZ_DOMAIN_SAVED_VOLUME** to the LVM volume you want to name the saved disk state of VM.
```
export OS_FUZZ_DOMAIN_SAVED_VOLUME=<path-to-saved-xen-lvm-volume>
```
6. Set environment variable **OS_FUZZ_DOMAIN_VOLUME** to the LVM drive name that you want. This is the path of disk in win.cfg file
```
export OS_FUZZ_DOMAIN_VOLUME=<path-to-xen-lvm-volume>
```
7. Create a lvm volume with name **$OS_FUZZ_DOMAIN_SAVED_VOLUME**
8. Copy the contents of the **$OS_FUZZ_DOMAIN_VOLUME** to **$OS_FUZZ_DOMAIN_SAVED_VOLUME**
```
#dd if=$OS_FUZZ_DOMAIN_VOLUME of=OS_FUZZ_DOMAIN_SAVED_VOLUME bs=1M
```

### Instructions to Build

2. cd drakvuf 
3. cd AFL and build AFL

  `make`

  `cd ..`
  
3. make two directories at a convinient location *in* and *out* ( Out of the source tree)
4. In *in* copy the sample input file. 
5. Run as following

  `sudo AFL/afl-fuzz -i <path to in> -o <path to out> -t 30000 -m 500 src/afl_injector -d <domain-name> -r <path-to-domain-rekall-profile> -f @@ -i <pid of process to be hijacked>`
  
#### Format of the candidates input file
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
