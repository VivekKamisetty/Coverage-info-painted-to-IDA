#  Coverage info painted to IDA

Their are situations where we are fed up of finding the control flow of the binary, so this idea has evoked at a similar situation. First we have to get the trace of the instructions of the binary, for this their are many ways to fetch the instructions using pintool or r2.

I have used gdb to get the trace of instructions by breaking at the address intended to debug and continue stepping into next instruction while the stdout will be logged into the file logfile.

```
import gdb

gdb.execute("file a.out")
gdb.execute("set disassembly-flavor intel")
gdb.execute("set pagination off")
gdb.execute("set logging file logfile")
gdb.execute("set logging on")

gdb.execute("b*main")
gdb.execute("r")
while(1):
gdb.execute("ni")
gdb.execute("x/s $rip")

```
Here the logfile contains the stdout of file a.out. So I only want the list of instructions, so I wrote a python script to fetch the instuctions alone and will write to another file instruction.

```
log = open("logfile","r")
read_logfile = log.readlines()
addr = open("instructions","w")

for i in read_logfile:
    for j in i.split():
        if '0x' in j:
            try:
                int(j.strip(':'),16)
            except:
                continue
            addr.write(j.strip(':') + '\r\n')
```
Finally we are having the all the addresses in our file instructions. By exporting the address to ida using the IDApython script(below) we can colour the instructions in IDA.

```
from idautils import *
from idc import *

f = open("shit","r")
addr = f.readlines()

for i in addr:
        SetColor(int(i,16), CIC_ITEM, 0x98ff000)
```
So our control flow is ready, Now we can only focus on the coloured instructions.

![CAP](https://github.com/VivekKamisetty/Coverage-info-painted-to-IDA/blob/master/CAP.png)
