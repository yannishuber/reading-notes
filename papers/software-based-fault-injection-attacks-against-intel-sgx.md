# Plundervolt: Software-based Fault Injection Attacks against Intel SGX [[PDF](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=9152636)]

*K. Murdock, D. Oswald, F. D. Garcia, J. Van Bulck, D. Gruss, & F. Piessens (2020). Plundervolt: Software-based Fault Injection Attacks against Intel SGX. In 2020 IEEE Symposium on Security and Privacy (SP) (pp. 1466-1482).*

**This paper review was written as an exercise.**

## Paper summary

Attacks on main memory, such as Rowhammer has led Intel to consider main memory as an untrusted storage facility. To solve this, Intel introduced Software Guard Extensions which can be used to execute sensitive code inside a secure enclave. SGX uses authentication and encryption of the memory to guarantee the integrity of the computation.

The authors of this paper show how they used the power optimization interface available on Intel CPUs to introduce faults in SGX enclaves by regulating the CPU voltage. The power optimization interface is used to reduce power consumptions of CPUs in data centres or mobile devices. The authors used the interface to supply the CPU with an unsafe but high enough voltage to introduce faults in SGX enclaves without crashing the system.

The main challenge for this attack is to find the right undervoltage which produces faults in computations without crashing the system. The authors document the effectiveness of their attack on various cryptographic libraries available in the Intel SGX SDK. They produce faults during cryptographic computations, which can be used to recover the private keys using already existing methods. The authors also show how plundervolt can cause memory safety misbehaviours of any code running in an SGX enclave using faulty array indexes or faulty memory allocations.

The paper mostly concentrates on faults during multiplication operation since it is the operation most vulnerable to this type of attack according to the authors. Possible mitigation for this attack is to disable the power optimization interface, scale back the voltage during enclave operations or software-level hardening. Intel, which has been made aware of the vulnerability has chosen to disable the mailbox interface to disallow undervoltage.

## Strengths

+ Importance

## Weaknesses

- Novelty
- CPU models are cherry-picked
- Motivation for the different choices
- Precision

## Discussion

**Importance:** with this vulnerability the authors show that even platforms such as SGX (which are advertised secure and trusted) can be vulnerable to attacks. The vulnerability that the authors found is of high importance since a lot of companies rely on SGX as a trusted platform to execute code.

**Novelty:** The attack seems very similar to CLKscrew and the authors even mention it multiple times. To me it seems that they did not much more than porting the existing attack from the ARM TrustZone to Intel SGX with only minor changes.

**Motivation & Precision:** There are a lot of choices that are not motivated by the authors. For example, Table 1 shows a list of processors on which they tested if SGX was vulnerable to Plundervolt. However, according to the table the E5-1630V4 does not even have SGX so it seems rather useless to include it in this list. Furthermore, throughout the paper they do not analyse their results on all platform and instead cherry-pick some CPUs from the list (sometimes the conduct the experiment on the i7-8650U-A, and other times on the i3-7100U-B without motivating their choice).

In Section III a) the authors mention that they investigated with non-SGX code but the section's title is "Faulting In-Enclave Multiplications" so it is not very clear if the results are from tests performed in-enclave or not. Also it is not clear why they would investigate on normal code, which in the SGX threat model is untrusted anyway. Still in III-A "all most significant bits flip" it is not very clear which bits are concerned by that.
