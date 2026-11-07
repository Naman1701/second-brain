
## Block Diagram
	
   ![[pusch_tb_processing.jpg]]

---
## Transport Block CRC attachment:

![[pusch_crc_processing.png| 400]]


---
## LDPC base graph selection

Base graph is selected based on two parameters: transport block size A and coding rate R.

![[pusch_base_graph_selection.png| 400]]

---

## Code block segmentation

if transport block size $B$ is greater than maximum size of a code block $K_{cb}$ then the transport block is divided into multiple code blocks and a separate CRC is attached to each code block. Below is the procedure to divide transport block into multiple code blocks. 

1. **Determine the max size of the code block ($K_{cb}$)** : 
	1. For LDPC base graph type 1 : $K_{cb}$ = 8448
	2. For LDPC base graph type 2 : $K_{cb}$ = 3840
2. **Determine the number of Code blocks**
   ```C
   if B(Transport block size) < Kcb(Max Codeblock size)
	{	
		L = 0;  // no code block no additional CRC.
		C = 1;  // number of codeblocks
		B'= B;  // this mean 'No Segmentation'.
	}
	else
	{
	    L = 24;  // extra CRC to be attached to each code block
	    C = Ceiling(B/(Kcb - L)); // Kcb is inclusive of CRC so Kcb - L is effective transport block share, since B is total transport block (A + L), the number of code blocks is ceil of (total / one code block size).
	    B' = B + C * L ; // new size of transport block which needs L crc bits more for each code blocks.
	}
   ```
3.  **Determine the number of bits in each code block**
		we do not send $C - 1$ code blocks with $K_{cb}$ size and the last one with remainder. Instead of this, we distribute the $B'$ in all $C$ code blocks equally. Such that each code block is the size of:
 		$$ K^{`} = \frac{B'}{C}$$
4. **Determine $K_b$** 
	1. Remember that every LDPC code is defied using a base branch with $K_{B}$ length. Following is the value of $K_{B}$ for different base graph and transport block size $B$ .

		```C
	if (LDPC_graph == base_graph_type_1)
	{	
		Kb = 22
	}
	else // LDPC graph == base_graph_type_2
	{
		if (B > 640)
		{
			Kb = 10
		}
		else if (B > 560)
		{
			Kb = 9
		}
		else if (B > 192)
		{
			Kb = 8
		}
		else
		{
			Kb = 6
		}
	}
		```


5. **Find lifting size $Z_{C}$ and set $K$**

we need to find the minimum value of lifting size $Z$ which satisfies following condition:
$$K_{B} * Z_{C} = K'$$
$Z_{C}$ is the value of $Z$ that satisfies above equation. $K$ is $22Z_{C}$ for base graph 1 and $K$ is $10Z_{C}$ for base graph 2.

6. **Perform Segmentation and add CRC**
	1. We take all $B'$ bits and equally distribute in $C$ code blocks. 
	2. While the size of every code block is $K$, bits filled in each code block is $K'$
	3. $K - K'$ are filler bits we need to fill in each code block.
	4. The below algorithm shows the segmentation procedure. $b\ [\ ]$ is input buffer for transport block.
	5. $c\ [\ ] [\ ]$ is output buffer, first dimensions is code block and second is bit index.
```C
	s = 0  // s = bit position in B (transport block)
	for r = 0 to C-1
	{
		for k = 0 to K'-L-1
		{
			c[r][k] = b[s]
			s = s + 1
		}
		if C > 1  // Do this if the number of the code block is more than 1
		{	
			pr = Calculate_CRC_for_each_codeblock(); //g_CRC24B(D) length L
			for k = K'-L to K'-1  // Append CRC bit
			{
				c[r][k] = pr[k+L-K'];
			}
		}

		for k = K' to K-1  // Insertion of filler bis
		{
			c[r][k] = filler_bit //mostly zero
		}
	}
```

---
## Channel Coding

The [[LDPC]] encoding process takes the segmented code blocks (with attached CRC) as input and generates parity bits based on the chosen base graph and lifting factor. These parity bits are then appended to the original data bits, forming a codeword that is transmitted over the PUSCH.





## Related topics
- [[PDCCH]]
- [[PUCCH]]
- [[PRACH]]
- [[SRS]]
