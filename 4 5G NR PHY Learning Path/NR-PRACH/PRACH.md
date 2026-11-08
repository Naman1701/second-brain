# PRACH

Chapter 8 RACH 

Rach Introduction 

RACH signal generation: 

Length of RACH LRA  

Zadoff Chu Seqeunce, root index. 

Circular shift Cv and Ncs 

Frequncy domain RACH signal  

Long and Short format 

Various paramters. 

Long Formats 

Short Formats 

Time Domain Scheduling of RACH: Main Table 

Configuration Index, Periodicity and Offset, Subframe values 

The Equation of L : starting symbol, Ndur, Nt and NRAt and NRAslot 

Frequency Domain Scheduling of RACH. 

The kmu0 parameter and its equation 

nstart_RA, nRARB, nRA , k1 and kbar 

SSBtoRACH binding concept. 

Restricted Set Concept. 

RRC Configuration. 

CBRP Vs CFRP. 

Rach Receiver Implementation 

 

Section 1 Rach Introduction 

It is the first signal from UE and serves many purposes. 

1.Initial access from RRC_IDLE. UE is switched on and starts try to clamp on a 

carrier. 

2.RRC Connection Re-establishment procedure. UE is trying to connect to 

another carrier or same carrier after some time. 

3.Handover. 

4.DL or UL data arrival during RRC_CONNECTED when UL synchronisation status 

is "non-synchronised"; 

5.Transition from RRC_INACTIVE. UE was already connected to a carrier but have 

been inactive for quite long. 

6.To establish time alignment at SCell addition; 

7.Request for Other SI: in few configuration deployments of NR, you can make 

few SIs information available to UEs only when RACH is received asking for 

them. This feature increases the spectral efficiency in DL. 

8.Beam failure recovery. In NR, RACH is tide with beamforming concept. UE 

detects the best beam in downlink for SS/PBCH channel and then use the 

corresponding PRACH resource to indicate gNB the best beam direction in DL. 

9.Signal generation uses Zadoff-Chu sequence because of its good correlation 

and cross correlation properties as well as its unit gain amplitude. 

10.TWO RACH procedure exists: Contention based RACH procedure and 

Contention free RACH procedure. 

11.Rach signals are divided into two major categories: Long Format and Short 

Format. Long Format are long () LRA = 839 and short format are short LRA = 

139 IQ samples. 

12.A gNB provides a list of rach preambles along with time – frequency resources 

to be used by UE to attempt rach. Now we will see everything in details: 

Section 2: RACH Signal Generation 

2.1 Zadoff-Chu (ZC) Sequence Properties:  

The Zadoff-Chu sequence is favoured for 5G NR RACH due to its excellent properties, 

which make it well-suited for random access scenarios: 

•Constant Amplitude: The Zadoff-Chu sequence has a constant amplitude before 

and after the Discrete Fourier Transform (DFT) operation. This minimizes power 

fluctuations during transmission. 

•Zero Cyclic Auto-Correlation: The sequence exhibits ZERO cyclic auto-

correlation, ensuring that each preamble is PURE orthogonal to itself even under 

cyclic shifts. 

•Low Cross-Correlation: the normalized cross-correlation between two unique 

ZC sequences (i.e., ZC sequences generated with two different root sequences) 

will be low and equal to √ (1/LRA). 

•Flexibility for Configurations: The sequence length (LRA) can be adapted for 

different scenarios, such as large cells or high-speed mobility, by choosing from 

values like 139, 571, 839, or 1151. 

2.2 RACH Singal Generation 

1.A ZC sequence can be generated in time-domain (TD), using a physical root sequence 

u as x_u (n) = e^{ −j πun(n+1)/ L_RA }, n= 0, 1, . . . ,L_RA − 1, where L_RA is the 

sequence length with u∈ {0, 1, . . . ,L_RA−1}. Root sequence u is identified by a table 

which maps the PRACHRootSequenceIndex in SIB to value of u. 

2.A cyclic shifted version (with cyclic shift value of Cv) of the ZC-TD signal can be 

obtained as x_u,v (n) = x_u ((n + Cv) mod L_RA). 

3.For unrestricted set  (we will discuss what is unrestricted set later), the value of Cv is 

calculated based on gNB parameter Ncs as below:​
$$C_{\nu} = \begin{cases} \nu N_{CS} &  \nu = 0,1,...,\lfloor \frac {L_{RA}}{N_{CS}} \rfloor -1 , N_{CS} \neq 0\\ 0 & N_{CS} = 0 \end{cases} $$

4.The parameter zeroCorrelationZone length Ncs is configured by the gNB which is 

derived from the higher layer parameter zeroCorrelationZoneConfig. We will discuss 

more about Ncs later. 

5.Note that the x_uv(n) is time domain signal, which needs a frequency domain 

equivalent to allow us to map it in time-frequency grid. A frequency domain (FD) 

version of the sequence is generated as: ​

 y_u,v(n) = SUM ^ LRA−1 _m=0 ( x_u,v(m) · e^{−j 2πmn/LRA} ) . 

6.A gNB configures the set of preamble sequences (with a maximum of up to 64 

preambles) the UE can use, and UE will select one preamble (randomly out of the 64 

preambles) for transmitting msg1 (or PRACH preamble). 

7.Consider a specific root u , for which there are total 139 ZC sequence (preambles) 

can be generated. However, gNB does not allow nearby ZC sequence because when 

rach is transmitted , one sequence can be detected as the nearby sequence because 

of time delay. Therefore gNB provides N_cs number which tells what is the gap 

between two allowed ZC sequence. Basically the allowed sequences are vN_cs where 

is v is 0 .. floor(L_RA / Ncs)-1.   

8.Using the start physical root sequence u and N_CS, the UE generates 64 ZC 

sequences (or preambles) for each time-frequency PRACH occasion, enumerated in 

first increasing order of cyclic shifts Cv of a logical root sequence, and then in 

increasing order of the logical root sequence index. 

9.It is possible that if N_cs is small (<=2), all 64 ZC preambles are generated within one 

root sequence. It is also possible that if N_cs is large only few sequence fall under 

root index u and then for the remaining we increase u to u+1. ​

             ​

​![[rach_premables_rootSeq_139.png]]
​ 

10.Few points to note above is that ZC sequences with different root u, exhibit low cross 

correlation = sqrt(1/LRA). One v = i box is Ncs size big, every row is 139 samples long 

in which 7*19 = 133 samples have been used. In the last for u+8 row, only one box of 

Ncs length is required. 

Section 3 Preamble Lengths and Formats: 

In NR, there are two types of sequence length called Long and Short Sequence are 

used. 

Long Sequence:  

1.Length 839, four preamble formats are supported, mainly targeting large cell 

deployment scenarios.  

2.These formats can only be used in FR1 and have a subcarrier spacing of 1.25 

or 5 kHz. 

3.TABLE we will se restricted type later.​

​![[rach_long_format_typeAB.png]]

4.Time Domain structure for long format 0,1,2 and 3. The vertical lines are 1 ms 

subframe boundaries. The frequency width is NOT correctly shown.​

​ ![[rach_longformat_time_structure.png]]

5.It is easy to see that long format 1 and 2 take length across multiple 

subframes. Format 0 take full subframe for just one repetition.  

Short Sequence:  

1.length 139, nine different preamble formats are introduced in NR, mainly targeting 

the small/normal cell and indoor deployment scenarios.  

2.The short preamble formats can be used in both FR1 with subcarrier spacing of 15 or 

30 kHz and FR2 with subcarrier spacing of 60 or 120 kHz. 

3.For the design of the short preamble formats, the last part of each OFDM symbol 

acts as a CP for the next OFDM symbol and the length of a preamble OFDM symbol 

equals the length of data OFDM symbols. 

4.Firstly, it allows the gNB receiver to use the same fast Fourier transform (FFT) for data 

and random-access preamble detection. 

5.Secondly, due to the composition of multiple shorter OFDM symbols per PRACH 

preamble, the new short preamble formats are more robust against time varying 

channels and frequency errors. 

6.Thirdly, it supports the possibility of analog beam sweeping during PRACH reception 

such that the same preamble can be received with different beams at the gNB.​

​![[rach_shortformat_types.png]]

7.​

​![[rach_shortformat_time_structure.png]]

8.The above diagram is showing ONE occasion of each format in a subframe. See that 

all formats are having start and end aligned to PUSCH symbol boundaries.  

Section 4 Time domain Scheduling 

In NR, the time domain scheduling is simply configured using a single index called 

PRACH Configuration Index. The index represents a row in the below table. The table is hugh 

so I am showing some part of it.​

​![[rach_ra_config_fr1_pairedspectrum.png]]
​​![[rach_ra_config_fr1_pairedsprectrum2.png]]

Following points are to be noted: 

1.The length of PRACH config index is 256 (0-255 the last row is not shown). 

2.The formats are 0 to 3 for long format and A1, A1/B1, A2, A2/B2, A3, A3/B3 , B1, B4, 

C0 and C2. 

3.There is a Ai/Bi format which we will understand after some time. 

4.B2 and B3 format do not exist idependently, they are only application aft A2 and A3 

repectiviely. 

5.Size of A1 and B1, size of A2 and B2 and size of A3 and B3 are same. The B1, B2 and 

B3 format has guardband at the end. 

Ok now lest start table understanding. 

1.Once a row index is received, UE knows which format is configured , long or short 

and then knows the LRA as well. 

2.Then the question is which system frames the rach resources exists. The answer is 

third column nSFN mod x = y. While x is periodicity , y is offset. For example if x  = 10 

and y = 2, it means every system frame which satisfies nsfn mod 10 = 2, will have rach 

resource like 2, 12, 22, 32 … till 1023. 

3.Inside the single 10 ms long system frame, we have 10 subframes worth of 1 ms 

each. The next question is which subframws inside that system frame from previous 

step. The answer is straight forward fourth cou,me. For example for index 141, it is 4 

and 9 subframes. 

4.Now the story for long format is almost finished. The second column long format 

starts from the first symbols of subframe(s) mentioned in 5th column. The starting 

symbols is always 0th symbols of the subframe.  

5.The story for short format is still carry on. Let’s revisit the picture for short format 

rach and table header and entries.​

![[rach_shortformat_time_structure.png]]
​ 

6.NRA _dur is duration of a short formet in terms of PUSCH symbols. For example C0 

format is 2 PUSCH symbols long so NRA_dur is 2 for C0 format.  

7.Any short format with duration N_dur can generate 14/N_dur rach occasion RO. But 

the issue is that few formats do note have guard band at the end so should be 

confined within less than 14 symbols. This total duration in which multiple RO for a 

format is allowed is called PRACH slot.  And the number of time domain ROs within 

that PRACH slot is represented by NtRA,SLOT. Consider the following example:​

8.A1 has duration 2 PUSCH symbols and it does not have guard band at the end so 

instead of 7, one less that is 6 ROs are allowed for A1 formation so that it is confiedn 

to 12 symbols and do not touch 14 symbols boundary. However, is A1/B1 formation 

is considered, 7 ROs are allowed such that the B1 takes the last 7th RO which is OK 

because B1 has guard period. 

9. IN short format, the UE can attempt to any one of the NtRA,SLOT ROs. The starting 

symbol for that RO is given by:
$$l = l_0 + n^{RA}_t N^{RA}_{dur} + 14 N^{RA}_{slot}$$

10.Here l0 is starting symbol which is given by 6th column. nRAt  is number from 0 to 

NtRA,SLOT -1 and nRA_slot is either 0 or 1 or {0,1}. Let discuss the last parameter below. 

11.In case of dfRA = 30 Khz SPS, we get two slots per subframe. The question is which 

slot (s) are considered for transmission. This problem is solved by nRA_slot .  

12.Basically 3GPP says for long format and short format with SPS 15Khz , there is no 

issue so set nRA_slot = 0. For short format, if Number of PRACH slots within subframe 

is set to 1, then you set nRA_slot to 1. (Note that this means 14 symbols are added 

and we will start filling rach from second slot in the subframe and first slot will not be 

touched). if Number of PRACH slots within subframe is set to 2, then you set nRA_slot 

to both 0 and 1 meaning you get 2 times of ROs now because slots are open for ROs. 

13.Finally, If the preamble format is A1/B1, A2/B2 or A3/B3, then ​

if  nRA_t =  nRA,slot_t  - 1 then ​

 the PRACH preamble with the corresponding PRACH preamble format from​

              B1, B2 and B3 is transmitted in the PRACH transmission occasion;  

otherwise ​

              the PRACH preamble with the corresponding PRACH preamble format from​

              A1, A2 and A3 is transmitted in the PRACH transmission occasion. 

Section 5 Frequency Domain Scheduling 

1.Before we go into frequency domain we need to understand few things.​

​ ![[rach_freq_structure.png]]

2.According to my current understanding: In a Resource grid, we need to align our 

SS/PBCH center to synchronisation raster. In pursuit of that we may have 0 frequency 

location in baseband away from center of the resource grid. In the above picture the 

0 Hz location is what will be mapped to fc carrier frequency in passband. The centre 

of resource grid is ku0   away from the 0Hz point. Similarly, the right highest frequency 

point of the resource is grid will be half of the size of grid plus ku0. And left lowest 

frequency side on the grid (in terms of negative frequency) will be ku0 minus half of 

the size of grid.  

3.The number ku0 is in terms of subcarriers and thus dependent on numerology. For a 

fixed frequency offset this number is smallest for highest numerology and lowest for 

highest SPS numerology. 

4.If we take two points: zero frequency point f0 and point A fpoint-A we can say following 

things:​

![[rach_freq_struct_equation1.png]]
                    ​

5.Suppose mu_o is highest SPS and for that the corresponding ku0 is set to zero than for 

any lower mu following can be said:

![[rach_freq_struct_eqaution2.png]]​

6.Now lets restart the frequency domain analysis. The gNB assigns certain part of the 

active uplink BWP as RACH total resources. Those resources are measured in term of 

PUSCH RB at numerology mu.  

7.The offset between the starting subcarrier of active uplibk BWP and thestarting 

subcarrier of the rach resource is denoted nRA start in terms of pusch resource block 

RB. So the offset in terms of subcarriers will be  nRA start * NRBsc.  

8.The entire rach resource is divided into multiply freuecy division rach occasions. The 

length of one rach occasion is dependent on LRA  and round off to the next pusch RB. 

For example consider ·Δf = ΔfRA = 15k. 139 length is closed to 144 which is 12*12. So 

one frequency domain RO will need 12 PUSCH RBs. The following table captures all 

possible combination of  ·Δf and ΔfRA.​

​ ![[rach_dfRA_combination_kbar.png]]

9.Finally, k1 is distance from lowest subcarrier of resource to the lowest subcarrier of 

that frequency domain RO which is used by UE. Note that nRA is the index of RO 

among all possible ROs. So the equation of k1 becomes:​
![[rach_ra_freq_strcut_equation3.png]]

Note that k1 is in terms of Δf that is SPS of NPUSCH subcarrier. To convert it into ΔfRA 

we need to multiple K = Δf/ΔfRA . 

10.As we have seen the RO length is in terms of PUSCH RBs so not all the RBs are 

occupied by rach. Few subcarriers below and few subcarriers above are empty. Se 

below:​

​

​
![[rach_crbpointA_RO_freq.png]]
​ 

11.Suppose nRA  index RO for LRA =  839 at ΔfRA = 1.25K and Δf = 15K. From the first row 

the above table NRARB = 6 which means 12*6*(15/1.25) = 864 which gives 864-839 = 

25 subcarriers of 1.25 extra. We also need to subtract Δf/2  = 7.5K from both of the 

side which means 12 subcarriers of 1.25 SCS so total remaining extra SCS are = 864-

839-12 = 13.  

12.The same table shows k_bar = 7 which means the preamble should starts AFTER 7 

subcarriers of ΔfRA. This will mean that at the end 13-7  = 6 subcarroiers will be left 

empty. 

Section 6: RO, SSB and Contention-based-Preamble 

Mapping 

In NR, the sync signal (SSB) is associated with different beam and UE select a certain 

beam and send PRACH using that beam. In order for NW to figure out which beam UE has 

selected, 3GPP defines a specific mapping between SSB and RACH Occasion (RO). By 

detecting which RO UE send PRACH to, NW can figure out which SSB Beam that UE has 

selected. 

1.We already know the RACH Occasions can be Frequency multiplexed also as in the 

previous section. RRC message parameter msg1-FDM tells how many ROs are 

multiplexed in frequency domain.  

2.In time domain, we have NRA,slot t  = = number of prach occasions withing a prach slot 

to tell how many ROs are multiplexed in time. 

3.Further, the gNB configures maximum number of preambles : NPREAMBLETOTAL is 

provided by totalNumberOfRA-Preambles for Type-1 random access procedure. 

4. Since there is a grid of ROs , 3gpp defines an ordering system to order ROs. SS/PBCH 

block indexes provided by ssb-PositionsInBurst in SIB1 are mapped to valid PRACH 

occasions in the following order: 

5.First, in increasing order of preamble indexes within a single PRACH occasion.  

6.Second, in increasing order of frequency resource indexes for frequency multiplexed 

PRACH occasions.  

7.Third, in increasing order of time resource indexes for time multiplexed PRACH 

occasions within a PRACH slot  

8.Fourth, in increasing order of indexes for PRACH slots. 

9.3gpps defined one parameter ssb-perRACH-OccasionAndCB-PreamblesPerSSB that 

tells two things : 1. How many SSBs are assigned to one RACH Occasion. 2. How many 

CB-Preambles are assigned to one SSB. From specs “For Type-1 random access 

procedure, a UE is provided a number  of SS/PBCH block indexes associated with one 

PRACH occasion and a number 1 of contention based preambles per SS/PBCH block 

index per valid PRACH occasion by ssb-perRACH-OccasionAndCB-PreamblesPerSSB.”​

​
![[ssb_rach_mapping_rrc.png]]
​ 

10.For example if ssb-perRACH-OccasionAndCB-PreamblesPerSSB = one: n4, then there 

is one SSB bean per RO and 4 preambles are assigned to one SSB. 

11.if N < 1, one SS/PBCH block index is mapped to 1⁄N consecutive valid PRACH 

occasions and R contention based preambles with consecutive indexes associated 

with the SS/PBCH block index per valid PRACH occasion start from preamble index 0 

12.If N ≥ 1, R contention based preambles with consecutive indexes associated with 

SS/PBCH block index n, 0 ≤ n ≤ N − 1, per valid PRACH occasion start from preamble 

index n NPREAMBLETOTAL / N where NPREAMBLETOTAL is provided by totalNumberOfRA-

Preambles for Type-1 random access procedure. 

13.From sharetechnote, following diagrams are generated for 64 preambles, 64 SSBs: 

a.Case 1: ​

msg1-FDM = one, ssb-perRACH-OccasionAndCB-PreamblesPerSSB = one​
![[ssb_rach_mapping_pat1.png]]

b.Case 2: ​

msg1-FDM = two, ssb-perRACH-OccasionAndCB-PreamblesPerSSB = one​

​![[ssb_rach_mapping_pat2.png]]

c.Case 3:​

msg1-FDM = two, ssb-perRACH-OccasionAndCB-PreamblesPerSSB = eight​

​![[ssb_rach_mapping_pat3.png]]

d.Case 4:​

msg1-FDM = two, ssb-perRACH-OccasionAndCB-PreamblesPerSSB = oneHalf​
![[ssb_rach_mapping_pat4.png]]
​

e.These above diagram do not show CB-PreamblesPerSSB values and also do 

not mapp them. It is TODO for us to generate more exhaustive examples. 

f.For reference:​

​![[beammapping_ssb_rach.png]]

SECTION 7 RRC Message 

​![[rach_config_rrc.png]]

​

​

Extra:​

You have hit exactly on the core mathematical paradox of Zadoff-Chu (ZC) sequences. Your 

intuition about the phase rotation is 100% correct, but the reason it turns into a physical 

time-shift is due to a unique property called Time-Frequency Dualism (or Ambiguity). [1, 2] 

Part 1: How Frequency Offset Transforms into a Time-Domain Shift 

Let’s mathematically bridge the gap between your phase rotation (\(\Delta f \cdot n\)) and a 

physical sample shift (\(t+1\)). 

A Zadoff-Chu sequence of length \(N\) and root index \(u\) has a quadratic phase profile:​

\(x(n)=e^{-j\frac{\pi un(n+1)}{N}}\) 

When a frequency offset \(\Delta f\) occurs, it multiplies the sequence by a linear phase 

ramp over time, exactly as you stated:​

\(y(n)=x(n)\cdot e^{j\frac{2\pi \Delta f\cdot n}{N}}\) 

If we substitute \(x(n)\) into this equation:​

\(y(n)=e^{-j\frac{\pi un(n+1)}{N}}\cdot e^{j\frac{2\pi \Delta f\cdot n}{N}}\) 

Here is the magic trick of ZC math. Through algebraic manipulation, you can factor out a 

term to force the linear phase back into the quadratic exponent. When you do that, the 

equation transforms into:​

\(y(n)\approx x(n-d_{offset})\) 

Where the pseudo-time shift \(d_{offset}\) is given by:​

\(d_{offset}=-\Delta f\cdot u^{-1}\mathinner{\;\left(\mod \,N\right)}\)​

(where \(u^{-1}\) is the modular inverse of the root index \(u\) modulo \(N\)). 

What this means in the Time Domain: 

Because a ZC sequence is a chirp (its frequency changes linearly with time), shifting its 

frequency is mathematically identical to shifting it in time. 

When the gNB passes \(y(n)\) through its matched filter, the filter doesn’t see a smeared 

correlation or a phase-rotated mess. The correlation remains nearly perfect, but the peak is 

physically displaced to a new sample location (\(t + d_{offset}\)). The gNB cannot natively 

differentiate whether the UE is far away (true delay) or moving exceptionally fast (Doppler 

shift). [1] 

Part 2: Do we have to test all 64 preambles? 

No, the gNB does not have to explicitly calculate or search for the frequency offset across 

all 64 preambles individually. 

Here is how the system handles it efficiently: 

1.The 64 Preambles are just Cyclic Shifts: The 64 preambles in a cell are not 

completely unique sequences; they are just different cyclic shifts (\(N_{CS}\)) of the 

same root sequence. The gNB performs only one massive correlation per root 

sequence using an IFFT-based matched filter. This single correlation maps the energy 

of all 64 preambles onto a time-domain grid simultaneously. [1] 

2.Why the Restricted Set fixes it: Because the gNB knows its own root index (\(u\)) and 

the maximum expected Doppler offset for high-speed trains (e.g., up to 500 km/h), it 

can calculate exactly what the shift \(d_{offset}\) will be. 

3.Ghost Zones: If Pre Preamble \(A\) gets shifted by \(d_{offset}\) samples due to 

Doppler, it will land exactly where Preamble \(B\)’s peak would normally sit. 

4.The "Restriction": Instead of attempting to calculate the exact frequency offset for 

every user, the gNB simply forbids the cell from using Preamble \(B\). The network 

engineer flags those overlapping shifts as "Restricted" (invalid). 

Summary 

The gNB relies on Preamble Exclusion rather than trying to calculate the phase rotation for 

every preamble. It defines wide "safe zones" around each valid peak. If a peak shifts due to 

Doppler, it moves within its designated buffer zone, allowing the gNB to correctly capture it 

without it bleeding into a neighboring preamble's territory. 

If you want to look closer at the implementation, we can discuss: 

•How the modular inverse (\(u^{-1}\)) dictates whether the peak shifts forward or 

backward. 

•The exact difference between Restricted Set Type A and Type B. 

suppose the gNB configure first preamble with 0 circular shift and next preamble with 10 

circular shift. If UE attempts first preamble and gNb receives peak at 5, how does gNB know 

how much of this 5 is due to doppler and how much it is because of actual time delay?​

​

In this exact scenario, if the gNB receives a peak at 5, the gNB cannot distinguish how much 

of that 5 is due to Doppler and how much is due to actual time delay. 

This is the exact reason why the Restricted Set exists. It doesn't break the physical 

ambiguity; it simply creates a strict logical framework where the gNB doesn't need to guess. 

Here is exactly how the gNB resolves this ambiguity in practice: 

