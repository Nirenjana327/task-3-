# Flags BLECTF
1. 12345678901234567890 (Writes data to a specific handle,Reads the current value stored at a given handle)
2. d205303e099ceff44835 (read the given handle using char-read-hnd ; got a hex value , converted it text and got the flag) 
3. 5cd56d74049ae40f442e ( echo -n "BLECTF" | md5sum ) (md5 is like an encryption language . here we are using the above command to find the md5 of the device named "BLECTF".)
4. 2b00042f7481c7b056c4 (first to know which handle has Device Name,i gave the command 'characteristics' and got the listing of all available characteristics .In that the uuid - 0x002A is the one corresponding to device name  and 0x0016 is the corresponding to handle . read that handle and got the flag)
5. 3873c0270763568cf7aa (the first handle said to 'Write anything here'. so using char-write-req wrote a word in the same handle .then when i read the handle again it gave the flag )
6. c55c6314b3db0a6128af (gatttool does its conversation using hex so to write something using gatttool we need to convert the text to hex . so i wrote to the handle and then read the handle and got the flag)
7. 1179080b29f8da16ad66 ( just write hex of 0x07 and then read the handle again and got flag)
8. f8b136d937fad6a2be9f ( just write hex of 0xc9 and then read the handle again and got flag)
9. 933c1fcfa8ed52d2ec05 
   code: 
   #!/bin/bash for i in {0..255};do
   hex=$(printf "%02x" $i)
   gatttool -b EC:E3:34:1A:EB:B6 --char-write-req -a 0x003c -n $hex 
   response=$(gatttool -b EC:E3:34:1A:EB:B6 --char-read -a 0x003c | cut - d: -f2 | xxd -r -p) 
   if [[ "$response" != "Brute force"* ]]; then
   echo"value tried: $hex"
   echo"flag: $response"
   exit 0 
   fi 
   done 
   
   (just run the file and get the flag)
   
10. 6ffcd214ffebdc0d069e 
    code :
    for i in {1..1000};do
       echo "read $i"
       gatttool -b $MAC --char-read -a 0x3e 
    done 
    
    (run the file and get the flag)

11. 5ec3772bcd00cf06d8eb  (gatttool -b EC:E3:34:1A:EB:B6 --char-write-req  -a 0x0040 -n 0100 --listen) 
(Notification don't need acknowledged, so they are faster. Hence, server does not know if the message reach to the client.
Indication need acknowledged to communicated. The client sent a confirmation message back to the server, this way server knows that message reached the client.
CCCD = Client Characteristic Configuration Descriptor
0000	Notifications OFF
0100	Notifications ON
0200	Indications ON 
0300    Notify+indicate ON

12. c7b86dd121848c77c113  (  gatttool -b EC:E3:34:1A:EB:B6 --char-write-req -a 0x0044 -n 0200 --listen)

13. c9457de5fd8cafe349fd (gatttool -b EC:E3:34:1A:EB:B6 --char-write-req -a 0x0046 -n 0300 --listen )

14. b6f3a47f207d38e16ffa (gatttool -b EC:E3:34:1A:EB:B6 --char-write-req -a 0x004a -n 0300 --listen )

15. SKIP

16. b1e409e5a4eaf9fe5158 (mtu 444)(MTU = Maximum amount of data that can be send at once. Default MTU size is 23 bytes out of which 20 bytes are only actually used.)

17.d41d8cd98f00b204e980 (char-write-req 0x0050 68656C6C6F[HELLO HEX VALUE] 
char-read-hnd 0x0050
got flag)

18. fc920c68b6006169477b  (char-read-hnd 0x0052 0300 [all 0100 0200 0300 all gives answer])

19. fbb966958f07e4a0cc48 

char-read-hnd 0x0054
Characteristic value/descriptor: 53 6f 20 6d 61 6e 79 20 70 72 6f 70 65 72 74 69 65 73 21 
char-write-req 0x0054 61  (61 means hex of a )
Characteristic value was written successfully
Notification handle = 0x0054 value: 30 37 65 34 61 30 63 63 34 38 
 char-read-hnd 0x0054 
Characteristic value/descriptor: 66 62 62 39 36 36 39 35 38 66 
char-read-hnd 0x0054 0100
Characteristic value/descriptor: 66 62 62 39 36 36 39 35 38 66 
 char-write-req 0x0054 0100
Characteristic value was written successfully
Notification handle = 0x0054 value: 30 37 65 34 61 30 63 63 34 38 

20. d953bfb9846acc2e15ee  (echo -n "@hackgnar" | md5sum | cut -c1-20)

UUID → human-friendly identifier(128 bit). like what the thing is.
Attribute ID (Handle) → machine-friendly address.
Brute force means trying all possible values automatically until the correct one works.
Bluetoothctl is a linux command line tool that helps to control Bluetooth devices
GATT is the way BLE devices offer services to clients(like communication rule)
profiles : they are standardized collections of Services.
services : customized collections of different features (they are just like folders)
characteristics : its the data ( like files) 
ATT (Attribute Protocol) and GATT (Generic Attribute Profile). 
ATT is the wire application protocol. ATT is the basic way BLE devices send and receive data. GATT tells ATT how to organize data into services and characteristics .All devices uses GATT rules. All BLE data is finally sent using ATT commands. (attribute - one data entry that is stored inside BLE Device that we can read and write) . ATT sends the data. GATT tells what the data represents.
