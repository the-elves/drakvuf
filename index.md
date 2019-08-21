### Instructions to Build
1. Build drakvuf as usuall
2. cd AFL and build AFL
  `make`
3. make two directories *in* and *out*
4. In *in* copy the sample input file. 
5. Run as following
  `sudo AFL/afl-fuzz -i <path to in> -o <path to out> -t 30000 -m 500 src/afl_injector -d <domain-name> -r <path-to-domain-rekall-profile> -f @@ -i <pid of process to be hijacked>`
