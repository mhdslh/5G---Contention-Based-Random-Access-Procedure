
This program simulates contention based random access procedure in 5G networks. More details are provided below.

	>>> For Random Access Preamble (MSG1):
	 1- totalNumberofRA-Preambles = 64
	 2- ssb-perRACH-Occasion = 1 
	 3- CB-PreamblesPerSSB = 64
	 4- groupBconfigured is not included in RACH-ConfigCommon
	 1, 2, 3, 4 -> For contention based random access, 64 preambles are associated with each SS/PBCH beam
	 5- Simulate contention based random access procedure in one PRACH occasion 	
	
	>>> For Random Access Response (MSG2)
	 6- Base station is able to differentiate the PRACH transmissions of two UEs which select the same preamble if they appear 
	   distinctly apart in the PDP, i.e., detect collision
	 7- delta = tau * c, where tau denotes delay spread and c = 3*10^8[m/s]
	 8- d_i denotes the distance between user i and the BS
	 6, 7, 8 -> collision occuers if exists i and j such that |d_i-d_j|>delta
	 9- tau = 0.3[us] -> delta = 90[m]
	10- RAR is scheduled on the PDSCH and indicated on the PDCCH using RA-RNTI
	11- RA-RNTI is calculated based upon time domain and frequency domain indices associated with the PRACH occasion
	12- Thus, all UE using the same PRACH occasion decode the same PDCCH and the same corresponding PDSCH
	13- Assume DCI Format 1_0 is received before RAR window expires 
	14- No UE requests for On-demand System Information
	15- RAR provides the UE with a Timing Advance command, an Uplink Grant for MSG3 transmission, a Temporary C-RNTI (TC-RNTI)
	16- TC-RNTI(16 bits) is used to address the UE on the PDCCH when subsequently allocating PDSCH resources for MSG4
	17- In this program, we set MSB in TC-RNTI to 1  and 6 LSB to RAPID. MSB is set to 1 since 0000 is not a valid TC-RNTI.
	
	>>> For Scheduled Transmission (MSG3):
	18- UE proceeds to transmit MSG3 on the PUSCH using the allocated resources and at the calculated transmit power
	19- If contention has occured and multiple UE have decoded the same uplink resource allocation then all those UE will transmit
	    their MSG3 using the same PUSCH resources
	20- For MSG3 transmission, assume all devices employ conventional power control scheme  (fully compensate path loss)
	21- Thus, received power at the BS is the same for all devices
	22- Assume all UEs transfer CCCH message for MSG3, and CCCH messages of all UEs are for RRCSetupRequest.
	23- Value of Logical Channel Identity in MAC header is 52 to indicate a CCCH message with a length of 48 bits is transmitted
	24- RRCSetupRequest (6 bytes) includes a UE identity which can be used for contention resolution (MSG4)
	25- UE identity (39 bits) is a random value  
	26- In this program, we assume other bits in CCCH message are zero
	27- We consider QPSK transmission for MSG3, where
	    00 ->  1 + 1 * I
	    01 -> -1 + 1 * I
	    11 -> -1 - 1 * I
	    10 ->  1 - 1 * I
	28- Structure of CCCH Message = R | R | LCID | CCCH SDU
	29- In this program, we focus on trasferring CCCH SDU
	30- When there is no collision over preamble i, BS allocates an uplink resource on the PUSCH for the transmission of MSG3 
	31- In this program, we have allocated uplink resources even when collision is detected in MSG2
	32- However, no UE uses these uplink resources since these preambles have no subheader in RAR
	
	>>> For Contention Resolution (MSG4):
	33- Assume, UE decodes a DCI Format 1_0 which allocates PDSCH resources for MSG4 (we skip this step)
	34- MSG4 includes the "UE Contention Resolution Identity" MAC Control Element
	35- This MAC Control Element includes the first 48 bits belonging to the uplink CCCH SDU within MSG3
	36- UE uses this MAC Control Element to determine whether or not the Random Access procedure has been successful
	37- Procedure is successful if the content of the MAC Control Element matches the content of the CCCH SDU transmitted by UE
	38- Otherwise, it means that contention has occurred and the MAC Control Element is intended for a different UE
	39- If contention resoultion is successful, the TC-RNTI becomes the allocated C-RNTI
	40- In addition, the UE uses the PUCCH to transmit a HARQ acknowledgement for MSG4
	
