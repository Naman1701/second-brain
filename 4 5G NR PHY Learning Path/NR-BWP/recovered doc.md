Introduction: 

![[bwp_spectrum_handling.png]]

1.NR defines frequency ranges (FRs). Currently, there are two FRs defined: The first is FR1 ranging from 410 – 7125 MHz [2], and the second is FR2 ranging from 24.25 – 52.6 GHz. 

2.3GPP further defines operating bands in each FR. An operating band is a frequency band associated with a certain set of radio frequency (RF) requirements. Different operators may have different amounts of spectrum within an operating band. 

3.To accommodate diverse spectrum scenarios while limiting implementation complexity, NR supports a 

range of channel bandwidths from 5 – 400 MHz, where a channel bandwidth refers to the bandwidth of 

an NR carrier. 

4.The number of resource blocks (RBs) that may be configured in a channel bandwidth, known as 

transmission bandwidth configuration, shall meet the specified minimum guardband requirements. 

5.In all previous technologies, a UE will configured to specific transmission bandwidth. NR introduces a 

concept of BWP where the transmission bandwidth is further parted into segments and UE is configured 

to only a part of the total transmission bandwidth. That part of the total transmission bandwidth is called 

one BandwidthPart. 

6.If the scheduler can give a UE 10 resource blocks out of 273, with less SCS (15Khz), has it not already 

narrowed the bandwidth ?  

7.NO - Scheduling a UE narrowly is not the same as letting the UE operate narrowly. It  still has to keep its 

receiver open across the whole carrier (doing full FFT in Tx and Rx side) 

Motivation 

8.The above UE argument is the original motivation and it is still the clearest one, but by the time the 

feature was finished it was serving several purposes at once. In practice there are four:
9.It is worth adding a word about the network side, because the section above says the purpose is more 

for the UE than for the network - which is true from the radio point of view but understates the 

deployment argument. Without BWP, an operator who wanted to serve narrowband devices on a wide 

carrier would have to deploy a separate narrow carrier for them. BWP means one wide carrier can serve 

everything, from a 100 MHz eMBB handset to a sensor that can only manage a few megahertz. That is a 

real saving in spectrum and in equipment, and it is not a UE benefit at all. 

![[bwp_motivation_table.png]]

BandwidthPart 

10.Definition:  "Bandwidth Part is a contiguous set of physical resource blocks,selected from a contiguous
  
subset of the common resource blocks for a given numerology(u) on a given carrier." 

11."a contiguous set of physical resource blocks" - a BWP cannot have holes in it. You are not allowed to 

build one BWP out of RB 0 to 9 plus RB 50 to 59. 

12."selected from a contiguous subset of the common resource blocks" - this is where the two different 

resource block numbering systems come from. The common resource blocks (CRB) are the absolute 

grid that runs across the whole carrier, and a BWP is carved out of that grid. Inside the BWP, however, the 

resource blocks are renumbered from 0 as physical resource blocks (PRB). So a BWP is fully described by 

just two numbers : where it starts in CRB terms, and how many resource blocks long it is. 

13."for a given numerology (u)" - Each BWP carries its own numerology, i.e, its own subcarrier spacing and 

cyclic prefix. Two BWPs on the same carrier can use 15 kHz and 60 kHz spacing respectively. 

14."on a given carrier" - a BWP belongs to one carrier (one serving cell). If the UE is doing carrier 

aggregation, every serving cell has its own independent set of BWPs and its own active BWP. 

15.BWPs are allowed to overlap, and in real configurations they very often do.

Common Resource Block (CRB) and Point A 

16.Since every BWP has its own numerology and BWPs can overlap also, we need a common reference 

point to locate a specific Physical Resource Block in a channel bandwidth. To achieve this, NR defines a 

pointA. PointA is operator chosen point in frequency lies at or below the lowest subcarrier of 

transmission bandwidth.  

17.Note that UE can not get POintA until it decodes MIB and SIB1. So the UE procedure till SIB1 is 

independent of POintA. 

18.UE finds SSB center during cell search so in MIB and SIB1, network must convey the POintA location to 

UE. For this NR defines CRB of fixed SCS which  In FR1, it has 15 Khz SCS and in FR2 it has 60 Khz SCS. 

Then based on SSB center, the offsettoPOintA is given to UE which is   

19.The two numbers mentioned in point 9 for a BWP - the starting point and the length - They are literally 

what gets signaled to the UE, packed into a single RRC field called locationAndBandwidth.

![[bwp_A_CRB_bwps.png]]


20.The same physical piece of spectrum has more than one name. One resource block is 'CRB 63' in the 

absolute grid, but it may be 'PRB 3' while BWP 1 is active and 'PRB 20' while BWP 2 is active. Which name 

is correct depends entirely on which BWP is active at that moment. 

BWPs Rules 

21.In Downlink:  

a.A UE can be configured with up to four carrier bandwidth parts in DL,  

b.The bandwidth of each BWP should be equal or greater than SS Block BW, but it may 

or may not contain SS Block. 

c.Only one carrier bandwidth part can be active at a given time. 

d.The UE is not expected to receive PDSCH, PDCCH, CSI-RS, or TRS outside an active 

bandwidth part. 

e.Each DL BWP include at least one CORESET with UE Specific Search Space (USS).  

f.In primary carrier, at least one of the configured DL BWPs includes one CORESET with 

common search space (CSS) 

22.In Uplink: 

a.A UE can be configured with up to four carrier bandwidth parts 

b.Only one carrier bandwidth part can be active at a given time. 

c.If a UE is configured with a supplementary uplink 

d.The UE can in addition be configured with up to four carrier bandwidth parts in the 

supplementary uplink
  
e.Only one carrier bandwidth part can be active at a given time 

f.The UE shall not transmit PUSCH or PUCCH outside an active bandwidth part. 

How UE finds POintA 

23.The UE scans the synchronization raster using the global search channels (GSCN). Since the SCS can 

vary, the UE performs blind searches/decodes for candidate SSBs (e.g., 15 kHz or 30 kHz in FR1) based 

on the frequency band specs. 

24.Suppose the network has configured SSB with 15 Khz and BWP#0 as 30 Khz then  

25.The UE finds the 15 kHz SSB and decodes the MIB. Inside MIB, there is a  parameter ssb-SubcarrierOffset 

k_SSB which defines the subcarrier-level frequency offset between the lowest subcarrier of the SSB and 

the boundary of the subcarrier-aligned Common Resource Block (CRB) . k_ssb is in terms of subcarriers 

of 15 Khz SCS in FR1. From this knowledge, UE gets the alignment in CRB grid of numerology u = 0 

(15Khz). Let the first subcarrier of the CRB which contains SSB be denoted by N^SSB_CRB.  

26.Then In MIB, network also tells the SCS for Common meaning the SCS of CORESET#0 via the IE 

subCarrierSpacingCommon. In our example UE knows it is 30 Khz. 

27.Finally, MIB also delivers a compact pdcch-Config IE of 8 bits whose first 4 bits indicate an index of a row 

in a predefined table in spec. there many tables based on the scenarios but for example lets look at first 

table below
![[bwp_coreset0_table.png]]

28.suppose the pdcch-Common indicates to row with index 6 that means UE knows that CORESET#0 is 48 

Rb tall (with SCS 30Khz) and in time domain it is 1 symbol wide and finally the offset between the 

N^SSB_CRB and the starting RB of CORESET #0 is 12 RBs. meaning from N^SSB_CRB if UE goes down 12 

RB (in terms of subcarrierSpacingCommon that is 30 Khz), the UE will find the starting of CORESET#0. 

29.Look at the picture Below:

![[bwp_point_A_CRB.png]]
30.Note that the UE still does not know PointA. 

31.Based on the other configuration in MIB, the UE decodes DCI_1_0 in CIRESET#0 and then finds the SIB1 

based on that DCI content and finally gets offsetToPOintA IE in SIB1 

32.offsetToPointA for a PCell downlink represents the frequency offset between point A and the 

lowest subcarrier of the lowest resource block of the SS/PBCH block used by the UE for initial 

cell selection, expressed in units of resource blocks assuming 15 kHz subcarrier spacing for 

FR1 and 60 kHz subcarrier spacing for FR2;​

33.Now UE knows where is PointA. 

Configuration of various BWPs 

34.Once UE knows pointA, the next thing is to configure various BWPs given by network in SIB1. 

35.In SIB1, Netweok sends IEs like BWP-downlink and BWP-uplink. BWP-downlink have three IEs: BWP-id, 

BPW-downlink-common and BWP-downlink-dedicated. Similarly, BWp-uplink has three elements: BWP-

id, BPW-uplink-common and BWP-uplink-dedicated.  

36.Inside both BPW-downlink-common and BPW-uplink-common, an IE exixts called BWP. BWP contains 

three subIEs: locationAndBandwidth, subcarrierSpacing, cyclicPrefix. 

37.While the subcarrierSpacing and cyclicPrefix is easy to understand, the subIE locationAndBandwidth 

tells the actual size and location of this BWP. 

38.locationAndbandwidth is an integer value that encompasses both location and bandwidth based on the 

below formula:

![[bwp_equation_2.png]]

39.L_RB is size of BWP in terms of RBs.  

40.N^size_BWP = 275 maximum RB possible in NR carrier. note that after half of itaround 137-138 the RIV 

value flip around half. this RIV is sent to UE for each bwp to be configured. 

41.UE calculates back the start and size as follows:

![[bwp_equation_1.png]]

42.RIV can be between 0 to 37949 = sum (1  to 275) = 275*276/2. 

Mapping between nCRB and nPRB 

43.The RB_start that comes out of the RIV is NOT counted from Point A. It is counted from the start of the 

carrier for that subcarrier spacing, and the carrier itself starts at an offset from Point A given by 

offsetToCarrier. So the position of the jth BWP in the common resource block grid is :​

​

N^{start}^{BWP,j}   =   offsetToCarrier   +   RB_start​

44.This N^{start}^{BWP,j} is used below to map nPRB with nCRB  
$$n_{CRB} = n_{PRB} + N^{start}_{BWP,j}$$
45.The above equation only runs in one direction as written. The formula converts PRB 

into CRB. Going the other way is just a subtraction, n(PRB) = n(CRB) - N(start,BWP), 

but that result is only meaningful if the resource block actually falls inside the BWP, 

i.e, if the answer lies between 0 and N(size,BWP) - 1. If it does not, the question 

simply has no answer, because that resource block is not part of the BWP at all.

BWP types: 

46.There are four types of BWPs :  

a.Initial BWP,  

b.firstActiveBWP,  

c.Default BWP and  

d.(regular) BWPs. 

47.These are not four different kinds of object. There is only one kind of object - a bandwidth part - 

and 'initial', 'first active' and 'default' are roles that a bandwidth part plays. 

48.The roles are assigned simply by pointing an ID at a BWP. That means one and the same BWP 

can hold several roles at once : it is perfectly normal for BWP #1 to be the first active BWP and 

the default BWP at the same time, and for BWP #0 to be the initial BWP and the default BWP at 

the same time.

​​​​​ 49
![[bwp_bwps_types_table.png]]

50.Two of the four are actual configurations, and two of them are just pointers. 

51.There is a defaultDownlinkBWP-Id but no defaultUplinkBWP-Id. The 'fall back when idle' behavior 

is defined on the downlink only. In TDD that is not a problem, because a DL BWP and the UL BWP 

with the same ID are paired and switch together anyway; in FDD the uplink simply has no 

inactivity fallback of its own. 

BWP Selection (BWP Switching) 

52.BWP selection (or BWP switching) can be done by four different ways as listed below: 

a.By PDCCH (i.e, DCI) : A specific BWP can be activated by Bandwidth part indicator in DCI 

Format 0_1 (a UL Grant) and DCI Format 1_1 (a DL Schedule) 

b.By the bwp-InactivityTimer : ServingCellConfig.bwp-InactivityTimer 

c.By RRC signalling
c.By RRC signalling 

d.By the MAC entity itself upon initiation of Random Access procedure 

​ 53.
![[bwp_rrc_table.png]]

54.Two of the four are UE decisions. BWP switching is not purely network controlled. The 

UE moves itself on inactivity and on random access, which is why both sides have to 

implement exactly the same rules - if they disagree about which BWP is active, the 

UE is listening in one place while the network transmits in another, and the 

connection is effectively dead until something resets it. 

55.The DCI that switches the BWP is usually also a scheduling DCI. The bandwidth part 

indicator lives inside DCI format 0_1 and 1_1, which are the formats that grant 

uplink and schedule downlink. So the switch is not a separate command - the 

network says "move to BWP 2, and by the way here is your data on BWP 2" in one 

shot. The frequency domain resource assignment in that same DCI is already 

interpreted against the new BWP. 

56.

![[bwp_bwp_swithing.png]]


![[bwp_four_bwps.png]]

58.We will cover BWP switching timer and switching delay later.