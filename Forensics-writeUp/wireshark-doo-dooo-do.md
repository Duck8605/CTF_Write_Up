# Wireshark doo dooo do doo

## Overview

Points: 50
Category: Forensics

## Description

Can you find the flag? [shark1.pcapng]([./shark1.pcapng](https://mercury.picoctf.net/static/81c7862241faf4a48bd64a858392c92b/shark1.pcapng)).

## Approach

I opened [shark1.pcapng]([./shark1.pcapng](https://mercury.picoctf.net/static/81c7862241faf4a48bd64a858392c92b/shark1.pcapng)) with [Wireshark](https://www.wireshark.org/).
I followed the TCP stream:

![image](https://github.com/user-attachments/assets/f98af362-0203-40a2-808c-56fc26701500)

Stream 5 (`tcp.stream eq 5`) contained something that looked promising
> Gur synt vf cvpbPGS{c33xno00_1_f33_h_qrnqorrs}

After decoding that with [ROT13](https://rot13.com/), the flag was revealed.

## Flag

picoCTF{p33kab00_1_s33_u_deadbeef}
