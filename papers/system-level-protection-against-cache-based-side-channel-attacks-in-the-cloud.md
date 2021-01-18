# StealthMem: System-Level Protection Against Cache-Based Side Channel Attacks in the Cloud [[PDF](https://www.usenix.org/system/files/conference/usenixsecurity12/sec12-final79.pdf)]

*Taesoo Kim, Marcus Peinado, & Gloria Mainar-Ruiz (2012). STEALTHMEM: System-Level Protection Against Cache-Based Side Channel Attacks in the Cloud. In 21st USENIX Security Symposium (USENIX Security 12) (pp. 189–204). USENIX Association.*

**This paper review was written as an exercise.**

## Paper summary 

This paper proposes a system-level protection mechanism against cache-based side-channel attacks in the cloud, StealthMem. To offer cost-efficient, scalable and high availability computing services, cloud providers share physical resources (the paper focuses on CPU) among multiple clients of their cloud platforms. This exposes mainly two cache-based side-channel attacks that can be exploited by any of the untrusted tenants sharing a physical CPU. The first side-channel attacks are active time-driven cache attacks where the attacker directly manipulates the cache state which is shared with the victim and precisely times the victim's execution. Secondly, there are trace-driven attacks where the attacker primes the cache with some controlled memory and analyzes the state of the cache after the victim's execution.

StealthMem mitigates both types of attacks by "locking" specific cache lines in the Last-Level Cache, preventing them from being evicted. Unlike standard page coloring, StealthMem does not sacrifices the utilization of the cache, by exploiting set associativity. StealthMem is implemented as an extension of a hypervisor and allocates "stealth memory" pages to each core in the CPU. The stealth pages are selected from a distinct pre-image set in memory for each processor and are never evicted from the cache. These memory pages should be used to store sensitive data like the S-boxes of block cipher encryption algorithms. The remaining pages of each pre-image set that contains a stealth page are considered dangerous because they could evict a stealth cache line. The first possibility is to not use those pages that could evict our stealth pages. But to still make use of this memory, StealthMem chose to still allocate those pages but monitors their access using Page Table Alerts. When a PTA is triggered (when the page is accessed), the hypervisor can reload the stealth cache lines to ensure they are still present in the cache. The paper also proposes a simplification for PTAs based on the k-LRU cache replacement policy.

Evaluation using the SPEC2006 CPU benchmark suite shows that the overhead introduced by StealthMem is caused mainly by the fact that the use of large pages is prevented. To demonstrate this they compared StealthMem with a baseline system with and without large pages. They have shown that the performance overhead over the baseline system is of about 5.9% and only 1% over the baseline system without the use of large pages.

They have also shown that their solution can be integrated easily to existing software with minimal lines of code. The number of lines of code changed is 5 lines for DES, 34 lines for AES and 3 lines for Blowfish. The overhead for each of these block cipher encryption algorithms is respectively 3% for DES, 5% for AES and 2% for Blowfish.

## Strengths

+ Relevant area of research with the rise of cloud-based solutions where hardware is shared across multiple untrusted clients.
+ Easy to implement in existing software (couple lines of code).
+ StealthMem can be deployed on commodity hardware already present in today’s data centers.
+ The evaluation is complete, measuring the overhead not only with benchmarks but also in real encryption algorithms. The solution is compaired with page coloring in a fair way. 

## Weaknesses

- It is not very clear if StealthMem also mitigates attacks on L1 and L2 caches (in the case of a 3-level cache architecture).
- The performance evaluation seems to omit certain benchmarks from the SPEC2006 benchmark suite (13 presented out of 31).
- The fact that StealthMem does not allow large pages is only analyzed from a performance point of view but it would be interesting to see if it could cause new attack vectors for example on the TLB?

## Discussion

**Relevance:** Unlike hardware mitigations to cache attacks that are expensive to develop and take a long time before being implemented in new CPUs and even longer before those CPUs are used in data centers, software mitigations like StealthMem provide a much simpler and quicker way to protect against those attacks. StealthMem is particularly interesting since the idea behind is fairly straightforward and the overhead is reasonable.

**Mitigation on L1 and L2:** The paper mentions in 2.2.2 that it is possible perform cache attacks on L1 caches, however, in the rest of the document it is only briefly mentioned in 4.1 that stealth pages are also loaded in L1 and L2 during context switching but does not mention many other details about the effectiveness of StealthMem in L1/L2 caches.

**SPEC2006 CPU benchmark suite:** According to the SPEC2006 CPU benchmark suite documentation there are 31 different benchmarks, the paper does not state why they only selected 13 of them.

**Lack of large pages:** The reason why large pages cannot be used with StealthMem is clearly stated in the paper and the authors even measured the performance overhead produced by this. However, I think that there could be more to it than just a performance overhead. There are already papers discussing attacks on the TLB and it would be interesting to analyze the impact of StealthMem in those attacks.

Globally, I think the paper is well written and easy to read. The authors explain and motivate their choices.