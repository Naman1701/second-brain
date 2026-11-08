
# Channel State Information



1.CSI -RS introduction:  

a.what is use 

b.types of CSI:  

i.NZP-CSI-RS,  

ii.CSI-IM 

iii.CSI-SSB 

iv.ZP-CSI-RS 

2.Signal processing of NZP-CSI -RS. 

a.beta, r, wf, wt and a 

b.Block diagram of processing 

c.Resource Mapping in a slot. 

d.Resource Mapping in band. 

e.Examples 

3.CSI-Reports from UE 

a.Types of reports and their relation 

b.frequency scope: wideband vs subband 

c.time restriction concept. 

4.Transmission Periodicity 

a.Aperiodic 

b.Semi-Persistent 

c.Periodic 

d.Relation between CSI-RS and CSI-Report. 

5.Basics of CSI-IM 

6.Basics of CSI-SSB 

7.Basics of ZP-CSI-RS 

8.CSI Framework (RRC structure) 

a.Philosophy of CSI-Framework 

b.PDSCH-Config 

c.CSI-MeasConfig 

d.CSI-Report 

e.Resource-Config 

f.ResourceSet and its types: 

i.NZP-CSI-RS-ResourceSet 

ii.CSI-SSB-ResourceSet 

iii.CSI-IM-ResourceSet 

iv.ZP-CSI-RS-ResourceSet 

g.NZP-CSI-RS-Resource 

h.CSI-SSB-Resource 

i.CSI-IM-Resource 

j.ZP-CSI-RS 

9.Miscellaneous 

a.Code book 

b.QCI 

c.TRS 

 

CSI-RS Channel State Information Reference Signal 

1.Introduction: 

A. Why to Use it 

CSI-RS is not used for only one purpose. The same physical signal is reused for 

several different jobs, and which job it is doing is decided purely by how it is configured : 

●Channel quality measurement – the classic use. The UE measures the channel and 

reports CQI/PMI/RI back to the network. This normally uses a multi-port 

configuration. 

●Beam management – the network sweeps beams and the UE reports which one it 

hears best (L1-RSRP).Time/Frequency tracking (TRS) – a single-port configuration 

used to keep the UE's timing and frequency locked. 

●Interference measurement – done with a companion resource called CSI-IM, 

which is a set of REs where the network deliberately sends nothing. 

B. Types of CSI-RS 

There are in total four types of CSI resource as follows: 

1.NZP-CSI-RS 

2.CSI-IM 

3.CSI-SSB 

4.ZP-CSI-RS 

![[csi_csi_types.png]]

Measuring Interference 

CQI is a statement about signal quality, and quality needs two numbers. How strong the 

wanted signal is, and how much everything else is getting in the way. The framework measures 

those separately, on different resources. 

That is why CSI-ReportConfig has three pointers rather than one. The channel comes 

from resourcesForChannelMeasurement, and interference comes from one of the other two. 

●CSI-IM is a resource where nothing is transmitted, deliberately. The UE measures the received 

power in those REs, and whatever it finds there is by definition not from this cell. Interference and 

noise, measured directly. 

●nzp-CSI-RS-ResourcesForInterference takes the opposite approach. A real signal is transmitted, 

and the UE is told to treat it as interference. This is how a network estimates what a UE would 

suffer if it were paired with another user on the same resources. 

The second one only makes sense for multi user MIMO, and that is exactly what it exists 

for. The network is asking a hypothetical question. If I put somebody else on this beam, what CQI 

would you report then ? Both pointers are optional in the ASN.1. A report configured with neither 

still works, and the UE falls back on measuring interference wherever it can. That is one reason 

CQI from different implementations is not always comparable. 

2.Sequence Processing of NZP-CSI-RS 

2.1.NR CSI is based on Pseudo Random Sequence modulated with QPSK.  

2.2.This sequence is multiplied by specially designed weighting sequence in both time 

domain and frequency domain and than scaled by power scaling factor.  

2.3.Then this sequence is mapped to a set of specific resource elements in Resource 

Grid.  

2.4.All of these process can be summarized as follows:​

​![[csi_signal_generation.png]]

​​​​ 

2.5.note that c_init is function of slot index in a frame and N_id = 

sequenceGenerationConfig or scramblingID. UE has to be in sync with gNB before 

it can use CSI-RS. 

2.6. the location of a in time is l which is the function of l' and l_bar. 

2.7. The location of a in frequency is k which is function of k' and k_bar and n. n tells 

how many PRB above you will see the the PRB in which CSI-RS will be found. k' and 

k_bar tells , in side that PRB where the CSI-RS you will find. 

2.8.m' is function of n, density rho, k' and k_bar. CSI-RS ports are usually more than 1 

i.e X > 1 so you can consider alpha = 2 rho. 

2.9.Since CSI-RS mostly has multi ports, we need Wf and Wt to generate and 

multiplex orthogonal codes for CSI-RS for different different ports. 

2.10.There are four kinds of CodeDivisionMutiplexing called cdm-Typ: 

2.10.1.No CDM 

2.10.2.FD-CDM2 

2.10.3.CDM4 (FD2,TD2) 

2.10.4.CDM8 (FD2,TD4) 

2.11.Before we go into more details, we will have to see an important table. This table 

is root table and gives almost all information which we have discussed and will 

discuss later.​

Tables for Resource Element Mapping 

![[csi_location_within_slot.png]]

A Few Points to note: 

●Second column give the value of X = number of ports so it is clear the max number of 

ports for CSI-RS is 32. 

●SInce usually CSI-RS will have more than one port, the density can be 1 or 0.5. 

●if there is only one port, the code division multiplexing is not required so for port 1 it is 

always NO CDM. 

●FD CDM2 is a set of orthogonal code of length 2 and aligned in frequency domain. 

●CDM4(FD2,TD2) is set of orthogonal code og length 4 where 2 are aligned in ffreq domain 

and 2 are aligned in time domain. 

●CDM8 (FD2,TD4) is orthogonal code of length 8 , 2 in freq domain and 4 in time domain. 

●you get the value of k', l', k_bar and l_bar from the table easily. 

●the number of brackets in k_bar, l_bar column multiplied by cdm-type is your number of 

ports X. 

●See that while k', l' are simple 0,1,2, or 3; the k_bar and l_bar are function of further 

k0,k1,k2 and k3 and l0 and l1. Next we see how to get these k0 to k3 and l0 and l1 values.  

Resource Mapping in a slot and in band: 

➤Two IEs are given in RRC messages: ​

(1) frequencyDomainAllocation , and​

(2) firstOFDMSymbolInTimeDomain and firstOFDMSymbolInTimeDomain2. 

➤k0, k1 and k2 values come from frequencyDomainAllocation, and l0 and l1 come from 

firstOFDMSymbolInTimeDomain / 2. 

➤for frequency Domain Allocation: 

➤based on ports density and CDM type we first identify which row is the configured row 

from network. 

➤Then based on the row number, as below, the frequecnyDomainAllocation changes it s 

bitmap size and their interpretation also changes.​
$$[b_3 ... b_0], k_i = f(i) \qquad for \: row \: 1$$
$$[b_{11} ... b_0], k_i = f(i) \qquad for \: row \: 2$$
$$[b_2 ... b_0], k_i = 4 \times f(i) \qquad for \: row \: 3$$
$$[b_ ... b_0], k_i = 2 \times f(i) \qquad for \: other \: cases$$


➤f(i) is the bit number of the ith bit in the bitmap which is set to one, repeated across 

every 1/density. In other words, from right to left, k0 is the location of the first set 

bit in bitmap.  

➤For example: Suppose for row1, the bitmap give is 0100. ​

A 4-bit bitmap is used for Row 1 configurations: Bits: [b3, b2, b1, b0] = [0, 1, 0, 0].​

From right to left, the second bit (b2) is set to "1" first time, so f(0) = 2,  which corresponds 

to k0 = 2. 

➤The index i goes from 0 to total number of 1s in the bitmap. It can be less than or equal to 

3.​

➤firstOFDMSymbolInTimeDomain l0 in RRC message is simply an integer between 0 to 13 

(as there are always 14 symbols in a slot). Similarly firstOFDMSymbolInTimeDomain2 l1 is 

also simply an integer between 2 to 12, which conveys that 

firstOFDMSymbolInTimeDomain2 has certain restrictions in terms of which symbols it can 

occupy. 

Tables for CDM Sequence Generation 

➤Once we know the k0, k1, k2, k3 and l0, l1. we move to apply wf and wt orthogonal 

sequence on the resource elements across ports. 

➤The row you picked also decides how the ports share those resource elements.  

➤When cdm-Type is anything other than noCDM, several ports sit on the same REs and are 

separated by an orthogonal code instead of by position.  

➤The tables below hold those codes, and there is one table per CDM type.​

​​​​​​​​ ![[csi_scheduletable_example.png]]

➤​

the values of wf(i) and wt(j) comes from a specific table from the specs. For example for 

the case of CDM8, following table is used:​

​ ![[csi_w_orthogonal_seq.png]]

➤What does it mean: in case of row 18 where cdm8 is used, the total ports for CSI_RS are 32. 

which means total 32 freq-time grid exists one for each csi-rs port.  

➤IN each those time-frequency grid the same 8 REs (exact same location in freq and time) 

will be used to fill the 8 values coming from r_l_nsf (m') from the a_k_l (p,u) equation.  

➤However, inside each port , the CSI-RS REs will be multiplied (point wise) with a matrics W 

which will come from kronecker product of wf and wt.  

➤In our 32 ports example, ​

 for the first port: location is k0 l0 and the W is [ [1 1 1 1]; [1 1 1 1 ] ]​

 for the second port: location is k0 l0 and W is [ [-1 -1 -1 -1]; [1 1 1 1] ]​

 ...​

 for the eighth port: location is k0 l0 and W is [[-1 1 1 -1][1 -1 -1 1]]​

for the nineth port: location k1 l0 and W is same as for port first. and so on.. 

➤there is a practical consequence that catches people out. Because CDM spreads a code 

across several REs, the UE can only recover the ports correctly if the channel is roughly 

constant across those REs. That is fine in most conditions, but it is the reason CDM 

patterns are kept small and compact rather than spread far apart. 

➤The above whatever we see is the congiuration of CSI-RS elemetn within a slot. The 

question comes when and how these CSI-Rs REs contained slots are repeated. This is 

question is answered by timing 

EXAMPLES: 

3.CSI-Reports from UE 

What the UE Actually Reports 

The UE receives (or not receives in case of IM) the CSI data and performs the 

measurement and reports the result. This is what is actually in that report, and I find it the 

reassuring part of the topic. The list is much shorter than the configuration machinery 

suggests.​

​![[csi_quantities_table.png]]

Ue does not report the above quantities indivudually. It sends the values in 
 combination of the above quantities. The combinations are also not free. The  reportQuantity field in CSI-ReportConfig is a choice with a fixed list. You pick one whole  combination rather than assembling your own. The reportQuantity has following possibel  values (combinations).  

![[csi_report_quantities_to_use.png]]

 Reporting Granularity - Subband Vs Wideband 

​

There are roughly two options when it comes to reporting based on the following question 

: Does the report value represent the whole frequency? or  only some specific segment of the 

frequency? The report value (measured value) representing the whole frequency band is called 

'Wideband' reporting and the value representing a specific segment of the band is called 

'Subband' reporting. It can be summarized as follows as per 38.214 - 5.2.1.4 

■Wideband : UE report one CQI/PMI report per Codeword for the whole band. 

■Subband : UE report one CQI/PMI report per Codeword for each subband whole band. 

Wideband vs Subband is determined by one or combination of a few RRC parameters as 

summarized below (based on 38.214 - 5.2.1.4) 

![[csi_quantities_rrc_param.png]]

NOTE : a UE is not expected to be configured with pmi-FormatIndicator if codebookType is set to 

'typeII-r16' or 'typeII-PortSelection-r16' (38.214-5.2.1.4) 

When UE perform the report with subband granularity, it should know of the size and location of 

the subbands it need to measure. How these are determined ? It determined partially by 

predefined specification and partially by RRC configuration. 

![[csi_subband_size_bwp.png]]

NOTE : : If the UE is configured with a CSI Reporting Setting for a bandwidth part with fewer than 

24 PRBs, the CSI reporting setting is expected to have a wideband frequency-granularity(38.214-

5.2.1.4). 

NOTE :: You see two values in Subband size column. Then which of the value should be used when 

specifying the subband ? It is determined by another IEsubbandsize in CSI-ReportConfig. If 

subbandsize = value1, the first number is used. If subbandsize = value2, the second number is 

used. 

The number of PRBs indicated by each Bit in the suband IE is determined by the table shown 

above(38.214-Table 5.2.1.4-2). Basically the number of PRB for each bit varies with the bandwidth 

of the BWP. 

![[csi_freq_report_configuration.png]]

For example, consider that your bandwidth part is 72 prb long. and for cqi format indicator you 

have chosen subband then in subbandsize you have chosen value2. so now your subband is 

divided into 72/8 = 9 sets of 8 prbs. csiReportingBand will be equal to subbands9 and in that 9 

bits you can set any of the bit 1 representing that specifuc set of 8 orb been includeed In 

reporting. 

there are more points like timerestriction and ports an qci... we will cover them later. 

4. CSI RS Transmission Timing 

Everything up to here places CSI-RS inside one slot. That leaves the other half of the 

question open : which all slots carry this information. Also, there is one more question in uplink: 

how frequent UE needs to send the CQI-Reports? The answers comprises of three classes of 

Transmission: Aperiodic, semi persistent and periodic which are applicable for both CSI-RS and 

CSI-Reports. ***add meaning of aperiodic, semi persistent and periodic*** 

In RRC message terms, resourceType in CSI-ResourceConfig says how frequent and where 

the reference signal will be transmitted. While the reportConfigType in CSI-ReportConfig says 

how frequent and when the report from UE is expected. Note that they are configured 

independently, but they are not independent. A report cannot be more persistent than the thing it 

measures, which gives the below table its diagonal shape. 
![[csi_persistency_types.png]]
NOTE : the semi-persistent CSI is further catogorised based on in which uplink channel, the report 

is to be sent. therefore semi-persistent CSI (called sp-CSI) have two flavour: sp-CSI-RS-onPUCCH 

and sp-CSI-RS-onPUSCH. 

we will cover the DCI later in the second revision of this chapter. 

Aperiodic 

![[csi_aperiodic_ue_nw.png]]

♢aperiodic CSI-RS timing offset X refers to the time gap between aperiodic CSI-RS 

triggering and aperiodic CSI-RS transmission with regard to the number of slots 

♢aperiodic CSI reporting timing offset Y refers to the time gap between aperiodic CSI 

reporting triggering and aperiodic CSI reporting with regard to the number of slots. 

♢​
![[csi_dci_param_aperiodic.png]]
​

♢NOTE : When you configure 'reportSlotOffsetList', you need to make it sure that the number of the 

elements in reportSlotOffsetList should be same as the number of elements in PUSCH-Config-

>TimeDomainAllocationList as per 38.331 as stated below. 

●reportSlotOffsetList:    Timing offset Y for semi persistent reporting using PUSCH. This 

field lists the allowed offset values. This list must have the same number of entries as 

the pusch-TimeDomainAllocationList in PUSCH-Config. A particular value is indicated in 

DCI. The network indicates in the DCI field of the UL grant, which of the configured report 

slot offsets the UE shall apply. The DCI value 0 corresponds to the first report slot offset 

in this list, the DCI value 1 corresponds to the second report slot offset in this list, and so 

on. The first report is transmitted in slot n+Y, second report in n+Y+P, where P is the 

configured periodicity. Timing offset Y for aperiodic reporting using PUSCH. This field 

lists the allowed offset values. This list must have the same number of entries as the 

pusch-TimeDomainAllocationList in PUSCH-Config. A particular value is indicated in DCI. 

The network indicates in the DCI field of the UL grant, which of the configured report slot 

offsets the UE shall apply. The DCI value 0 corresponds to the first report slot offset in 

this list, the DCI value 1 corresponds to the second report slot offset in this list, and so on 

(see TS 38.214, clause 6.1.2.1). 

Periodic:​

​
![[csi_periodic_ue_nw.png]]


➤A periodic resource answers that with two numbers, a periodicity and an offset, and the 

equation below is how the UE turns them into slot numbers. 

➤CSI Transmission Timing in slot is determined by RRC parameter CSI-

ResourcePeriodicityAndOffset based on following equation. 

➤​

​![[csi_periodic_periodicity.png]]

​

Semi-Persistent​

​
![[csi_semipersistent_ue_nw.png]]
​

The periodicity shown in the diagram is configured by RRC message. Depending on the physical 

channel and report periodicity type, different RRC parameter is used for the periodicity as shown 

in the following table​

​![[csi_report_phy_channel.png]]

Note that reportSlotConfig is IEs name, its type is actually dependent upon which periodicity class 

we are talking about, see below:​

​
![[csi_report_config_type.png]]
​

For class type: periodic and semiPersistentOnPUCCH, the reportSlotConfig is of the type 

CSI-ReportPeriodicityAndOffset, while for semiPersistentOnPUSCH it is ENUM whit different slots 

values and finally for Aperidic class it is reportSlotOffsetList (instead of reportSlotConfig)  

When codebook is enabled, PUCCH can report codebook type I only and PUSCH can report both 

codebook type I and II (as per 38.214 - 5.2.3, 5.2.4). We will discuss about codebook more in the 

second revision of this book. reportSlotOffsetList defines a list (table) of reportslotOffsets, the 

index of the list to be applied for each report instance is decided by "CSI Request" field of DCI 0_1. 

We will see the structure of reportSlotCOnfig, reportSlotOffsetList and DCI_0_1 below.​

​

​

[This below section should belong to Aperiodic section]​

How to configure Aperiodic Report Trigger ? 

In case of Aperiodic report, the report is triggered by DCI only in some case or by the 

combination of MAC CE + DCI in some other case. In theory, it may sound simple.. yes.. just 

triggering itself is simple, but configuring the trigger condition is quite complicated and 

confusing which involves the interplay of many different parts. Following are the components that 

are involved in the trigger configuration and triggering process: 

DCI 0_1 -> CSI request : this specifies the index of Aperiodic Trigger State configured in 

CSI-AeriodicTriggerStateList or codepoint defined in the MAC CE(Aperiodic CSI Trigger State 

Subselection MAC CE). The bit length of this field is specified by CSI-MeasConfig-

>reportTriggerSize. 

What does DCI 0_1 CSI Request indicate: It can indicate the index of the items in CSI-

AperiodicTriggerStateList or the index of the codepoint in MAC CE(Aperiodic CSI Trigger State 

Subselection MAC CE). Simply put, if the bit length of this field is set large enough that can point 

to all the items of CSI-AperiodicTriggerStateList, it directly indicates the item list in the CSI-

AperiodicTriggerStateList. However, if the bit length is not large enough, it points to the codepoint 

index in the MAC CE which defines a subset of CSI-AperiodicTriggerStateList. 

Following is an example showing how DCI 0_1 triggers an aperiodic report. As you see 

here, one CSI request can trigger only one associatedReportConfigInfoList, but 

associatedReportConfigInfoList can be associated with one or more reportConfig which results in 

triggering one or multiple reports. Usually this part of RRC tend to be very long and you would 

need some practice to get a big picture out of a real rrc message. I think the relational diagram 

shown below would help you get the big picture out of real RRC message. 

![[csi_dci_1_0_csi_request.png]]

How long does it take for UE to compute CSI measurement result ? 

When Network triggers CSI Report request, UE is not able to compute the result and report right 

away. UE needs a certain amount of time to perform the measurement and calculate the result. 

The amount of time that UE needs for CSI report is described in 38.214 - 5.4 and following 

illustration is my interpretation of the specification.​

​
![[csi_dci_carrying_csi_request.png]]
​
​
Z, Z' in the above illustration are from following tables. Which table or which columns in the table to be applied is determined by the condition described below each table.​

![[csi_trigger_z_zprime.png]]
​

Basics of CSI-IM 

It is easy to skip past CSI-IM as a minor detail, but it is worth understanding why it has to 

exist at all. Channel quality is not an absolute signal strength – CQI is fundamentally a signal-to-

interference-and-noise quantity. So the UE needs two different measurements : how strong the 

wanted signal is, and how strong everything else is. 

NZP-CSI-RS gives the first one. CSI-IM gives the second, and it does it in an elegantly 

simple way. 

The network reserves a small set of REs and deliberately transmits nothing on them. 

Whatever the UE receives there cannot be from this cell, so by definition it is interference 

plus noise. 

That leads to the single most important practical point about CSI-IM : those REs must genuinely 

be empty.  

If your own cell transmits anything at all in the CSI-IM location – data, a filler signal, anything – 

then the UE measures your own transmission as if it were interference. The reported CQI will still 

look perfectly plausible, it will simply be wrong, and nothing in the system will flag it. (This is 

exactly the situation the ZP-CSI-RS section addresses). 

You will also notice that CSI-IM offers only two patterns (pattern0 and pattern1) rather than the 

large table used for NZP CSI-RS. That is intentional: nothing is being transmitted here, so there 

are no ports and no CDM to describe. All that is needed is a location:  

​
![[csi_csiIM_patter01.png]]
​

CSI-IM-ResourceSet are resources for interference measurement.  

Entry number in csi-IM-ResourceSetList in the CSI-ResourceConfig indicated by csi-IM-

ResourcesForInterference in the CSI-ReportConfig indicated by reportConfigId below 

(value 1 corresponds to the first entry, value 2 to the second entry, and so on). 

The indicated CSI-IM-ResourceSet should have the same number of resources like the NZP-

CSI-RS-ResourceSet indicated in nzp-CSI-RS-ResourcesforChannel. 

In short, this indicates the Entry number in CSI-ResourceConfig(indicated by csi-IM-

ResourcesForInterference of the associated ReportConfig) ->CSI-IM-ResourceSetList 

Resource for Interference Measurement (CSI-IM) 

CSI IM resource is a set of specific resource elements reserved for Interference 

Measurement. This resources are configurable by RRC message. The frequency and time 

domain location is defined in 38.214 - 5.2.2.4 as illustrated below. 

![[csi_csi_im_resource_rrc.png]]

Basics of ZERO POWER RS : ZP-CSI-RS​

-- it is under construction ---​

​

​

Higher Layer Framework of CSI Report and  RRC Parameters for CSI RS 

CSI Report framework is made up of two large parts. One is for the configuration and the 

other one is for Triggering States. Configuration are real data through which a specific 

configuration is defined. But just because a configuration is defined does not mean it is to be 

used. ResourceConfig refers to or points to or are associated a specific configuration as illustrated 

below (based on 38.214 - 5.2.1). Once the configuration is referred then only that is to be used.​

​

It is like C programming, you can defined and instantiate many structures you want, but eventually 

they will be used only when a ResourceConfig will inform UE to use them in specific way by 

pointing at them. Following is an overall description of the framework and the relationships 

among each component: 

●XXX-YYY-Resource : This parameter defines the physical location (i.e, the location of 

physical resource element) of the various CSI related reference signal. There are three 

types of the resources belonging to this category. They are NZP-CSI-RS resource, ZP-CSI-

RS resource, IM Resource.  The Rrc message information element for these resources are 

listed below. 

○PDSCH-Config 

■zp-CSI-RS-ResourceToAddModList -> ZP-CSI-RS-Resource -> CSI-RS-

ResourceMapping 

○CSI-MeasConfig 

■nzp-CSI-RS-ResourceToAddModList -> NZP-CSI-RS-Resource -> CSI-RS-

ResourceMapping 

■csi-IM-ResourceToAddModList -> CSI-IM-Resource 

●ResourceSet : XXX-YYY Resource defines individual resources. But in NR all the individual 

resources should belong to a specific group to be really used. Even if only one resource is 

to be used, it should belong to a group. That group is called as a ResourceSet. The 

ResourceSet is configured in the following RRC. 

■nzp-CSI-RS-ResourceSetToAddModList->NZP-CSI-RS-ResourceSet->nzp-

CSI-RS-Resources 

■csi-SSB-ResourceSetToAddModList -> CSI-SSB-ResourceSet -> SSB-Index 

●CSI ResourceConfig: specifies on what type of reference signal (nzp-CSI-RS-SSB, csi-IM-

Resource) is to be transmitted. It also configures the types of the transmission (periodic, 

aperiodic, semiPersistent). XXX-YYY-Resource and ResourceSet just defines the structure 

of the CSI resources. They don't trigger the transmission of resources. It is CSI 

ResourceConfig that triggers the transmission of the resources. 

●CSI ReportConfig specifies which of CSI ResourceConfig to be used for the measurement. 

It has the mapping table between the measurement type and the corresponding CSI 

ResourceConfig ID.​

​![[csi_resource_request_config.png]]

➤Here is what each level actually decides:​
![[csi_objext_decides.png]]
​ 

RRC structures of the above framework 

1.PDSCH-Config​
![[csi_pdsch_config_rrc.png]]
​ 

2.CSI-MeasConfig 

![[csi_meas_config_rrc.png]]

3.CSI-ReportConfig:​

​![[csi_report_config_rrc.png]]
![[csi_report_config_rrc_2.png]]

​

4.CSI-ResourceConfig​

​ 
![[csi_resource_config_rrc.png]]
​

There are three main resource sets are included:​

		NZP-CSI-RS-ResourceSet,​
		CSI_SSB-ResourceSet,
		CSI-IM-ResourceSet 

5.NZP-CSI-RS-ResourceSet​

​![[csi_nzp_resource_set_rrc.png]]

​​ 

6.CSI-SSB-ResourceSet​

​![[csi_ssb_resourceset_rrc.png]]​

​ 

7.CSI-IM-ResourceSet​

​
![[csi_im_resource_set.png]]
​ 

8.NZP-CSI-RS-Resource and its ingradients​

​​![[csi_nzp_resource_set.png]]
​
![[csi_rs_resource_mapping.png]]
​ 

9.CSI-IM-Resource​
![[csi_im_resource_rrc.png]]
​

10. ZP-CSI-RS-ResourceSet and Resource
![[csi_zp_csi_resource_resourceset.png]]Miscellaneous (To be covered in the second revision) 

1.block diagram of CSI-RS with antenna port mapping 

2.Codebook and its configuration 

3.QCI  

4.TRS 

5.aperiodic triggers and  

6.scheduling of aperiodoc and semiPersistent csi. 

7.format the document.