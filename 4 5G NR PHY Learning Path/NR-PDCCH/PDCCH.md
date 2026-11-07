

1.Introduction to PDCCH 

2.Block Diagram of DCI Processing 

3.DCI Types and their usage.  

4.CORESET 

4.1.Common resource pool in time-freqeuncy domain (bitmap and symbols)  

4.2.REG, CCE and Aggregation Level AL 

4.3.Interleaving of physical vs logical resources 

5.Search Space 

5.1.Introduction 

5.2.Periodicity, Offset and Duration 

5.3.relation of search space with a CORESET and a DCI type 

5.4.NPDCCH Candidates 

6.CORESET Zero and SearchSpaceZero 

6.1.What is need 

6.2.MIB IEs for CORESETZero and their meaning 

6.3.UE procedure to detect/ decode sib1 

7.References 



SECTION 1: Introduction to PDCCH 

1.The Physical Downlink Control Channel (PDCCH) plays a central role in 5G NR by delivering 

downlink scheduling assignments, uplink scheduling grants, and special-purpose indications 

such as slot format, preemption, and transmit power control. 

2.The information carried by the PDCCH is referred to as Downlink Control Information (DCI). A DCI 

payload contains scheduling details for uplink or downlink data channels, as well as other control 

commands targeted at a single UE or a group of UEs. 

3.Although a DCI message is relatively small, the PDCCH lacks a dedicated feedback mechanism 

(such as HARQ). Consequently, high channel robustness is critical. Simultaneously, spectral 

efficiency requires that control channels minimize overhead while allowing flexible resource 

utilization, enabling operators to balance resource consumption against channel robustness. 

4.To satisfy these requirements, the PDCCH design accommodates varying channel conditions and 

control signaling demands while maintaining robustness. It achieves this through several core 

concepts: 

a.CORESET (Control Resource Set): A set of physical time-frequency resources allocated 

specifically for control channels. A CORESET serves as a resource pool shared across 

multiple Search Spaces. 

b.Search Space (SS): A logical framework defining when and how a UE monitors control 

information. Multiple Search Spaces can be linked to a single CORESET, sharing its 

underlying physical resources. 

 

c.PDCCH Candidates: A Search Space is further divided into PDCCH candidates—the 

specific positions within the Search Space where a UE attempts to blind-decode DCI 

messages. 

d.Blind Decoding: An individual PDCCH candidate may or may not contain DCI for a 

specific UE. The UE attempts to decode candidates sequentially until a valid Cyclic 

Redundancy Check (CRC) pass is achieved or all PDCCH candidates are exhausted. 

We will look into these concepts in depth. After that we will discuss the DCI signal processing in L1 and 

mapping of modulated symbols on physical resources. 

SECTION 2 DCI Block Diagram 

![[pdcch_dci_processing.png]]


If the size of the DCI format is less than 12 bits, a few zero padding bits will be appended until 

the payload size equals 12 bits [11]. For the DCI payload bits, a 24-bit cyclic redundancy check 

(CRC) is calculated and appended to the payload. The CRC allows the UE to detect the presence 

of errors in the decoded DCI payload bits. After the CRC is attached, the last 16 CRC bits are 

masked with the corresponding identifier, referred to as a radio network temporary identifier 

(RNTI). Using the RNTI mask, the UE can detect the DCI for its unicast data and distinguish sets 

of DCI with different purposes that have the same payload size. The CRC attached bits are then 

interleaved to distribute the CRC bits among the information bits. The interleaver supports a 

maximum input size of 164 bits. This means that DCI without CRC can have at most 140 of 

payload bits. The bits are then encoded by the Polar encoder to protect the DCI against errors 

during transmission. The encoder output is processed using a sub-block interleaver and then 

rate matched to fit the allocated payload resource elements (REs) of the DCI. 

The payload bits of each DCI are separately scrambled by a scrambling sequence generated 

from the length-31 Gold sequence. The scrambling sequence is initialized by the physical layer 

cell identity of the cell or by a UE specific scrambling identity and a UE specific cell RNTI (C-

RNTI). After the scrambled DCI bit sequence is Quadrature Phase Shift Keying (QPSK) 

modulated, the complex-valued modulation symbols are mapped to physical resources in units 

referred to as control channel elements (CCEs). Each CCE consists of six resource element 

groups (REGs), where a REG is defined as one PRB in one OFDM symbol which contains nine REs 

for the PDCCH payload and three demodulation reference signal (DMRS) REs. For each DCI, 1, 2, 

4, 8, or 16 CCEs can be allocated, where the number of CCEs for a DCI is denoted as aggregation 

level (AL). With QPSK modulation, a CCE contains 54 payload REs and therefore can carry 108 

bits. This requires the output size of the rate matching block to be 𝐿 ⋅ 108, where 𝐿 is the 

associated AL. Based on the channel environment and available resources, the BS can adaptively 

choose a proper AL for a DCI to adjust the code rate. 

SECTION 3 DCI TYPES 

![[pdcch_dci_summary_table.png]]​

SECTION 4: CORESET (Control Resource Set) & Resource Mapping 

A Control Resource Set (CORESET) is a designated pool of time-frequency physical resources reserved 

for PDCCH. Before we discuss its size and location which are configured via RRC signaling to meet 

specific network deployment requirements, lets address few important points:​

1.An NR carrier can configure up to 4 BWPs per UE, though only one BWP is active at a time 

2.CORESETs per BWP: Up to 3 CORESETs can be configured per BWP. 

3.Total CORESETs per UE: Maximum 12 CORESETs across all 4 BWPs, identified by 

ControlResourceSetId (0 to 11).  

4.CORESET#0 is a special common CORESET configured via MIB/PBD. 

5.Search Spaces (SS) per BWP: Up to 10 Search Spaces can be configured per BWP. 

6.Total Search Spaces per UE: Maximum 40 Search Spaces across 4 BWPs, identified by 

SearchSpaceId (0 to 39). 

​

2. Time-Frequency Dimensions 

​

Frequency Domain Allocation 

Defined by the RRC L1 parameter frequencyDomainResources (CORESET-freq-dom): 

●Comprises a 45-bit bitmap. 

●Each bit represents a contiguous group of 6 Resource Blocks (RBs) (also known as a Resource 

Block Group, or RBG). 

●One RB (or PRB) comprises of 12 subcarriers and 14 OFDM symbols 

●Grouping starts from PRB 0 of the BWP within which the CORESET is configured. 

●If a bit is set to 1, that 6-RB group is included in the CORESET grid. 

Time Domain Allocation 

●Defined by the RRC parameter duration. 

●This parameters defines how many OFDM symbols are allocated in a slot. 

●Occupies 1, 2, or 3 consecutive OFDM symbols at the beginning of a slot. 

Relationship Between CORESET and Search Spaces 

●Resource Grid Validity: Once configured, the CORESET bitmap defines a physical time-frequency grid that persists across slots.It means bitmap tells which RBs are allocated and duration tells how many OFDM symbols will be reserved as CORESET. NOTE THAT this 
configuration is valid for ALL the slots in a radio frame. These resources are now 
belonging to CORESET which is a shared pool of resources.

![[pdcch_rrc_controlsetZero.png]]​

​
​

●Mapping: A single Search Space set ($s$) is associated with exactly one CORESET ($p$). A 

Search Space (SS) takes resources from a CORESET for a specific purpose. NOTE that a single 

CORESET can be shared by multiple Search Spaces (e.g., both Common and UE-specific Search 

Spaces). 

●Execution: Search Space defines when (slot periodicity, offset, symbol monitoring pattern) the UE 

monitors PDCCH, while CORESET defines where (frequency allocation and symbol duration) in 

physical resources to look. 

●Before we go into search space, we need to understand CORESET more deeply. How the 

resources of a CORESET are manged. 

​

3. Resource Elements: REG, CCE, and Aggregation Levels 

look at section 7.3.2.2 CORESET in TS 38.211  to modify the below section 

Resource Element Group (REG) 

●Definition:   1REG = 1 PRB X 1 OFDM symbol (12 subcarriers X 1 symbol). 

●RE Allocation: Out of 12 Resource Elements (REs) in a REG, 9 REs carry PDCCH data symbols and 

3 REs carry Demodulation Reference Signals (DMRS) for channel estimation. 

Control Channel Element (CCE) 

●Definition: 1 CCE = 6 REGs. 

●Dimensions: Depending on the CORESET time duration (N_{symb}^{CORESET}), a CCE's physical 

footprint varies: 

●1 OFDM Symbol duration:$6\text{ PRBs} \times 1\text{ symbol}$ ($72\text{ subcarriers} \times 

1\text{ symbol}$) 

●2 OFDM Symbol duration:$3\text{ PRBs} \times 2\text{ symbols}$ ($36\text{ subcarriers} \times 

2\text{ symbols}$) 

●3 OFDM Symbol duration:$2\text{ PRBs} \times 3\text{ symbols}$ ($24\text{ subcarriers} \times 

3\text{ symbols}$) 

■ 

Time-First REG Mapping within a CCE 

When $N_{\text{symb}}^{\text{CORESET}} > 1$, REGs forming a CCE are indexed first along the time 

domain, then along the frequency domain. 

●Example (2 OFDM symbols wide): A CCE occupies a 3 PRBs X 2 symbol ​

grid ( 6 REGs total, indexed 0 to $5$). 

● 

■$\text{REG } 0$: PRB 0, Symbol 0$ 

■$\text{REG } 1$: PRB 0, Symbol 1$ 

■$\text{REG } 2$: PRB 1, Symbol 0 

■$\text{REG } 3$: PRB 1, Symbol 1 

■$\text{REG } 4$: PRB 2, Symbol 0 

■$\text{REG } 5$: PRB 2, Symbol 1 

Example Calculation: For a CORESET with $54\text{ PRBs}$ and $2\text{ OFDM symbols}$: 

$$\text{Total REGs} = 54 \times 2 = 108\text{ REGs}$$ 

$$\text{Total CCEs} = \frac{108}{6} = 18\text{ CCEs}\quad (\text{CCE indices } 0 \text{ to } 17)$$ 

​

4. Logical vs. Physical Mapping & Interleaving 

To combat frequency-selective fading and achieve frequency diversity across the wideband channel, 

3GPP defines logical-to-physical resource mapping using REG Bundles and Interleaving. 

REG Bundle ($L$) 

A REG Bundle is a group of contiguous REGs that share the same precoding and channel estimation. The 

REG bundle size $L$ is configured via RRC signaling: 

●Interleaved CORESET:$L \in \{2, 6\}$ (for 1-symbol CORESET, $L \in \{2, 6\}$; for 2/3-symbol 

CORESET, $L$ can be 2, 3, or 6 depending on duration). 

●Non-Interleaved CORESET:$L = 6$ (1 CCE = 1 REG Bundle).​

CCE-to-REG Mapping Modes 

1.Non-Interleaved Mapping: 

○.Logical CCEs are mapped directly to consecutive physical REG bundles. 

○.Suitable when beamforming/precoding is tailored to a specific narrow frequency region 

or channel state. 

2.Interleaved Mapping: 

○.Logical REG bundles are permuted using a block interleaver before being mapped to 

physical REG bundles across the CORESET frequency grid. 

○.Parameterized by Interleaver Size ($R \in \{2, 3, 6\}$) and Shift Value ($n_{\text{shift}}$). 

○.Provides frequency diversity across the entire CORESET bandwidth. 

How to Apply REG Bundles 

1.Define REG Bundles: Partition the total REGs inside the CORESET into contiguous bundles of size 

$L$. 

2.Form Logical CCEs: Group $6/L$ consecutive REG bundles to form one logical CCE. 

3.Interleave (if configured): Pass the bundle indices through the 3GPP block interleaver using $R$ 

and $n_{\text{shift}}$ to obtain the physical REG bundle indices. 

4.Map to Antenna Ports: Transmit each REG bundle using a uniform precoder over all REs in the 

bundle, allowing the UE to perform joint channel estimation per bundle. 

​![[pdcch_candidate_mapping_reg.png]]

​

SECTION 3 SEARCH SPACE: 

1.Recall that a UE already knows the CORESET in which search space is defined. So which PRBs to look inside 

the active BWP is something UE already knows. Also UE knows the duration of CORESET so how many 

OFDM symbols to look in a slot is also known to UE. The question becomes which slots UE should look for 

if it wants to decode a specific DCI type. 

2.A SS tells where to look for specific DCI in time domain in the unit of slots. It tells that for a specific SS s, 

what is the periodicity k, offset o and duration d in terms of SLOTS where a UE should look. Offset o tells 

which slot is starting slot of that serach space from the starting slot 0 in frame 0. duration d tells how 

many continuous slots will be considered for that search space. (note that this duration d is different from 

the duration of CORESET which is 1 2 or 3 OFDM). and at last periodicity k tells after how many slots the 

pattern will repeat. 

![[pdcch_num_cce_required.png]]


3. 

4.A serach space are of two types: CSS (Common Search Space) and USS (User 

Search Space) 

5.A SS set with index s is associated with only one CORESET with index p. On the other 

hand A Control Resource Set (CORESET) can be associated with multiple Search 

Space (SS) sets. While the mapping relationship in the specification defines that 

each individual Search Space set with index s is associated with only one 

CORESET with index p, the reverse is not restricted—multiple different Search 

Space sets can point to and share the same CORESET. 

6.This architecture allows a single CORESET to carry various types of control traffic 

(such as common search spaces for system information or UE-specific search 

spaces for dedicated data scheduling) simultaneously across different time and 

frequency configurations 

7.Search Space Configuration: Each Search Space points to a specific 

controlResourceSetId. Multiple Search Space configurations can link to the exact 

same CORESET ID. 

8.Time-Domain Scheduling (Monitoring Periodicity): 

a.Different Search Spaces associated with the same CORESET can have 

distinct monitoring periodicities, slot offsets, and symbol monitoring 

patterns (defined by parameters like monitoringSlotPeriodicityAndOffset 

and monitoringSymbolsWithinSlot). 

b.For example, SearchSpace 1 (e.g., for SIB1 or paging) might monitor the 

CORESET every 10 ms, while SearchSpace 2 (UE-specific PDCCH) 

monitors the same CORESET in every slot. 

9.Logical Separation by Search Space Type: 

a.A single CORESET can hold both Common Search Space (CSS) 

candidates (monitored by multiple UEs for system information, paging, or 

RACH responses) and UE-Specific Search Space (USS) candidates 

(monitored by individual UEs for user-specific data scheduling). 

10.CCE Aggregation Levels and Candidate Offsets: 

a.Inside the CORESET's total CCE capacity, each Search Space specifies 

how many PDCCH candidates to monitor at various Aggregation Levels 

(AL∈{1,2,4,8,16}). 

b.The starting CCE index for a candidate is determined hashing functions 

(for USS) or fixed rules (for CSS). Because different Search Spaces use 

different parameters or hashing seeds (like Radio Network Temporary 

Identifiers, or RNTIs), their candidate CCE locations within the shared 

CORESET grid are offset from one another. 

11.QUESTION: Assuming a standard subcarrier spacing where 10 ms corresponds 

to 10 slots (slots 0 to 9), slot 9 represents the 10th ms of the frame. For example, 

SearchSpace 1 (e.g., for SIB1 or paging) might monitor the CORESET every 10 ms, while 

SearchSpace 2 (UE-specific PDCCH) monitors the same CORESET in every ALTERNATE slot 

i.e 1,3,5,7,9.​

Following are questions:​

1. In the 9th slot, Will UE decode SS1 and SS2 both?​

2. If the above Yes, will it mean the SS1 and SS2 be mutually exclusive inside the coreset?​

3. what happens to the resource in slot 0,2,4,6,8? Are they still called coreset ?  

a.Yes, the UE will monitor both Search Space 1 (SS1) and Search Space 2 

(SS2) in the 9th slot. Because slot 9 falls on the 10 ms boundary 

(triggering SS1) and is an odd-numbered slot (triggering SS2), both 

monitoring conditions are satisfied simultaneously. 

b.No, SS1 and SS2 do not have to be strictly mutually exclusive inside the 

CORESET, though their PDCCH candidates can overlap depending on 

configuration. 

i.Logical Overlap: The candidate Control Channel Elements (CCEs) 

for SS1 and SS2 are calculated using their respective rules and 

hashing functions. If the network configures them such that their 

candidate CCE indices do not overlap, they remain separate. 

However, if the candidate CCEs overlap, the UE simply monitors 

those CCEs against the expected Downlink Control Information 

(DCI) formats and Radio Network Temporary Identifiers (RNTIs) for 

both search spaces. 

ii.Physical Transmission: If the gNB actually transmits a PDCCH for 

SS1 (e.g., using SI-RNTI) and another PDCCH for SS2 (e.g., using C-

RNTI) in the same slot, it will schedule them on non-overlapping 

CCEs within the shared CORESET to avoid physical collision. 

c.Yes, the physical resources in slots 0, 2, 4, 6, and 8 are still called the 

CORESET. A CORESET defines the physical time-frequency resource 

allocation grid (OFDM symbols and PRB groups). That resource grid exists 

in every slot as defined by its configuration, regardless of whether a 

specific Search Space is active or monitored by a particular UE in a given 

slot.. 

A Search Space is a predefined region where the UE looks for control information on the 

PDCCH. The network does not send control messages directly to a fixed location known by 

the UE. Instead, it places the control information in certain candidate locations within this 

search space. 

In 5G, the PDCCH carries Downlink Control Information. This includes scheduling 

assignments, paging indication, and various control commands. However, the exact position 

of this information is not fixed. It can vary in time and frequency depending on 

configuration. 

The Search Space defines a set of possible locations where the UE should attempt to decode 

control information. These locations are called PDCCH candidates. Each candidate 

corresponds to a specific aggregation level and a specific Control Channel Element mapping. 

The UE does not know in advance which candidate actually contains the message intended 

for it. Therefore, it performs blind decoding. This means the UE tries multiple decoding 

attempts across different candidates and different formats. 

For each candidate, the UE attempts to decode the signal and checks whether the decoded 

information is valid. This validation is done using CRC, which is masked with a specific RNTI. 

If the CRC check passes with the UE’s RNTI (or a common RNTI like P-RNTI for paging), the 

UE knows that the message is intended for it. 

This process is repeated across all configured candidates in the search space. Once a valid 

match is found, the UE stops further decoding and processes the received control 

information. 

The Search Space configuration is provided by higher layer signaling such as RRC. It defines 

parameters like monitoring periodicity, duration, aggregation levels, and number of 

candidates. 

From UE perspective, the behavior is systematic. UE wakes up at the configured monitoring 

occasion. UE scans the configured search space. UE performs blind decoding on each 

candidate. UE checks CRC with relevant RNTIs. If a match is found, UE processes the 

message. If not, UE stops monitoring and returns to sleep. 

This mechanism provides flexibility to the network. The gNB can dynamically place control 

information without needing a fixed mapping for every UE. At the same time, the UE can 

still reliably find its messages through controlled blind decoding. 

In simple terms, the Search Space is a set of possible locations where messages may exist. 

The UE checks each possible location until it finds the one that belongs to it. 

Common vs UE specific Search Space 

In 5G, search spaces are divided into two main types. These are Common Search Space and 

UE-specific Search Space. This separation is important because the UE behavior changes 

depending on its connection state. 

The Common Search Space is used for signals that are relevant to all UEs or to UEs that are 

not yet fully connected. This search space is configured in a way that every UE in the cell 

can monitor it without needing dedicated signaling. It is mainly used during initial access 

and for broadcast or system-level information. 

For example, when a UE is trying to access the network, it uses the Common Search Space 

to detect control information related to SIB1 or RACH procedures. During the random access 

process, the UE monitors PDCCH in the Common Search Space to detect DCI such as Msg2 

or Msg4 scheduling. Also, paging indication using P-RNTI is typically detected in this 

common region. 

Because the UE does not yet have a dedicated configuration, the Common Search Space 

acts as a shared monitoring area. Every UE knows where to look based on predefined or 

broadcast configuration. 

The UE-specific Search Space is different. This search space is dedicated to a particular UE. 

It is configured through RRC signaling after the UE establishes a connection with the 

network. This means the UE must be in RRC_CONNECTED state to receive and use this 

configuration. 

In UE-specific Search Space, the gNB assigns a customized set of PDCCH candidates for that 

UE. This allows more efficient and flexible scheduling. The network can optimize control 

signaling for each UE individually, reducing unnecessary blind decoding and improving 

performance. 

From UE perspective, the usage is state-dependent. Before connection establishment, the 

UE relies only on Common Search Space. It monitors shared control regions to receive 

essential system information and complete access procedures. After connection 

establishment, the UE also uses UE-specific Search Space for dedicated control signaling. 

This separation improves overall system efficiency. Common Search Space ensures that all 

UEs can receive essential information without prior configuration. UE-specific Search Space 

enables optimized and targeted communication once the UE is connected. 

In simple terms, Common Search Space is like a public notice board that everyone can 

read. UE-specific Search Space is like a private mailbox assigned to each UE after it 

becomes fully connected. 

●Common Search Space (CSS): This is like a public bulletin board. Everyone knows 

where it is. It's used for messages that everyone needs (like SIB1) or for UEs that 

haven't fully "joined" the network yet. 

●UE-Specific Search Space (UE-SS): This is like a private PO Box. Once you are fully 

connected (RRC_CONNECTED), the network gives you a private area to look for your 

specific data. 

Summary Table of Search Space Types 

This table provides a high-level map of how different PDCCH search space types are used in 

5G. It shows that the network does not use a single search method for all control messages. 

Instead, it defines several search space categories, and each category is associated with a 

specific purpose, a specific RNTI, and a specific stage of UE operation. Some search spaces 

are common and are meant for all UEs, especially during system information reading, 

random access, and paging. Others are UE-specific and are used after dedicated 

configuration is established for an individual UE. Overall, the table helps explain how 5G 

organizes control-channel monitoring in a structured way so that the UE knows where to 

look for different kinds of signaling depending on its state and the type of message being 

delivered. 

![[pdcch_searchspace_type_summary.png]]


SECTION 6 CORESET Zero and Search 

Space Zero 

1.CORESET 0 is a special type of CORESET which carries PDCCH/DCI for SIB1 

2.It plays a critical role in the process of cell detection in NR SA 

3.The resource allocation (i.e, time and frequency domain resource allocation) for 

CORESET 0 is configured by MIB (i.e, PBCH in SSB). Other types of CORESET is 

configured by SIB or RRCSetup/RRCReconfiguration, but CORESET 0 cannot be 

configured by SIB or other RRC message because it should be known before SIB or 

other RRC is detected. 

4.There are only two options of frequency domain resource allocation (i.e, number of 

RBs) for CORESET 0 : 24 or 48 RBs. 

5.There are three options of time domain resource allocation (i.e, number of OFDM 

symbols) for CORESET 0 : 1, 2 or 3. 

6.The starting position of CORESET 0 in frequency domain is set with reference to SSB 

position 

Why we need a special procedure ? 

SIB1 is carried by a PDSCH like any other user data (or other Over The Air message : OTA). 

Then, why we need a special algorithm for transmitting and decoding a PDSCH carrying 

SIB1 ? 

Before answering the question, let's briefly summarize a mechanism in which other user 

data or downlink OTA message is scheduled and decoded. It goes as follows. 

i) network defines a specific physical resources for transmitting DCI (CORESET) 

ii) network defines a specific set of candidate physical resources that UE has to monitor 

(Search Space) to find scheduling information 

iii) network defines a list of symbol allocation (TimeDomainResourceAllocation) for PDSCH 

iv) Network notifies UE of all of these configuration information via RRC message (like SIB1, 

RRCSetup or RRCReconfiguration) 

v) When the network want to send a specific PDSCH, it sends a DCI and transmits PDSCH 

as scheduled in the DCI. 

The keyword for this process is RRC message. In case of user data or other OTA message, 

PDSCH is transmitted after all the detailed configuration information is informed to UE. But 

SIB1 transmission/decoding should happen before any RRC message is delivered to UE. The 

only RRC message delivered to UE before SIB1 transmission is MIB message. As you know, 

MIB can carry only a handful of information.. not enough space to carry all the detailed 

information mentioned above. Then how can UE figure out all the detailed information at 

this point ? A common technique to handle this kind of situation (i.e, letting UE know of the 

long/detailed information without relying on a lengthy RRC message) is to use some 

predefined table (predefined configuration) and algorithm known to both UE and NW by the 

specification. This is the basic idea of transmitting and decoding SIB1 in NR. 

3GPP defined many of predefined tables and algorithms to process it and a few information 

elements in MIB specifies which of the predefined table should be used. 


![[pdcch_rrc_config_mib.png]]


HOw does UE operate: 

1.UE is tuned to a specific NR band.  

2.When UE is switched on, it starts to scan the band at synchronisation raster 

(GSCN) 

3.These synchronisation rasters are the points where SSB block is centered. 

4.When one of the raster is aligned with SSB, the ue blindly decode PSS and SSS 

signal for both possible SCS. Since UE already knows the FR (FR1 or FR2) based 

on the band it is operating, it also know if the possible SCS is 15 or 30 Khx or 120 

or 240 Khz. 

5.Assume it is fr1 and SSB has 15 Khz SCS. Then with 15 Khz blind decoding gives 

good RSRP and RSRQ. 

6.The UE then decodes PSS and SSS singal and finds out the Ncell ID. 

7.Then UE goes further and decode MIB.  

8.inside MIB it finds the following relevant IEs for PDCCHconfing for SIB1. 

9.Note that DCI on PDCCH will tell the scheduling info of SIB1. SIB1 is actually on 

PDSCH. 

10.Inside MIB, the IE subcarrierspacingCommon will tell the SCS of PDCCH for 

common i.e. 15 or 30 

11.since UE already know the band in which it is operating in, it knows the minimum 

BW 3gpp allows in it. so based on these three values, Ue find which table it has to 

refer. from the above table. 

12.for example lets say we have SSB SCS = 30 Khz, PDCCH SCS = 30 Khz and assume 

that the band is n78. From SSB SCS and PDCCH SCS, we can find two candidates 

Table 13-4 and Table 13-6. To find which of the two candidate to choose, we need 

additional information Min BW. This min BW varies depending NR band and the 

channel BW that are supported by the band. This is defined in 38.101-1 Table 5.3.5-

1. From this table, you would notice the minimum Bandwidth that n78 support is 

10Mhz. 

13.In conclusion, you would know the CORESET table we need to look into is 38.213-

Table 13-4.which looks like below point​

​ ![[pdcch_coreset_table.png]]

14.Now since the table is known, the UE uses the first four bits of  pdcch-ConfigSIB1 

to get the index number, suppose  pdcch-ConfigSIB1 = 0000 then the first row is 

something UE will pick. 

15.from the first row UE gets: ​

the size of CORESETZero in freq = N^{CORESET}_{RB} = 24 RBs.​

the sixe of CORESETZero in time = Nsymb = 2​

multiplexing pattern = 1​

offset RB = 0 

16. Now UE tries to find searchspaceZero. Based on the multiplexing pattern = 1 and 

FR1 Ue finds the correct row in search space table (shown above the coresetzero 

table). 

17.From there UE gets that table 13-11 it needs to look, which looks like below:
![[pdcch_searchspace_table.png]]​

18.From the this table, UE looks at the last 4 bits of pddch-ConfigSIB1. Assume 

pddch-ConfigSIB1 = 0000, then the index is 0 and we need to look first row in the 

above table. 

19.That row gives following info:​

O = 0;​

SSset per slots = 1;​

M = 1;​

first symbols index = 0 

20.There is a one more important parameters we get from MIB also:  ​

k_ssb = ssb-subcarrierOffset. always 15 Khz subcarrier spacing for FR1, 60 Khz 

subcarrier spacing for FR2 regardless of SSB subcarrier spacing. Importance of which 

we can see in below figure:​

​![[bwp_point_A_CRB.png]]

​ 

21.Note that SSB has fixed 20 PRbs (irrespective of SSB SCS which could be 15 or 

30). Since UE first find SSRref only,  based on SCS which UE already knows the 

lower boundary of SSB is known to UE. then UE use k_ssb to find the nearest CRB 

starting point. and then use Offset from table 13.4 to calculate starting point of 

CORESET Zero in frequency. (PointA is still not know to UE, it will come in SIB1) 

22.IMPORTANT point: k_ssb is in terms of 15Khz subcarriers for FR1 ALWAYS and 

Offset is in terms of RBs which SCS is same as CORESETZero SCS (could be 15 * 

12 or 30 * 12). 

CORESET 0 Transmission Slots 

The transmission and monitoring is defined by the 3GPP TS 38.213 - Section 13.  This 

process involves determining the timing and slot positions for CORESET 0 transmission in 

relation to theSSB blocks, using multiplexing pattern. It ensures that the UE can efficiently 

monitor the PDCCH in the correct slots and symbols, based on parameters like slot offsets, 

scaling factors, and numerology, enabling successful initial access and synchronization with 

the 5G network. 

NOTE : Coreset0, SearchSpace0, DCI for SIB1, PDSCH for SIB1 are all in the same slot, 

because SearchSpace0 is within Coreset 0 and DCI for SIB1 is within the SearchSpace0 and 

DCI & the scheduled PDSCH is in the sameslot (i.e, k0=0). Therefore, Coreset 0 

transmission slot also means the transmission slot of SearchSpace0, DCI for SIB1, PDSCH 

for SIB1 

NOTE :Simply put, n0 indicates the offset in the unit of slot between SSB and the 

corresponding coreset 0.  n0=0 means that SSB(PBCH/MIB) and Coreset 0(SearchSpace0, 

DCI for SIB1, PDSCH for SIB1) are all in the same slot.. n0!=0 means SSB and Coreset0 are 

in different slots 

NOTE :You can get many visualized example of n0calculation at this Amarisoft Tech-

Academy Tutorial 

The overall logic of determining the Coreset 0 transmission is illustrated as below 

![[pdcch_coreset_calculation.png]]


Let’s break down the process. 

PDCCH monitoring for Type0-PDCCH CSS (Common Search Space) set is associated with 

SS/PBCH blocks and CORESET multiplexing pattern. The key points are: 

●CORESET 0 Transmission Timing: 

■CORESET 0 is transmitted in even frames when [(O · 2μ + ⌊i · M⌋) / 

Nslotframe,μ] mod 2 = 0. 

■CORESET 0 is transmitted in odd frames when [(O · 2μ + ⌊i · M⌋) / 

Nslotframe,μ] mod 2 = 1. 

●Here, O is the slot offset, i is the candidate SS/PBCH block index, M is a scaling 

factor, Nslotframe,μ is the number of slots in a radio frame for subcarrier spacing (SCS) 

configuration μ, and μ is the numerology index (related to SCS). 

●Slot Index for Monitoring: 

○The slot index n0 where the UE monitors PDCCH is given by: 

○n0 = (O · 2μ + ⌊i · M⌋) mod Nslotframe,μ 

○The UE monitors two consecutive slots starting from slot n0, i.e., slots n0 and 

n0 + 1. 

The imprtant point of this process (calculation) is to figure out n0.  n0represents the starting 

slot index within a radio frame where the UE begins monitoring the PDCCH for the Type0-

PDCCH Common Search Space (CSS) set, which is associated with CORESET 0 

Practically, n0 determines the exact timing (in terms of slots) within a radio frame where the 

UE should look for CORESET 0, which contains the PDCCH. With 38.213 Table 13-11, as an 

example, for μ = 1 (where Nslotframe,μ = 20), O = 0, and M = 1/2, the values of n0 are 

calculated as 0, 1, 2, and 3 for SS/PBCH block indices i = 0, 2, 4, 6, respectively. This 

means the UE monitors slots 0 and 1 for i = 0, slots 1 and 2 for i = 2, and so on. This slot 

index ensures that the UE aligns its monitoring with the network’s transmission schedule for 

CORESET 0, which is tied to the SS/PBCH block transmissions. 

Example 01 >  SSB/PDCCH SCS = {30,30}, SSB Index = 0, 

Table 13-11 = 4 

This example is the case where FR1 and Pattern 1 is used, SSB index is 0 (the first SSB) 

and the index within the table 13-11 is 4 (This index is configured by the IE 

SearchSpaceZero in MIB). 

According to the calculation shown below, CORESET 0 is scheduled at slot 10 in the radio 

frame that meets the SFNc criteria. 

![[pdcch_coreset_indepth_calc.png]]

According to following calculation, CORESET 0 is scheduled to be every EVEN radio frame. (This determined the radio frame where the SSB is transmitted) 

![[pdcch_coreset_indepth_sfn.png]]



CORESET 0 Aggregation Level 

What is the aggregation level that are applicable to CORESET 0 for SIB1 decoding ? I 

haven't found any explicit statement yet from the specification, but I guess that at least 

following aggregation levels would be applicable to CORESET 0 as per 38.213 - Section 10. 

Your instinct here is right, and the specification does let you pin it down - just not in one 

sentence. The table above tells you what the UE would monitor, and the CORESET 0 

dimensions from Tables 13-1 to 13-10 tell you what will physically fit. Put the two together 

and the answer falls out. 

A CCE is 6 REGs, and a REG is one resource block in one OFDM symbol. So a CORESET 

carries : 

number of CCEs = $(N_{RB} \times N_{sym}) / 6$    

Every CORESET 0 shape that appears anywhere in Tables 13-1 to 13-10 therefore has a 

fixed CCE budget, and an aggregation level simply cannot be monitored if the CORESET 

does not contain that many CCEs : 

![[pdcch_aggregation_level.png]]

Two things in that table are worth pausing on. 

The small shapes cannot host AL 16 at all. A 24 RB / 2 symbol CORESET 0 has 8 CCEs, and 

an aggregation level 16 candidate needs 16 - there is simply nowhere to put it. So on a 

narrow CORESET 0 the UE's most robust candidate is AL 8, and SIB1 coverage is 

correspondingly worse. If you are choosing an index for a cell that has to reach the edge of 

a large sector, this is the argument for spending the extra bandwidth on a 48 RB or 96 RB 

CORESET 0 rather than the smallest one that fits. 

The largest shape fits everything exactly. Add up the table above it - 4 candidates at AL 4, 2 

at AL 8, 1 at AL 16 - and you get 4x4 + 2x8 + 1x16 = 48 CCEs, which is precisely the 

capacity of a 96 RB / 3 symbol CORESET 0. That is almost certainly not a coincidence : the 

biggest CORESET 0 in the specification is sized so that every candidate the UE is asked to 

monitor can sit in its own CCEs with no overlap at all. 

Finally: 

Once all the information about CORESET 0 is determined, UE need to figure of PDSCH 

resource informtion from DCI. The DCI for SIB1 PDSCH is DCI 1_0 explained below.​

​
![[dci_1_0_si_rnti.png]]
​

​

From the DCI, UE need to figure out the frequency and time domain resource information of 

the SIB1 PDSCH. There is no problem with figuring out the frequency domain resource 

information since it is directly encoded in the DCI, but there is problem with figuring out the 

time domain resource information since it is not directly encoded in the DCI. The time 

domain resource information is indicated by an index value pointing to a special form of 

table (called PDSCH-TimeDomainResourceAllocation). Usually this table is informed to UE 

via RRC message as explained in this note, but at the point of SIB1 decoding these RRC 

message has not been delivered to UE yet. So there should be some predefined table known 

to UE by 3GPP specification. In SIB1 PDSCH case, the table 38.214 -Table 5.1.2.1.1-2 

(shown in this note) is used. 

🔹 Why not simply ? 

●If we only needed to represent which RB index to start from, then yes,  bits would 

suffice. 

●But in NR, the UE must know both the start RB and the length of the allocation. 

●That means the scheduler can choose any contiguous block of RBs: 

■Start position = 0 …  

■Length = 1 …  

So the number of possible allocations is not just , but the number of unique (start, 

length) pairs. 

🔹 3GPP Formula
$$N_{FDRA} = \bigl \lceil \log_2 \frac{N_{RBDL} \times (N_{RBDL} + 1)}{2}\bigr\rceil$$

●The term  ( N_RBDL x (N_RBDL + 1) ) /2  is the number of possible contiguous RB 

allocations. 

●It comes from the formula for the number of intervals in a set of elements. 

●Each allocation is defined by a start RB and a length, but since it’s contiguous, the 

count is triangular rather than square. 

●for example lets say N_RBDL is 5. Then you can  

○1 

○1..2 

○1..3 

○1..4 

○1..5 

○2 

○2..3 

○2..4 

○2..5 

○3 

○3..4 

○3..5 

○4 

○4..5 

○5 

○So basical 5+4+3+2+1 = 5 (5+1)/2 = 15 

○then we find the required bits to represent this number and take upper 

integer value 

🔹 Step 2: RIV encoding rule (TS 38.214 §5.1.2.2.1) 

For contiguous allocation type 1, the RIV is defined as: 

RIV = N_RBDL * (L - 1) + S 

● L = length of allocation (number of RBs) 

● S = start RB index (0-based) 

●Constraint: 0 <= S <= N_RBDL - 1  

🔹 Step 4: How UE decodes 

●UE reads the RIV field from DCI 1_0. 

●It applies the inverse mapping: 

○ L = floor(RIV / N_RBDL ) + 1 

○S = RIV modulo N_RBDL 

●Then it knows the exact RB range to demodulate. 

Time domain scheduling of SIB1. 

 Time Domain Assignment Field (3GPP definition) 

●In DCI 1_0, the time domain assignment is encoded as an index. 

●This index points into a predefined table (TS 38.214 §5.1.2.1.1). 

●Each table row defines: 

■K0 → slot offset relative to the DCI slot 

■Start symbol (S) → first OFDM symbol of PDSCH 

■Length (L) → number of symbols allocated 

■Mapping type (A or B) → determines reference signal placement 

So the UE doesn’t directly get “start symbol = 2, length = 12” from DCI. Instead, it gets an 

index, then looks up the table. 

SECTION 7 REFERENCES 

Understanding the Heart of the 5G Air Interface: An Overview of Physical Downlink 

Control Channel for 5G New Radio (NR)  

https://www.sharetechnote.com/html/5G/5G_ResourceAllocationUnit.html 

https://www.sharetechnote.com/html/5G/5G_SearchSpace.html 

https://www.sharetechnote.com/html/5G/5G_CommonSearchSpace_Type0_PDCCH.ht

ml 

https://www.sharetechnote.com/html/WebProgramming/Websim_ResourceGridSsbCor

eset0.html 

Words 6234

Chars 36993

Page 27 of 27