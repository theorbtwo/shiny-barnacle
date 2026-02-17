Some time ago, I acquired two cisco ip phones from the london hackspace.  They are 7940s, *extremely* similar to 7960s.
I was hoping that I could do something intersting with them, and have so far (spoiler alert)... failed.

There's an unpopulated header I thought might be JTAG.  I populated it, and I don't think it is, it's various voltages 
on one side and a bunch of grounds on the other.  On JTAG, I'd expect everything to be either ground or one single 
voltage, probably 3.3v.  This seems like it's for testing the power supplies.

There's a documented serial console on the rj11 port marked AUX.  See https://www.cisco.com/c/en/us/support/docs/collaboration-endpoints/unified-ip-phone-7900-series/212061-How-To-Make-A-Cisco-IP-Phone-Console-Cab.html and https://www.cisco.com/c/en/us/support/docs/interfaces-modules/port-adapters/200084-How-To-Custom-Make-Cisco-IP-Phone-Consol.html.  
I checked this with my oscillicsope, and nothing ever comes out of it.

I found, with a fair chunk of looking, current firmware for it.  It took some doing, but I managed to Ghidra out a fair chunk of the one firmware file that is actually readable, and ... didn't find anything
terribly interesting.  There's a console you can telnet into, and commands... but I didn't find any undocumented commands.  It seems like there might be a memdump command, but it's not actually wired up -- nothing
adds it to the list of commands, so you can't actually call it.

The firmware file I could make sense of is `P0S-8-12-00.sb2`.  I loaded (after some checking) at an offset of 0x2fe6c, which makes the code for several "show" commands line up properly.  I'm not sure this is actually "correct" in any meaningful sense, but it does
make some stuff line up nicely.  I think it might be *more* correct to say that there is a header of `0x30000-0x2fe6c = 0x194` bytes prior to the payload, which is arm and thumb 32-bit le code (mostly thumb).  This offset makes a set of ARM branch instructions starting at address 0x3000, which is a good sign, generally speaking.
It also means that those instructions mostly point into address space that we don't have, which is a bad sign.  The file seems to use padding of `c0 0x46`.

Notable structures I've found include something I've been calling `fancy_str`.  It's not a very good name.  A fancy_str is a dword length, followed by a dword pointer to a destination.  Following that is `length` bytes of payload, which is copied during boot
to `destination`.  Following the payload is up to 3 bytes of padding to the next 4-byte boundry, and another fancy_str, terminating when a length of 0 is reached.

The other notable structure is called `struct_1_shifted`, which is an actively bad name.  This looks something like...
```
0x0	0x4	char *	name
0x4	0x2	word	type
0x6	0x2	word	attr
0x8	0x4	pointer	data
0xc	0x4	dword	data_len
0x10	0x4	dword	
0x14	0x4	pointer	parse
0x18	0x4	pointer	func_18
0x1c	0x4	pointer	func_1c
0x20	0x4	pointer	cycle_info
0x24	0x4	char*	default
```

This represents a config setting, and is represented by a zero-based id outside the config system mostly.  The "cycle_info" is more of an enum thing, but debug strings refer to it as a cycle, so I've followed suit.  Attr=0 will make config_get_string_with_attr return an attribute of 2 if the global config lock is on and 0 if it is not.
These start at `0x000cc5d0`.  Note that this is inside a `fancy_str` -- the static copies are at `000cc5d0`, the runtime copies are at `0047be00`.

`parse` is a pointer to a function `int config_parse_X(struct_1_shifted *param_1,char *param_2)`.  It should parse `char *` and stick it in the data member, returning 1 on error and 0 otherwise.
