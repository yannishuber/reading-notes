# Exploiting Correcting Codes: On the Effectiveness of ECC Memory Against Rowhammer Attacks [[PDF](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8835222)]

*L. Cojocar, K. Razavi, C. Giuffrida, & H. Bos (2019). Exploiting Correcting Codes: On the Effectiveness of ECC Memory Against Rowhammer Attacks. In 2019 IEEE Symposium on Security and Privacy (SP) (pp. 55-71).*

**This paper review was written as an exercise.**

## Paper summary

Rowhammer attacks are widespread and affect almost every device from mobile phones to cloud servers. Past research shows that bit flips can be caused by hammering DRAM rows on DDR3 and also DDR4 memory. It is common belief that the addition of Error Correcting Codes to memory modules would reduce Rowhammer attacks to a denial-of-service vulnerability. However, the authors of this paper have shown that Rowhammer attacks can still be used against ECC memory to produce data-controlled bit flips. To achieve this, they present a novel side channel in the memory controller that can be used to detect bit flips despite that the ECC memory controller corrected them. Ultimately, to reproduce existing exploit the authors present ECCploit which allows practical Rowhammer exploits on ECC memory such as PTE corruption or Opcode modification among others.

The paper underlines 3 main challenges, I) reverse engineer the unknown ECC functions II) trigger Rowhammer corruptions on ECC systems while avoiding crashing the system III) how to use Rowhammer-based corruptions for multiple bits at a time.

For the first challenge, they show different methods to inject controlled faults into the system to reverse engineer the ECC functions. Controlled fault injection on ECC memory can be done physically by short-circuiting pins of the DDR DIMM or even by using Rowhammer to trigger bit flips and observing the side channel caused by the change in response time of the memory controller during error correction. They also show an interesting way of recovering ECC functions by using a cold-boot attack to increase the retention time of the DDR module and allowing them to swap the DIMM to another computer and recover the ECC bits.

The authors solved the second challenge by using their reverse engineered ECC functions to produce bit flips which do not get detected by the memory controller. This is done again by exploiting the timing difference due to error correction and carefully chosen data patterns to control the bit flips precisely.

The last challenge is solved by applying all the knowledge presented in the paper which combined produce ECCploit. ECCploit consists of three phases, resulting in practical Rowhammer attacks.

This paper shows that while Rowhammer exploits are harder on ECC memory, they still are a realistic and worrying threat.


## Strengths

+ The work is very innovative and shows that, contrary to popular belief, ECC memory still is vulnerable to Rowhammer attacks.
+ Novel side channel exploiting timing differences caused by error corrections.

## Weaknesses

- DDR4 is only shortly mentioned. However it would be interesting to include DDR4 modules in the evaluation for comparison.
- ECC memory is often used on embedded systems, therefore it would be nice to include an ARM system.

## Discussion

I particularly liked the reverse engineering approach that the authors took using the cold boot attack to retrieve the ECC functions. Cold boot attacks have been used before to dump memory from a victim DRAM module but using it with a custom FPGA-based memory controller to read ECC control bits is especially novel. The paper gives all the necessary theoretical background concerning the multiple error correction codes that can be found in ECC DRAM modules like SECDED or chipkill.

It would have been nice if the authors had investigated the negative peaks found in the timing differences of error corrections. These peaks are very counter-intuitive and it is not very clear if they are observed on all tested systems.

Finally, since ECC memory is increasingly used in embedded systems to allow powering the systems below a "safe" level it would have been nice to add an ARM system to the tests.