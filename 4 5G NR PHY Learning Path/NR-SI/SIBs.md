


# System Information Blocks



scope: this will capture the scheduling of MIB and SIBs their content and meaning of it. 

It will NOT capture the PHY layer processing. This document is more 3gpp protocol 

specific and IEs in MIB and SIBs. 

Section 1 Main block 

![[si_sib1_main.png]]

This ASN.1 structure is the formal definition of SIB1 from 3GPP TS 38.331. Let’s go element by element so you 

understand what each IE means, how the UE uses it, and what values it can take. 

🔹 cellSelectionInfo:   

Defines thresholds for cell selection and reselection. 

●q‑RxLevMin / q‑RxLevMinSUL → Minimum RSRP required to camp on the cell (in dBm). 

●q‑RxLevMinOffset → Offset applied when evaluating neighboring cells. 

●q‑QualMin / q‑QualMinOffset → Minimum RSRQ thresholds for quality‑based selection. Used during initial 

cell search and reselection; values typically range from –140 to –44 dBm for RSRP. 

🔹 cellAccessRelatedInfo   

Contains PLMN identities, tracking area code, cell identity, and access barring flags. UE reads this to know which 

operator the cell belongs to and whether it’s barred or reserved. 

🔹 connEstFailureControl 

Controls how the UE handles repeated connection‑establishment failures. Includes timers and counters to prevent 

 

endless RRC Connection Request loops. 

🔹 si‑SchedulingInfo 

Tells the UE when and how other SIBs (SIB2, SIB3, etc.) are broadcast. Defines periodicity (e.g., 160 ms, 320 ms) and 

mapping of SIBs to SI messages. This element is relevant for us at this stage and we will look into more depth later. 

🔹 servingCellConfigCommon 

Provides common physical‑layer and MAC‑layer parameters for the serving cell. Includes bandwidth parts, 

reference‑signal configs, and timing alignment info used before RRC setup. This element is also relevant to us and we 

shall look into this element later. 

🔹 ims‑EmergencySupport 

Indicates whether IMS emergency services are supported. Value = true → UE may initiate emergency calls via IMS. 

🔹 eCallOverIMS‑Support 

Shows if eCall (automatic vehicle emergency call) is supported over IMS. Value = true → UE can use IMS for eCall 

procedures. 

🔹 ue‑TimersAndConstants 

Contains timers like T300, T301, T310, T311 and constants used in RRC procedures. UE uses these for connection 

setup, radio‑link failure detection, and recovery. 

🔹 uac‑BarringInfo 

Defines access‑class barring parameters. 

●uac‑BarringForCommon / uac‑BarringPerPLMN‑List → Probability and delay for access attempts. 

●uac‑AccessCategory1‑SelectionAssistanceInfo → Helps UE choose which access category to use. Used to 

control network load and prioritize emergency or high‑priority UEs. 

🔹 useFullResumeID 

Indicates whether the UE should use a full resume ID during RRC resume. If true → UE resumes using full context; 

otherwise uses shortened ID. 

🔹 lateNonCriticalExtension 

Reserved for future non‑critical additions. Optional octet string; ignored by current releases. 

🔹 nonCriticalExtension 

Holds version‑specific extensions (e.g., SIB1‑v1610‑IEs). Allows backward‑compatible evolution of SIB1 without 

breaking older UEs. 

the two most important elements from above are si-schedulingInfo and 

servingCellCommonConfig which we shall see next.​

![[si_scheduling_info.png]]


![[si_scheduling_info_element.png]]

​
![[si_request_config_resources.png]]

All three ASN.1 structures are visible (SIB1, SchedulingInfo/SIB‑Mapping, and SI‑RequestConfig), let’s 

build a complete, professional explanation of every element, showing how the UE interprets them 

step‑by‑step. 

1. SIB1 Structure 

SIB1 is the first and most critical System Information Block. It provides the UE with all parameters needed 

to camp on a cell and request further system information. 

|                                                         |                                                                                      |                                                                                                        |                                                            |
| ------------------------------------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
| Element                                                 | Description                                                                          | Usage                                                                                                  | Typical Values <br>/ Notes                                 |
| cellSelectionInfo                                       | Contains thresholds for cell <br>selection and reselection.                          | UE compares measured <br>RSRP/RSRQ against <br>these values to <br>decide if the cell is <br>suitable. | q‑RxLevMin, <br>q‑QualMin <br>(in dBm); <br>offsets (1–8). |
| cellAccessRelatedInfo                                   | Holds PLMN IDs, Tracking Area <br>Code, Cell Identity, and access <br>barring flags. | UE identifies operator <br>and checks if the cell <br>is barred or reserved.                           | PLMN list, TAC, <br>Cell ID.                               |
| connEstFailureControl                                   | Controls retry behavior after <br><br>failed RRC Connection Request.                 | Prevents repeated <br><br>failures; defines <br><br>timers and counters.                               | Optional.                                                  |
| si‑SchedulingInfo                                       | Defines periodicity and mapping <br><br>of other SIBs (SIB2, SIB3 …).                | UE learns when to <br><br>read additional <br><br>system information.                                  | Periodicity <br><br>values <br><br>(rf8–rf512).            |
| servingCellConfigCommon                                 | Common PHY/MAC <br><br>configuration for the serving cell.                           | Used before <br><br>RRC setup; includes <br><br>bandwidth parts and <br><br>reference signals.         | Optional.                                                  |
| ims‑EmergencySupport                                    | Indicates IMS emergency call <br><br>capability.                                     | UE can initiate <br><br>emergency calls if <br><br>true.                                               | true / absent.                                             |
| eCallOverIMS‑Support                                    | Indicates support for eCall over <br><br>IMS.                                        | Used by automotive <br><br>UEs.                                                                        | true / absent.                                             |
| ue‑TimersAndConstants                                   | Contains RRC timers <br><br>(T300–T311) and constants.                               | UE uses these for <br><br>connection setup and <br><br>recovery.                                       | Optional.                                                  |
| uac‑BarringInfo                                         | Defines access‑class barring <br><br>parameters.                                     | Controls network <br><br>load; prioritizes <br><br>emergency or <br><br>high‑priority UEs.             | Probability, <br><br>delay, <br><br>per‑PLMN lists.        |
| useFullResumeID                                         | Indicates whether full resume ID <br><br>is used in RRC resume.                      | true → UE resumes <br><br>with full context.                                                           | Optional.                                                  |
| lateNonCriticalExtension / <br><br>nonCriticalExtension | Reserved for future versions.                                                        | Ensures backward <br><br>compatibility.                                                                |                                                            |
|                                                         |                                                                                      |                                                                                                        |                                                            |




2. SchedulingInfo, SIB‑Mapping, and SIB‑TypeInfo 

These structures describe how the gNB broadcasts or groups SIBs into SI messages. 

|                        |                                                                          |                                                           |                                                                           |
| ---------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Element**            | **Description**                                                          | **Usage**                                                 | **Typical Values / Notes**                                                |
| si‑BroadcastStatus     | Indicates whether the SI <br><br>message is currently <br><br>broadcast. | UE ignores <br><br>non‑broadcast <br><br>messages.        | broadcasting / notBroadcasting.                                           |
| si‑Periodicity         | Defines repetition rate of <br><br>SI messages.                          | UE schedules <br><br>reading <br><br>accordingly.         | {rf8, rf16, rf32, rf64, rf128, rf256, rf512} <br><br>(radio‑frame units). |
| sib‑MappingInfo        | Points to list of SIBs <br><br>included in this SI <br><br>message.      | UE knows which <br><br>SIBs are grouped <br><br>together. | Sequence of SIB‑TypeInfo.                                                 |
| SIB‑TypeInfo.type      | Identifies the SIB (e.g., <br><br>SIB2, SIB3, SIB5).                     | UE requests or <br><br>decodes the correct <br><br>SIB.   | Enumerated {sibType2…sibType9}.                                           |
| SIB‑TypeInfo.valueTag  | Version tag for the SIB.                                                 | UE re‑reads SIB if <br><br>tag changes.                   | Integer 0–31.                                                             |
| SIB‑TypeInfo.areaScope | a specific area.                                                         | broadcast (e.g., <br><br>small cells).                    | true → area‑specific.                                                     |


3. SI‑RequestConfig and SI‑RequestResources 

These define how the UE requests non‑broadcast SIBs via RACH. 

|                           |                                                                                    |                                                                                       |                                                                          |
| ------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| Element                   | Description                                                                        | Usage                                                                                 | Typical Values / Notes                                                   |
| rach‑OccasionsSI          | Defines RACH <br><br>occasions <br><br>reserved for SI <br><br>requests.           | UE uses these <br><br>occasions to <br><br>send SI‑request <br><br>preambles.         | Sequence of RACH‑ConfigGeneric.                                          |
| rach‑ConfigSI             | Generic RACH <br><br>configuration for <br><br>SI requests.                        | Contains <br><br>preamble <br><br>format, power <br><br>ramping, <br><br>repetitions. | Inherited from RACH‑ConfigGeneric.                                       |
| ssb‑perRACH‑Occasion      | Number of SSBs <br><br>per RACH <br><br>occasion.                                  | Aligns RACH <br><br>timing with <br><br>SSBs.                                         | {oneEighth, oneFourth, oneHalf, one, two, four, eight, <br><br>sixteen}. |
| si‑RequestPeriod          | Defines periodicity <br><br>of SI‑request <br><br>opportunities.                   | Prevents <br><br>excessive RACH <br><br>load.                                         | {one, two, four, six, eight, ten, twelve, <br><br>sixteen} frames.       |
| si‑RequestResources       | Lists resources for <br><br>each SI message <br><br>that can be <br><br>requested. | UE selects <br><br>resource based <br><br>on desired SIB.                             | Sequence (1..maxSI‑Message).                                             |
| ra‑PreambleStartIndex     | Starting index of <br><br>RACH preamble <br><br>group for SI <br><br>requests.     | UE picks <br><br>preamble within <br><br>this range.                                  | Integer 0–63.                                                            |
| ra‑AssociationPeriodIndex | Association period <br><br>between SSB and <br><br>RACH occasions.                 | Maps SSB to <br><br>RACH timing.                                                      | Integer 0–15 (optional).                                                 |
| ra‑ssb‑OccasionMaskIndex  | defining valid SSB <br><br>occasions for SI <br><br>requests.                      | allowed <br><br>SSB‑RACH <br><br>mapping <br><br>pattern.                             | Integer 0–15 (optional).                                                 |



Other SIBs (Periodic SIBs) 

Followings are the overall characteristics of other SIBs (i.e, SIBs other than SIB1) that are transmitted 

by periodic broadcast. Their timing still comes from the scheduling information broadcast in SIB1. 

SIB1 groups one or more SIB types into each scheduled SystemInformation message. The scheduling 

entry provides the SI periodicity and the mapping of SIB types to that message. A common si-

WindowLength then defines the duration of each SI window. 

The UE calculates the SI-window position from the message index and window length. It monitors the 

configured SI-RNTI scheduling during that window. If decoding succeeds, the UE stores the SIB 

contents and its value tag where applicable. A changed value tag identifies a later version of the 

associated SIB. 

●Transmitted over DL-SCH (For the detailed lower layer and higher layer scheduling for OSI, see 

this page) 

●Transmitted within periodically occurring time-domain window called SI-Window 

●SIB1 supplies the schedule : The UE obtains the message periodicity, SI-window length, and 

SIB mapping before monitoring periodic SI. 

●One SI message can contain several SIBs : The mapping in SIB1 determines which SIB types 

share a transmission opportunity. normally SIB2 and SIB3 goes in one SI. 

●The window limits monitoring time : The UE searches for the scheduled SI message within its 

calculated SI window rather than continuously. 

SIB2 

SIB2 provides cell-reselection information that is common across intra-frequency, inter-

frequency, or inter-RAT procedures. It also carries serving-frequency reselection parameters 

and intra-frequency information that is not specific to one neighbouring cell. 

SIB3 

Caries NR Intrafrequency Neighbor cell list and Reselection Critera. It identifies intra-

frequency neighbours that require cell-specific reselection offsets. It also lists cells that the 

UE must exclude from reselection consideration. 

there are other SIBs also but we will look them later. right now lets discuss how UE foes 

from SIB1 to SIB2  

UE Procedure: 

How Other System Information (OSI) is Scheduled 

●SI Message Packing: SIBs other than SIB1 do not transmit individually. They are 

grouped into System Information (SI) messages based on matching periodicities. 

●Scheduling applies to the SI message itself rather than to individual SIBs. SIBs 

and positioning SIBs (posSIBs) are never combined in the same message. 

●Non-Overlapping SI Windows: Each SI message transmits within its own 

dedicated, periodically recurring time window (si-WindowLength). Windows of 

different SI messages do not overlap. 

●Legacy (Release 15/16) Calculation: 

■The window start depends on the message's order of appearance (n) in 

schedulingInfoList. 

■The offset parameter x is calculated as: 

■x=(n−1)×w 

■(where w is the si-WindowLength in slots) 

■the first entry in schedulingInfoList starts at offset x = 0. The next starts 

when the first is finished that means after si_windowLength w, and so on.. 

■Limitation: Inserting or deleting an SI message shifts the entry positions 

(n) of all subsequent messages, causing their starting point of 

transmission windows x to change position. 

●Release 17 Improvement: 

■To fix this dependency, Release 17 introduces schedulingInfoList2 and an 

explicit si-WindowPosition-r17 parameter for each entry. 

■The offset parameter x is updated to: 

■x=(si-WindowPosition−1)×w 

■Benefit: Each SI message's scheduled window is fixed to its explicitly 

assigned position value, preventing position shifts when list entries are 

modified.​
![[si-scheduling_info2.png]]
Examples 

Assuming a constant window length of w=10 slots: 

Example 1: Release 15 (Order-Dependent Scheduling) 

●Configuration: 

■Entry 1 (n=1): SI Message A →x=(1−1)×10=0 

■Entry 2 (n=2): SI Message B →x=(2−1)×10=10 

●Change: If a new message (SI Message X) is inserted at Entry 2: 

■Entry 1 (n=1): SI Message A →x=0 

■Entry 2 (n=2): SI Message C →x=10 

■Entry 3 (n=3): SI Message B →x=(3−1)×10=20 

●Result: SI Message B's offset shifted from slot 10 to slot 20 because its list index 

changed from 2 to 3. 

Example 2: Release 17 (Explicit Window Position) 

●Configuration: 

■SI Message A assigned si-WindowPosition = 1 →x=(1−1)×10=0 

■SI Message B assigned si-WindowPosition = 2 →x=(2−1)×10=10 

●Change: A new message (SI Message C) with si-WindowPosition = 5 is added to 

schedulingInfoList2.​

1. SI Message A assigned si-WindowPosition = 1​

2. SI Message C assigned si-WindowPosition = 5​

3. SI Message B assigned si-WindowPosition = 2 

●Result: SI Message B still uses si-WindowPosition = 2, keeping its offset 

calculated at x=10 regardless of where SI Message C is placed inside the 

sequence list. 

Few points: 

1.For every SI in siInfoList the starting frame is the system frame which satisfies 

SFN mod T = 0 where the T is si-Periodicity of that SI. 

2.For example if two SIs have si-Periodicity rf8 and rf16 than the starting frame fo 

3.r SI1 is 0, 8, 16  and so on while for the SI2 it is 0,16,32, and so on. 

4.Inside the starting frame, the specific SI starts at an slot offset x.  

5.This slot offset is calculated as x = (n-1)*w where n is the si-WindowPosition of 

that SI and w is the si-WindowLength. 

6.Note that si-WindowLength is common for all SIs that means if you selected s5 

(slot 5) as the si)windowLength,  and if an SI has si-WindowPosition as n thatn it 

will start after (n-1) * 5. 

7.the offset of any SI x = (n-1) * 5 can cross the frame boundary also. However the 

importatn conditoj is that (n_max  - 1)* w should be very very less than T. that is 

two SIs do not overlap. 

8.

​![[si_time_scheduling_naman.png]]

 

9.for example let take ​

SI-1 =  { si-WindowPosition = 1, si-Periodicity = rf8  }​

SI-1 =  { si-WindowPosition = 4, si-Periodicity = rf8  }​

SI-1 =  { si-WindowPosition = 2, si-Periodicity = rf16  }  ​

and si-WindowLength = s5  = 5 slots. 

10. If the broadcast is set to not broadcast, the UE has to send rach to gNB to get those 

SI. For that SI-RequestConfig is also provided in SIB1. Since this rach configuration is 

conceptually and structurelly very similar to normal Rach, we will cover this information 

in chapter Rach. Here we just assume that rach is sent by UE based on the config sent 

in SIB1. 

When a UE sends an on-demand SI request (using a dedicated PRACH resource), it does 

not immediately monitor for the requested SI. Instead, the start of the downlink 

monitoring window is governed by a specific timeline specified in 3GPP TS 38.331: 

1. The Trigger Condition 

Once the UE transmits the PRACH preamble configured in si-RequestConfig, it waits for 

the gNB to process the request. The gNB acknowledges the request (typically via a 

Random Access Response / Msg2) or simply begins broadcasting the requested SI 

message in its scheduled window. 

2. Time Boundary: The "Next" SI Window 

According to 3GPP standards, the UE starts monitoring for the requested SI message 

starting from the first SI window of that SI message that occurs after the completion of 

the SI request procedure: 

●SI Request Completion: The SI request procedure is considered successfully 

completed when the UE receives the RAR (Msg2) corresponding to its SI 

preamble transmission. 

● 

●First Occurring Window: The UE determines the schedule of the requested SI 

message (using its periodicity $T$ and window position offset $x$). It then 

identifies the very next SI window for that message that starts after the RAR 

reception boundary. 

● 

Summary Timeline 

$$\text{UE sends SI PRACH (Msg1)} \longrightarrow \text{UE receives RAR (Msg2)} 

\longrightarrow \text{UE waits for the NEXT scheduled SI Window for that message}$$ 

●Inside the SI Window: The UE monitors the PDCCH for DCI Format 1_0 

addressed to SI-RNTI (0xFFFF) during that specific SI window. 

● 

If not received: If the UE does not successfully receive the SI message during that first 

SI window, it continues monitoring during the subsequent periodic SI windows for that 

message until it successfully receives all required SIBs or the acquisition timer expires. 

Next is serving cell config Common SIB 

![[si_serving_cell_common.png]]

|   |   |   |   |
|---|---|---|---|
|Element|Description|Usage / <br><br>Purpose|Possible Values / Notes|
|downlinkConfigCommon|Downlink <br><br>configuration <br><br>common to all <br><br>UEs in the cell.|Defines <br><br>parameters like <br><br>bandwidth part, <br><br>reference <br><br>signals, and <br><br>resource <br><br>allocation for <br><br>downlink.|Structure: DownlinkConfigCommonSIB. <br><br>Mandatory field.|
|uplinkConfigCommon|Common uplink <br><br>configuration for <br><br>the cell.|Provides uplink <br><br>bandwidth part, <br><br>power control, <br><br>and resource <br><br>mapping.|Structure: UplinkConfigCommonSIB. <br><br>Optional; required for FDD or TDD uplink.|
|supplementaryUplink|Configuration for <br><br>supplementary <br><br>uplink (SUL)|Used when the <br><br>network <br><br>provides an|Structure: UplinkConfigCommonSIB. <br><br>Optional; used in SUL deployments.|

|   |   |   |   |
|---|---|---|---|
||carrier.|additional <br><br>low‑frequency <br><br>uplink carrier to <br><br>improve <br><br>coverage.||
|n‑TimingAdvanceOffset|Defines offset <br><br>between <br><br>downlink and <br><br>uplink timing <br><br>advance.|Helps UE align <br><br>uplink <br><br>transmission <br><br>timing with gNB <br><br>reception <br><br>window.|Enumerated: {n0, n25600, n39936} <br><br>corresponding to 0, 25.6 µs, 39.936 µs <br><br>offsets. Optional.|
|ssb‑PositionsInBurst|Indicates which <br><br>SSBs are <br><br>transmitted <br><br>within an SSB <br><br>burst.|UE uses this to <br><br>identify valid <br><br>SSB positions <br><br>for <br><br>synchronization <br><br>and <br><br>measurement.|Sequence of two bit strings (size 8): <br><br>inOneGroup and groupPresence.|
|inOneGroup|Bitmask of <br><br>active SSBs <br><br>within one <br><br>group.|Each bit <br><br>corresponds to <br><br>an SSB position; <br><br>‘1’ = SSB <br><br>present.|BIT STRING (8 bits).|
|groupPresence|Bitmask <br><br>indicating which <br><br>SSB groups are <br><br>active.|Used only in <br><br>FR2 (mmWave) <br><br>where multiple <br><br>SSB groups <br><br>exist.|BIT STRING (8 bits). Optional; <br><br>condition FR2‑only.|
|ssb‑PeriodicityServingCell|Periodicity of <br><br>SSB <br><br>transmission for <br><br>the serving cell.|UE uses this to <br><br>predict when <br><br>the next SSB <br><br>will appear for <br><br>synchronization <br><br>and <br><br>measurement.|Enumerated: <br><br>{ms5, ms10, ms20, ms40, ms80, ms160}.|
|tdd‑UL‑DL‑ConfigurationCommon|Common TDD <br><br>uplink/downlink <br><br>slot <br><br>configuration.|Defines DL/UL <br><br>pattern for TDD <br><br>cells.|Structure: TDD‑UL‑DL‑ConfigCommon. <br><br>Optional; condition TDD‑only.|
|ss‑PBCH‑BlockPower|Transmit power <br><br>of the SS/PBCH|Used by UE for <br><br>link budget and <br><br>RSRP|Integer range (–60 to +50 dBm).|

|   |   |   |   |
|---|---|---|---|
||block.|estimation.||
|Extension groups (Release 16–17)|Reserved for <br><br>future <br><br>enhancements.|Carry additional <br><br>parameters <br><br>introduced in <br><br>later releases; <br><br>none affect <br><br>system <br><br>information <br><br>scheduling.|Not used for SIB1 scheduling.|






DownlinkConfigCommonSIB 
![[si_dl_config_common_sib.png]]

![[si_bwp_dl_common.png]]

![[si_pdcch_config_common.png]]

|                 |                                                                    |                                                                                    |                                                            |
| --------------- | ------------------------------------------------------------------ | ---------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| Element         | Description                                                        | Usage / Purpose                                                                    | Possible Values / <br><br>Notes                            |
| frequencyInfoDL | Defines downlink <br><br>carrier frequency, <br><br>bandwidth, and | UE uses this to <br><br>tune to the <br><br>correct downlink <br><br>frequency and | Structure: <br><br>FrequencyInfoDL‑SIB. <br><br>Mandatory. |

|                               |                                                                                                |                                                                                                    |                                                           |
| ----------------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
|                               | subcarrier spacing.                                                                            | decode system <br><br>information.                                                                 |                                                           |
| initialDownlinkBWP            | Specifies the initial <br><br>Bandwidth Part <br><br>(BWP) configuration <br><br>for downlink. | UE uses this BWP <br><br>for initial access <br><br>and SIB decoding <br><br>before RRC setup.     | Structure: <br><br>BWP‑DownlinkCommo<br><br>n. Mandatory. |
| bcch‑Config                   | Configuration for <br><br>Broadcast Control <br><br>Channel (BCCH).                            | Defines <br><br>scheduling and <br><br>mapping of BCCH <br><br>messages (e.g., <br><br>SIBs).      | Structure: <br><br>BCCH‑Config. <br><br>Mandatory.        |
| pcch‑Config                   | Configuration for <br><br>Paging Control <br><br>Channel (PCCH).                               | Defines paging <br><br>occasions and <br><br>resource <br><br>mapping.                             | Structure: <br><br>PCCH‑Config. <br><br>Mandatory.        |
| pei‑Config‑r17                | Paging Early <br><br>Indication <br><br>configuration <br><br>(Release 17).                    | Used for early <br><br>paging <br><br>notification to <br><br>reduce UE power <br><br>consumption. | Optional.                                                 |
| initialDownlinkBWP‑RedCap‑r17 | Reduced‑Capability <br><br>(RedCap) UE <br><br>downlink BWP <br><br>configuration.             | Provides <br><br>optimized <br><br>parameters for <br><br>low‑complexity <br><br>devices.          | Optional.                                                 |
| frequencyInfoDL‑v1800         | Extended frequency <br><br>information <br><br>(Release 18).                                   | Supports <br><br>additional carrier <br><br>frequency bands <br><br>introduced in <br><br>Rel‑18.  | Optional.                                                 |
| lowPowerConfig‑r19            | Low‑power <br><br>configuration for <br><br>downlink <br><br>(Release 19).                     | Enables <br><br>power‑saving <br><br>modes for UEs.                                                | Optional.                                                 |
| pagingAdaptPEI‑Config‑r19     | Paging adaptation <br><br>configuration for PEI release 19                                     | Enhances paging <br><br>efficiency for <br><br>power‑optimized UEs                                 | Optional.                                                 |


BWP‑DownlinkCommon 

|   |   |   |   |
|---|---|---|---|
|Element|Description|Usage / Purpose|Possible Values / Notes|
|genericParameters|Defines general <br><br>BWP parameters <br><br>(numerology, cyclic <br><br>prefix, bandwidth).|UE uses these to <br><br>configure its <br><br>physical layer for <br><br>downlink <br><br>operation.|Structure: BWP. <br><br>Mandatory.|
|pdcch‑ConfigCommo<br><br>n|Common <br><br>configuration for <br><br>PDCCH (control <br><br>channel).|Defines <br><br>CORESETs and <br><br>search spaces for <br><br>common control <br><br>signaling.|SetupRelease { <br><br>PDCCH‑ConfigCommon }. <br><br>Optional but required for <br><br>initial access.|
|pdsch‑ConfigCommo<br><br>n|Common <br><br>configuration for <br><br>PDSCH (data <br><br>channel).|Defines <br><br>modulation, <br><br>coding, and <br><br>mapping for <br><br>downlink data.|SetupRelease { <br><br>PDSCH‑ConfigCommon }. <br><br>Optional but required for <br><br>initial access.|

PDCCH‑ConfigCommon 

|   |   |   |   |
|---|---|---|---|
|Element|Description|Usage / Purpose|Possible Values / Notes|
|controlResourceSetZero|CORESET #0 <br><br>definition for initial <br><br>BWP.|Used for decoding <br><br>common control <br><br>messages (e.g., <br><br>SIB1 scheduling).|Optional; condition InitialBWP‑only.|
||Common CORESET|Defines shared|Optional; required for common|

|   |   |   |   |
|---|---|---|---|
|commonControlResourceSet|for all UEs.|control region for <br><br>system <br><br>information and <br><br>paging.|signaling.|
|searchSpaceZero|Search space <br><br>associated with <br><br>CORESET #0.|UE monitors this <br><br>for DCI 1_0 (SIB1 <br><br>scheduling).|Optional; condition InitialBWP‑only.|
|commonSearchSpaceList|List of common <br><br>search spaces.|UE monitors these <br><br>for broadcast and <br><br>paging DCIs.|Sequence (1–4) of SearchSpace. <br><br>Optional.|
|searchSpaceSIB1|Search space used <br><br>for SIB1 scheduling.|UE monitors this <br><br>for DCI 1_0 with <br><br>SI‑RNTI.|Optional; required for SIB1 <br><br>decoding.|
|searchSpaceOtherSystemInformation|Search space for <br><br>other SIBs <br><br>(SIB2, SIB3 …).|UE monitors this <br><br>when requesting <br><br>or reading <br><br>additional system <br><br>information.|Optional; required for SI scheduling.|
|pagingSearchSpace|Search space for <br><br>paging messages.|UE monitors this <br><br>for PDCCH paging <br><br>DCIs.|Optional; required for paging.|
|ra‑SearchSpace|Search space for <br><br>Random Access <br><br>(RACH) procedures.|UE monitors this <br><br>for RACH‑related <br><br>DCIs.|Optional; required for RACH <br><br>initiation.|
|Extension groups (Rel 16–17)|Reserved for future <br><br>enhancements.|Carry new <br><br>parameters added <br><br>in later releases.|Not used for SIB1 scheduling.|

