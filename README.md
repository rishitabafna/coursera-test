1.
set ns [new Simulator] 
 
#Open trace file 
set nt [open lab1.tr w] 
$ns trace-all $nt 
 
#Open namtrace file 
set nf [open lab1.nam w] 
$ns namtrace-all $nf 
 
            #Create nodes  
            set n0 [$ns node]  
            set n1 [$ns node]  
            set n2 [$ns node]  
            set n3 [$ns node] 
 
#Assign color to the packet 
$ns color 1 Blue 
$ns color 2 Red 
 
#label nodes 
$n0 label "Source/udp0" 
$n1 label "Source/udp1" 
$n2 label "Router" 
$n3 label "Destination/null" 
 
#create links, specify the type, nodes, bandwidth, delay and ARQ algorithm for it 
$ns duplex-link $n0 $n2 10Mb 300ms DropTail 
$ns duplex-link $n1 $n2 10Mb 300ms DropTail 
Computer Network Laboratory (BCS502) V SEM 
Dept. of CS&E, BIT, 2024 – 25 2 
 
 
 
$ns duplex-link $n2 $n3 100Kb 300ms DropTail 
 
#set queue size between the nodes 
$ns queue-limit $n0 $n2 10 
$ns queue-limit $n1 $n2 10 
$ns queue-limit $n2 $n3 5 
 
           #create and attach UDP agent to n0, n1 and Null agent to n3  
           set udp0 [new Agent/UDP] 
           $ns attach-agent $n0 $udp0 
 
set udp1 [new Agent/UDP] 
$ns attach-agent $n1 $udp1 
 
 
set null3 [new Agent/Null] 
$ns attach-agent $n3 $null3 
 
#attach Application cbr to udp 
set cbr0 [new Application/Traffic/CBR] 
$cbr0 attach-agent $udp0 
 
set cbr1 [new Application/Traffic/CBR] 
$cbr1 attach-agent $udp1 
 
#set udp0 packet to red color and udp1 packet to blue color 
$udp0 set class_ 1 
$udp1 set class_ 2 
 
#connect the agents 
$ns connect $udp0 $null3 
$ns connect $udp1 $null3 
 
#set packet size and interval for cbr1 
$cbr1 set packetSize_ 500Mb 
$cbr1 set interval_ 0.005 
 
           #finish procedure  
proc finish { } { 
global ns nf nt 
$ns flush-trace 
exec nam lab1.nam & 
close $nt 
close 
$nf 
exit 0 
 } 
 
$ns at 0.1 "$cbr0 start" 
$ns at 0.1 "$cbr1 start" 
$ns at 10.0 "finish" 
$ns run 
Computer Network Laboratory (BCS502) 
V SEM 
Awk file- 
BEGIN { count=0; 
} 
{ 
} 
if($1=="d") 
count++ 
END{ 
printf("Number of packets dropped is = %d\n",count); 
} 

2.
#create Simulator object 
set ns [new Simulator] 
#open trace file 
set nt [open prac2.tr w] 
$ns trace-all $nt 
#open namtrace file 
set nf [open prac2.nam w]

$ns namtrace-all $nf 
#create nodes 
set n0 [$ns node] 
set n1 [$ns node] 
set n2 [$ns node] 
set n3 [$ns node] 
set n4 [$ns node] 
set n5 [$ns node]

#label nodes 
$n0 label "ping0" 
$n1 label "ping1" 
$n2 label "ping2" 
$n3 label "ping3" 
$n4 label "ping4" 
$n5 label "router”

#create links, specify the type, nodes, bandwidth, delay and ARQ algorithm for it 
$ns duplex-link $n0 $n5 1Mb 10ms DropTail 
$ns duplex-link $n1 $n5 1Mb 10ms DropTail 
$ns duplex-link $n2 $n5 1Mb 10ms DropTail 
$ns duplex-link $n3 $n5 1Mb 10ms DropTail 
$ns duplex-link $n4 $n5 1Mb 10ms DropTail

#set queue length 
$ns queue-limit $n0 $n5 5 
$ns queue-limit $n1 $n5 5 
$ns queue-limit $n2 $n5 2 
$ns queue-limit $n3 $n5 5 
$ns queue-limit $n4 $n5 2

$ns color 2 Red 
$ns color 3 Blue 
$ns color 4 Green 
$ns color 5 Yellow

#define ‘recv’ function for class Agent/Ping 
Agent/Ping instproc recv {from rtt} { 
$self instvar node_ 
puts "node [$node_ id] received ping answer from $from with round-trip time $rtt ms" 
} 
#create ping agent and attach them to node 
set p0 [new Agent/Ping] 
$ns attach-agent $n0 $p0 
$p0 set class_ 1

set p1 [new Agent/Ping] 
$ns attach-agent $n1 $p1 
$p1 set class_ 2 
set p2 [new Agent/Ping] 
$ns attach-agent $n2 $p2 
$p2 set class_ 3 
set p3 [new Agent/Ping] 
$ns attach-agent $n3 $p3
$p3 set class_ 4 
set p4 [new Agent/Ping] 
$ns attach-agent $n4 $p4 
$p4 set class_ 5 
#connect 2 agents 
$ns connect $p2 $p4 
$ns connect $p3 $p4 
#procedure
proc sendPingPacket { } { 
global ns p2 p3

set intervalTime 0.001 
set now [$ns now] 
$ns at [expr $now + $intervalTime] "$p2 send" 
$ns at [expr $now + $intervalTime] "$p3 send" 
$ns at [expr $now + $intervalTime] "sendPingPacket"
} 
proc finish { } { 
global ns nt nf 
$ns flush-trace 
close $nt 
close $nf 
exec nam prac2.nam & 
exit 0 
} 

$ns at 0.1 "sendPingPacket" 
$ns at 2.0 "finish" 
$ns run

3.
#set ns Simulator
set ns [new Simulator]

#define color for data flow
$ns color 1 Blue
$ns color 2 Red

#open trace file
set tracefile1 [open lab3.tr w] set winfile [open winfile w]
$ns trace-all $tracefile1

#open namtrace file
set namfile [open lab3.nam w]
$ns namtrace-all $namfile

#define finish procedure proc finish { } {
global ns tracefile1 namfile
$ns flush-trace close $tracefile1 close $namfile
exec nam lab3.nam & exit 0
 
}

#create 6 nodes set n0 [$ns node] set n1 [$ns node]
 

set n2 [$ns node] set n3 [$ns node] set n4 [$ns node] set n5 [$ns node]

$n1 shape box
#create link  between nodes
$ns duplex-link $n0 $n2 2Mb 10ms DropTail
$ns duplex-link $n1 $n2 2Mb 10ms DropTail
$ns simplex-link $n2 $n3 0.3Mb 100ms DropTail
$ns simplex-link $n3 $n2 0.3Mb 100ms DropTail

set lan [$ns newLan "$n3 $n4 $n5" 0.5Mb 40ms LL Queue/DropTail MAC/802_3]

#give node position
$ns duplex-link-op $n0 $n2 orient right-down
$ns duplex-link-op $n1 $n2 orient right-up
$ns simplex-link-op $n3 $n2 orient left
$ns simplex-link-op $n2 $n3 orient right

#set queue size of link(n2-n3)
$ns queue-limit $n2 $n3 20

#setup tcp connection set tcp [new Agent/TCP]
$ns attach-agent $n0 $tcp
set sink [new Agent/TCPSink]
$ns attach-agent $n4 $sink
$ns connect $tcp $sink
$tcp set fid_ 1
$tcp set packetSize_ 552

#set ftp over tcp connection set ftp [new Application/FTP]
$ftp attach-agent $tcp

#setup a TCP1 connection set tcp1 [new Agent/TCP]
$ns attach-agent $n1 $tcp1
set sink1 [new Agent/TCPSink]
$ns attach-agent $n5 $sink1
$ns connect $tcp1 $sink1
$tcp1 set fid_ 2
$tcp1 set packetSize_ 552

set telnet0 [new Application/Telnet]
$telnet0 attach-agent $tcp1

#title congestion window1
set outfile1 [open congestion1.xg w]
puts $outfile1 "TitleText: Congestion Window-- Source _tcp" puts $outfile1 "xUnitText: Simulation Time(Secs)"
 

puts $outfile1 "yUnitText: Congestion WindowSize"

#title congestion window2
set outfile2 [open congestion2.xg w]
puts $outfile2 "TitleText: Congestion Window-- Source _tcp1" puts $outfile2 "xUnitText: Simulation Time(Secs)"
puts $outfile2 "yUnitText: Congestion WindowSize"

proc plotWindow {tcpSource outfile} { global ns
set time 0.1
set now [$ns now]
set cwnd [$tcpSource set cwnd_] puts $outfile "$now $cwnd"
$ns at [expr $now+$time] "plotWindow $tcpSource $outfile"
}

$ns at 0.1 "plotWindow $tcp $winfile"
$ns at 0.0 "plotWindow $tcp $outfile1"
$ns at 0.1 "plotWindow $tcp1 $outfile2"
$ns at 0.3 "$ftp start"
$ns at 0.5 "$telnet0 start"
$ns at 49.0 "$ftp stop"

$ns at 49.1 "$telnet0 stop"
$ns at 50.0 "finish"

$ns run

4.
import java.util.Scanner;
public class CRC {
public static int n;
public static void main(String[] args)
{
Scanner sc=new Scanner(System.in); 
CRC crc=new CRC();
String copy,rec,code,zero="0000000000000000";
System.out.println("enter the dataword to be sent");
code=sc.nextLine();
n=code.length();
copy=code; 
code+=zero; 
code=crc.divide(code);
System.out.println("dataword="+copy); copy=copy.substring(0,n)+code.substring(n);
System.out.print("CRC="); 
System.out.println(code.substring(n));

System.out.println("transmitted frame is="+copy);
System.out.println("enter received data:"); 
rec=sc.nextLine();
if(zero.equals(crc.divide(rec).substring(n))) 
System.out.println("correct bits received"); 
else
System.out.println("received frame contains one or more error");
sc.close();
}



public String divide(String s)
{
String div="10001000000100001";
int i,j;
char x;
for(i=0;i<n;i++)
{
    x=s.charAt(i);
for(j=0;j<17;j++)
{
if(x=='1')
{
if(s.charAt(i+j)!=div.charAt(j)) s=s.substring(0,i+j)+"1"+s.substring(i+j+1);
 else
s=s.substring(0,i+j)+"0"+s.substring(i+j+1);
}
}
}
return s;
}
}


