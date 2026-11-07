# SSB - Synchronisation Signal Block

![[ssb_pbch_ssb.png]]



![[ssb_periodicity.png]]

2.One or multiple SS/PBCH Blocks compose an SS/PBCH set ca. 

3.An SS/PBCH set resides in 5 ms and has 20 ms periodicity. 

4.The maximum number of SS/PBCH Block within an SS/PBCH set (i.e., within 5ms period) is specified to 

be: ​

 4 for frequency ranges up to 3 GHz, ​

 8 for 3 to 6 GHz, or ​

 64 for 6 to 52.6 GHz. 

5.The number of actual transmitted SS/PBCHs is configurable and could be less than the maximum 

number. 

6.For initial downlink access, the synchronization block (SS/PBCH) can be transmitted with a sub-carrier 

spacing (SCS) different than the carrier SCS.  

7.PSS/SSS/PBCH can be transmitted with SCS of 15/30kHz for FR1 and with SCS of 120/240kHz for 

frequency range 2. However, RMSI (Remaining System Information SIB1 ans so forth) is defined only 

for SCS of 15/30kHz for FR1 and SCS of 60/120kHz for FR2. 

8.BCH channel (MIB) has 80 ms periodicity which means the MIB content will be repeted after every 80 

ms. Since once SSPBCH set has 5 ms duration, we have 16 possible of an SSPBCH set.


##  SSref, ARFCN and GSCN

The center of SSB = SSref might not be center of channel BW over which gNB 

transmits. It can locate anywhere in the channel BW.

![[ssb_arfcn_calc.png]]

Since channel raster is small <= 100Khz and channel BW are big in NR, a new raster Global 

Synchronisation Channel Raster is introduced for SSref. GSCN defines the possible frequency of SSref and has a 

relatively bigh channel raster. Look below table.

![[ssb_gscn_calc.png]]


c.Concept of Bandwidth part  = BWP and UEs limitation of transmit BW 

UE can not transmit at full channel BW due to low power low complexity. Therefore 

a concept of BWP is introduced. A channel BW is divided into small parts called BWP. 

The UE transmits and receives from and to one BWP at a time. SSB lies in one BWP. 

However BWP creates issues:​

the index of an RB inside a BWP will not be same as its index when entire channel 

BW is considered. Which index should be used by gNB and UE?​

the numerology itself change the subcarrier and RB index of an abolute frequency. 

One RB can have 100 index when 30 K SPS is used while have 200 index when 15 K 

SPS is used. To remove these problem CRB is introduced. 

d.Concept of CRB and PointA definition 

Common Resource Block is by definition common across all UE and BWP for a 

specific SPS. CRB #0 starts from lowest subcarrier of the channel BW.  Different CRB 

for different SCS but the first subcarrier of first CRB aligns irrespective of SCS and is 

called PointA. 

e.SA mode, total offset to point A from SSref = offsetToPOintA + Kssb



![[ssb_ssb_from_pointA.png]]



![[ssb_kssb.png]]















​

So UE serach for SSref, once proper PSS and SSS and PBCH is decoded, from PBCH, UE gets 

k_ssb and offsetToPointA, Note that due to alignment requirement of SSB with SSref as 

center, the SSB may not be RB aligned. N^SSB_CRB is that CRB inside which SSB block starts. 

K_ssb is offset between N^SSB_CRB and starts of SSB in terms of 15Khz SCS. K_SSB can go 

from 0 to 23 (in 30 Khz SCS, one RB contains 12 * 2 15K subcarrier = 24 but since SSB has to 

be in N^SSB_CRB the least it can take is the upper most subcarrier so k_ssb goes to 0 to 23)​

f.NSA mode, absolute freq of point A and SSref.  ​

3.SSB in time domain. 

a.SSB Burst, its duration and periodicity. 

b.Maximum number of SSB inside an SSB Burst. 

c.Scheduling of SSBs: Case A, Case B and Case C 

4.SSB in spatial domain: 

a.Concept of SSB Beam. 

b.Beam Sweeping. 

c.Link between SSB Beam and PRACH 

5.Generation of PSS 

6.Generation of SSS 

7.Generation of PBCH from BCH transport block. 

8.SSB DMRS generation. 

9.Content of MIB and their meaning 

10.UE procedure till acquiring MIB information.