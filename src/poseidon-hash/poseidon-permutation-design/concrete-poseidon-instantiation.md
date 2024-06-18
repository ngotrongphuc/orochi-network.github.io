# Concrete POSEIDON\\(^\pi\\) instantiation

## Main instance

+ SBox function: The authors proposed using \\(SB(x)=x^5\\) for all use cases
+ With the security level \\(M = 80, 128\\), the size of the Capacity \\(c = 255\\) bits (\\(1\\) field element). And \\(t\\) can be \\(3\\) or \\(5\\) to achieve the *2-to-1* or *4-to-1* compression functions.
+ Psudeonumber generation: The paper uses ***Grain LFSR*** in self-shrinking mode. This can be used to generate the round constants and matrices. This usage can be reminiscent to [nothing-up-my-sleeve numbers](https://en.wikipedia.org/wiki/Nothing-up-my-sleeve_number).
+ MDS matrix: It is recommended that we should use the Cauchy matrix for the linear layer, which described in [Hades-based permutation design](./hades-based-design.md).

## The grain LFSR
The grain LFSR is used to generate pseudorandom numbers for the round constants and the MDS matrices described in [Hades design strategy](./hades-design-strategy.md). The technical details of the LFSR is provided in Appendix E of {{#cite GKRRS21}}. The state in Grain LFSR is \\(80\\) bits in size and is computed as follows:
1. Initialize the state with \\(80\\) bits \\(\\{b_0,b_1,\cdots,b_{79}\\}\\) as follows:
    - \\(b_0,b_1\\): describe the field.
    - \\(b_2,b_3,b_4,b_5\\): describe the SBox.
    - \\(b_6 \rightarrow b_{17}\\): binary representation of \\(n\\).
    - \\(b_{18} \rightarrow b_{29}\\): binary representation of \\(t\\).
    - \\(b_{30} \rightarrow b_{39}\\): binary representation of \\(R_F\\).
    - \\(b_{40} \rightarrow b_{49}\\): binary representation of \\(R_P\\).
    - \\(b_{50} \rightarrow b_{79}\\): set to \\(1\\).
2. Update the bits: $$b_{i + 80} = b_{i + 62} \oplus b_{i + 51} \oplus b_{i + 38} \oplus b_{i + 23} \oplus b_{i + 13} \oplus b_i$$
3. Discard the first \\(160\\) bits.
4. Evaluate bits in pairs: if the first bit is \\(1\\), output the second bit. If it is \\(0\\), discard the second bit.

If a randomly sampled integer is \\(\geq p\\), we discard this value and take the next one. We generate numbers starting from the most significant bit. However, starting from MSB or LSB makes no difference regarding the security

## Choosing number of rounds
Proved in Proposition 5.1, 5.2, and 5.3 in {{#cite GKRRS21}}, this table represents the parameters \\(R_F, R\\) that can protect the construction from statistical and algebraic attacks:



|                Construction                 | \\(R_F\\) | \\(R = R_F + R_P\\)                   |
| ------------------------------------------- | --------- | ------------------------------------- |
| \\(x^5\\)-\\(\mathsf{Poseidon}\\)-\\(128\\) | \\(6\\)   | \\(56 + \lceil \log_5{(t)} \rceil\\)  |
| \\(x^5\\)-\\(\mathsf{Poseidon}\\)-\\(80\\)  | \\(6\\)   | \\(35 + \lceil \log_5{(t)} \rceil\\)  |
| \\(x^5\\)-\\(\mathsf{Poseidon}\\)-\\(256\\) | \\(6\\)   | \\(111 + \lceil \log_5{(t)} \rceil\\) |