# References

*[← 12 — Conclusion & Future Work](12_Development_Journey.md) · [README](../README.md)*

---

## ASCON

- Dobraunig, C., Eichlseder, M., Mendel, F., Schläffer, M. — *Ascon v1.2: Lightweight Authenticated Encryption and Hashing*. Journal of Cryptology, 2021. The core specification underlying the state layout, permutation, and initialization procedure used throughout [Chapter 3](03_ASCON_Architecture.md) and [Chapter 8](08_Leakage_Model.md).
- NIST — *Lightweight Cryptography Standardization Process*. National Institute of Standards and Technology. Documents the multi-year process that selected ASCON as the LWC standard; see [nist.gov/programs-projects/lightweight-cryptography](https://csrc.nist.gov/projects/lightweight-cryptography) for the current standardization status and publications.
- ASCON team — Official reference materials and specification documents. [ascon.iaik.tugraz.at](https://ascon.iaik.tugraz.at/)
- ASCON SimpleSerial firmware (reference and protected implementations used and adapted in this project). [github.com/ascon/simpleserial-ascon](https://github.com/ascon/simpleserial-ascon)

## Side-Channel Analysis and CPA

- Kocher, P., Jaffe, J., Jun, B. — *Differential Power Analysis*. CRYPTO 1999. The foundational DPA paper motivating the statistical framing developed in [Chapter 4](04_CPA_Theory.md).
- Brier, E., Clavier, C., Olivier, F. — *Correlation Power Analysis with a Leakage Model*. CHES 2004. Introduces the Pearson-correlation-based CPA distinguisher used throughout this repository.
- Mangard, S., Oswald, E., Popp, T. — *Power Analysis Attacks: Revealing the Secrets of Smart Cards*. Springer, 2007. A standard reference for leakage models (Hamming Weight/Distance), trace preprocessing, and general SCA methodology referenced in [Chapter 2](02_Background.md) and [Chapter 4](04_CPA_Theory.md).

## Hardware and Tooling

- NewAE Technology Inc. — *ChipWhisperer* documentation and hardware. [chipwhisperer.readthedocs.io](https://chipwhisperer.readthedocs.io) and [github.com/newaetech/chipwhisperer](https://github.com/newaetech/chipwhisperer). Source for the ChipWhisperer Nano platform, SimpleSerial protocol, and Python acquisition API used in [Chapter 5](05_Experimental_Setup.md) and [Chapter 6](06_Firmware_Modifications.md).
- STMicroelectronics — STM32F0 series reference manual and datasheets, describing the Cortex-M0 target used throughout acquisition.

## Software

- NumPy — vectorized numerical computation used for hypothesis matrix generation and Pearson correlation ([Chapter 9](09_CPA_Attack.md)). [numpy.org](https://numpy.org)
- Matplotlib — all figures in this repository (`figures/`) were generated with Matplotlib. [matplotlib.org](https://matplotlib.org)

---

*Have a reference to add? Open a pull request or issue against this repository.*
