
//add figure 1
# Introduction

1. **Frequency Ranges (FRs):** Currently, there are two FRs defined: **FR1** ranging from 410 – 7125 MHz, and **FR2** ranging from 24.25 – 52.6 GHz.
2. **Operating Bands:** 3GPP further defines operating bands in each FR. An operating band is a frequency band associated with a certain set of radio frequency (RF) requirements. Different operators may have different amounts of spectrum within an operating band.
3. **Channel Bandwidths:** To accommodate diverse spectrum scenarios while limiting implementation complexity, NR supports a range of channel bandwidths from **5 – 400 MHz**, where a channel bandwidth refers to the bandwidth of an NR carrier.
4. **Transmission Bandwidth Configuration:** The number of resource blocks (RBs) that may be configured in a channel bandwidth, known as transmission bandwidth configuration, shall meet the specified minimum guard-band requirements.
5. **BWP Concept:** In all previous technologies, a UE was configured to a specific transmission bandwidth. NR introduces a concept of **BWP** where the transmission bandwidth is further parted into segments and UE is configured to only a part of the total transmission bandwidth. That part of the total transmission bandwidth is called one Bandwidth Part.
6. **UE side motivation: Narrow Scheduling vs. Operation:** If the scheduler can give a UE 10 resource blocks out of 273, with less SCS (15 kHz), has it not already narrowed the bandwidth?
   **Answer:** **NO** - Scheduling a UE narrowly is not the same as letting the UE operate narrowly. It still has to keep its receiver open across the whole carrier (doing full FFT in Tx and Rx side).
7. **Network Side Motivation: Supporting IoT:** It is worth adding a word about the network side, because the section above says the purpose is more for the UE than for the network — which is true from the radio point of view but understates the deployment argument. Without BWP, an operator who wanted to serve narrowband devices on a wide carrier would have to deploy a separate narrow carrier for them. BWP means one wide carrier can serve everything, from a 100 MHz eMBB handset to a sensor that can only manage a few megahertz. That is a real saving in spectrum and in equipment, and it is not a UE benefit at all.

// add second picture here

# Bandwidth Part Definition and Rules

- **Definition:** A Bandwidth Part is defined as _"a contiguous set of physical resource blocks, selected from a contiguous subset of the common resource blocks for a given numerology ($u$) on a given carrier."_

- **Contiguity:** A BWP cannot have "holes" in it; you cannot construct a single BWP out of non-contiguous resource blocks (e.g., combining RBs 0–9 and RBs 50–59).

- **Numerology ($u$):** Each BWP carries its own numerology (subcarrier spacing and cyclic prefix). Two overlapping BWPs on the same carrier can operate independently using different spacings (such as 15 kHz and 60 kHz).

- **Carrier Association:** A BWP belongs to a single serving cell. If a User Equipment (UE) uses carrier aggregation, every serving cell maintains its own independent set of BWPs and active BWP. BWPs are also permitted to overlap.
- "selected from a contiguous subset of the common resource blocks" - this is where the two different resource block numbering systems come from. The common resource blocks (CRB) are the absolute grid that runs across the whole carrier, and a BWP is carved out of that grid. Inside the BWP, however, the resource blocks are renumbered from 0 as physical resource blocks (PRB). So a BWP is fully described by just two numbers : where it starts in CRB terms, and how many resource blocks long it is.
    
      
    
13. "for a given numerology (u)" - Each BWP carries its own numerology, i.e, its own subcarrier spacing and cyclic prefix. Two BWPs on the same carrier can use 15 kHz and 60 kHz spacing respectively.
    
      
    
14. "on a given carrier" - a BWP belongs to one carrier (one serving cell). If the UE is doing carrier aggregation, every serving cell has its own independent set of BWPs and its own active BWP.
    
      
    
15. BWPs are allowed to overlap, and in real configurations they very often do.
    
      
    

### 4. Common Resource Block (CRB) and Point A

Because multiple BWPs with different numerologies can overlap across a carrier, the network needs a common reference point to locate specific Physical Resource Blocks:

- **Point A:** An operator-chosen frequency point located at or below the lowest subcarrier of the transmission bandwidth configuration.
- **Signaling:** The UE cannot determine Point A until it decodes the **MIB** and **SIB1** during the cell search procedure. Once found, the network signals the starting point and length of a BWP to the UE inside a single RRC field called `locationAndBandwidth`.
  

### 5. How the UE Finds Point A & Coreset #0

// picture 3 here
- **Global Search Channels (GSCN):** The UE scans the synchronization raster using GSCN to find candidate Primary/Secondary Synchronization Signals (SSB).
- **Decoding MIB:** Once the UE finds an SSB and decodes the **MIB**, it reads the parameter `ssb-SubcarrierOffset` ($k_{SSB}$). This indicates the subcarrier-level frequency offset between the lowest subcarrier of the SSB and the boundary of the subcarrier-aligned Common Resource Block (CRB) grid.
    
      
    
- **Common SCS:** The MIB also provides the Subcarrier Spacing for common channels (`subCarrierSpacingCommon`) for **CORESET #0**, along with a compact 8-bit `pdcch-Config` IE.
    
      
    
- **Locating CORESET #0:** The first 4 bits of `pdcch-Config` index a predefined table in the 3GPP specifications, telling the UE the size of CORESET #0 (in resource blocks and OFDM symbols) and its frequency offset relative to the SSB.
    
      
    

### 6. BWP Rules in Downlink (DL) & Uplink (UL)

#### **Downlink BWP Rules:**

- A UE can be configured with **up to 4 carrier BWPs** in the downlink.
    
      
    
- The bandwidth of each DL BWP must be **equal to or greater than the SS Block bandwidth**, though it does not necessarily have to contain the SS Block.
    
      
    
- **Only one DL BWP can be active** at any given time.
    
      
    
- The UE is **not expected to receive** PDSCH, PDCCH, CSI-RS, or TRS outside of an active BWP.
    
      
    
- Every DL BWP must include at least one **CORESET with a UE-Specific Search Space (USS)**. On the primary carrier, at least one configured DL BWP must also include a **Common Search Space (CSS)**.
    
      
    

#### **Uplink BWP Rules:**

- A UE can similarly be configured with **up to 4 carrier BWPs** in the uplink.
    
      
    
- **Only one uplink BWP can be active** at a given time per carrier.
    
      
    
- If a UE is configured with a **Supplementary Uplink (SUL)**, it can be configured with an additional 4 BWPs in the SUL (with one active at a time).
    
      
    
- The UE **shall not transmit** PUSCH or PUCCH outside of an active uplink bandwidth part.
- Based on the [Bandwidth Part BWP](https://writer.zoho.in/writer/open/m7or04b1135e751884a889f662d91c0febb1c#) document, let's continue with the mathematical formulation for configuring a BWP and how resource block mapping works:

  

### 9. Resource Indication Value (RIV) & BWP Sizing

To signal the starting point and size of a BWP efficiently over the air, NR packs both values into a single integer called the **Resource Indication Value (RIV)** using the same allocation formula used for resource block allocation:

  

- **Formula Variables:** $L_{RB}$ represents the size of the BWP in terms of resource blocks, and $N^{size}_{BWP}$ is the maximum possible RBs in an NR carrier (up to 275).
    
      
    
- **The RIV Range:** The RIV value ranges from 0 to 37,949 (calculated as $\sum_{1}^{275} = \frac{275 \times 276}{2}$). Because the values "flip" around the halfway point (roughly 137–138 RBs), a single encoded RIV allows the UE to instantly decode both the starting RB ($RB_{start}$) and the total length ($L_{RB}$).
    
      
    

### 10. Mapping Between Common Resource Blocks (CRB) and Physical Resource Blocks (PRB)

- **Offset from Carrier:** The $RB_{start}$ derived from the RIV is **not** counted directly from Point A. Instead, it is counted from the start of the carrier for that specific subcarrier spacing, which itself starts at an offset from Point A (`offsetToCarrier`).
    
      
    
- **The Formula:** The position of the $j$-th BWP in the common resource block grid is given by:$$N^{start, j}_{BWP} = offsetToCarrier + RB_{start}$$
- **PRB to CRB Conversion:** Within an active BWP, resource blocks are renumbered starting from 0 as Physical Resource Blocks (PRBs). Going from PRB to CRB uses the equation above. Going in reverse is a simple subtraction: $n_{PRB} = n_{CRB} - N^{start}_{BWP}$, valid only if the resulting index falls between $0$ and $N^{size}_{BWP} - 1$.

**BWP Roles** and **BWP Switching / Selection Mechanisms**:

### 11. The Four BWP Roles (Not Separate Objects)

There are four terms used to describe Bandwidth Parts:

- **Initial BWP**
- **First Active BWP**
- **Default BWP**
- **Regular (Configured) BWPs**

Crucially, these are **not four different physical types of objects**. There is only one kind of object—a Bandwidth Part. Terms like _initial_, _first active_, and _default_ are simply **roles** assigned by pointing a configuration ID to a BWP.


- **Overlapping Roles:** One and the same BWP can hold multiple roles at once. For instance, it is completely normal for **BWP #1** to act as both the _first active BWP_ and the _default BWP_ simultaneously, and for **BWP #0** to act as the _initial BWP_ and _default BWP_ at the same time.
    
      
    
- **Downlink Fallback Only:** There is a `defaultDownlinkBWP-Id` parameter, but **no** corresponding `defaultUplinkBWP-Id`. The "fall back when idle" behavior is defined exclusively on the downlink. In TDD systems, this is handled automatically because the DL BWP and UL BWP with the same ID are paired and switch together; in FDD systems, the uplink simply has no standalone inactivity fallback.
    
      
    

### 12. BWP Selection and Switching Mechanisms

BWP switching is not controlled purely by the network; it relies on both explicit signaling and autonomous UE timers across four distinct triggers:

  

1. **By PDCCH (DCI - Explicit Signaling):** The gNodeB can trigger an immediate BWP switch using the **Bandwidth Part Indicator field** embedded inside **DCI Format 0_1** (for an Uplink Grant) or **DCI Format 1_1** (for a Downlink Schedule).
    
      
    
2. **By the BWP Inactivity Timer (`bwp-InactivityTimer`):** Configured via `ServingCellConfig`, the UE starts this timer whenever it receives a downlink assignment on a non-default BWP. If the timer expires without any further scheduling activity, the UE automatically falls back to its designated **default DL BWP**.
    
      
    
3. **By RRC Signaling:** The network can explicitly reconfigure or switch the active BWP via an RRC Reconfiguration message.
    
      
    
4. **By the MAC Entity (Random Access):** The MAC entity itself initiates a BWP switch upon starting a Random Access (RACH) procedure, switching to the designated initial BWP to handle random access messages.
Based on the [Bandwidth Part BWP](https://writer.zoho.in/writer/open/m7or04b1135e751884a889f662d91c0febb1c#) document, here is the final section covering **BWP Inactivity Timers** and **Switching Delays**:

  

### 13. BWP Inactivity Timer Mechanics

- **Trigger Condition:** If the network configures a `bwp-InactivityTimer`, the UE starts this countdown timer whenever it receives a downlink assignment (PDSCH) or uplink grant (PUSCH) on a **non-default** BWP.
    
      
    
- **Reset Behavior:** If new scheduling activity arrives before the timer expires, the timer is restarted.
    
      
    
- **Expiration & Fallback:** Once the timer hits zero without any further scheduling activity, the UE automatically switches its active DL BWP back to the designated **default DL BWP** without requiring explicit signaling from the gNodeB.
    
      
    

### 14. BWP Switching Delay Requirements

Switching between BWPs is not instantaneous; physical radio frequency components (such as local oscillators and filters) need time to retune to the new frequency allocation:

  

- **Switching Time ($T_{BWP}$):** The 3GPP specification defines strict transition timelines (ranging typically from 0.5 ms to several milliseconds depending on the numerology, frequency range FR1 vs. FR2, and UE capability classes).
    
      
    
- **No Transmission During Switch:** During this transition delay ($T_{BWP}$), the UE is **not expected** to transmit or receive any data channels, ensuring that both the gNodeB and the UE remain synchronized on exactly when the new BWP becomes active.