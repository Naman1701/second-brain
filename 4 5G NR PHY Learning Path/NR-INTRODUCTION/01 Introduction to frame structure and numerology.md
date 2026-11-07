# Frame Structure

![[intro_NR_frame_structure.png]]



![[intro_SCS.png]]


2.4 NR Frequency Planning 

1.At a high level, NR defines frequency ranges (FRs). Currently, there are two FRs defined: The first is FR1 

ranging from 410 – 7125 MHz, and the second is FR2 ranging from 24.25 – 52.6 GHz. 

2.3GPP further defines operating bands in each FR. Attach an example here from the spec. 

3.An operating band is a frequency band associated with a certain set of radio frequency (RF) 

requirements. Bandwidths of different operating bands can vary from several MHz to a few GHz. 

4.Different operators may have different amounts of spectrum within an operating band. To 

accommodate diverse spectrum scenarios while limiting implementation complexity, NR supports a 

range of channel bandwidths from 5 – 400 MHz, where a channel bandwidth refers to the bandwidth 

of an NR carrier.


![[bwp_spectrum_handling.png]]

5.NR defines scalable orthogonal frequency division multiplexing (OFDM) numerologies using subcarrier 

spacing (SCS) of 2𝜇⋅15 kHz (𝜇 = 0, 1, …, 4) [6]. 

6.The number of resource blocks (RBs) that may be configured in a channel bandwidth, known as 

transmission bandwidth configuration, shall meet the specified minimum guardband requirements. 

7.Base station (BS) and user equipment (UE) can support different channel bandwidths.  

8.The UE is made aware of the channel bandwidth of the cell.  

9.In addition to the cell bandwidth the network informs the UE about the position and width of a BWP. 

Loosely speaking, a BWP is a set of contiguous RBs configured inside a channel bandwidth.  

10.Two motivations for the introduction of BWP in NR: ​

(1) To support UE bandwidth adaptation to help reduce device power consumption. A UE may use a 

wide bandwidth when a large amount of data is scheduled, while being active on a narrow bandwidth 

for the remaining time. ​

(2) To support devices of different bandwidth capabilities by configuring the devices with different 

BWPs. A BS may support a very wide channel bandwidth which may not be supported by some UEs. 

BWP provides a mechanism to flexibly assign radio resources such that the signals for a UE are 

confined in a portion of BS channel bandwidth that the UE can support. 

11.