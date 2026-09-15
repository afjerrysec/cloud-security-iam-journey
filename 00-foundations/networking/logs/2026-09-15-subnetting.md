# Subnetting

# IPv4 Subnetting

# Subnetting is the process of dividing a larger IP network into smaller logical networks called **subnets**. It allows networks to use IP addresses more efficiently and provides better organization, routing, isolation, and security.

# Subnetting is especially important in cloud environments, where networks such as AWS VPCs and Azure VNets are divided into multiple subnets.

---

## 1. IPv4 Address Structure

An IPv4 address is **32 bits**, divided into four 8-bit octets.

Example:

192.168.1.10

11000000.10101000.00000001.00001010


An IPv4 address consists of:

Network portion | Host portion

The subnet mask/prefix determines where the network portion ends and the host portion begins.

---

## 2. CIDR Notation

CIDR (Classless Inter-Domain Routing) represents the network prefix using `/number`.

Example:

192.168.1.0/24

`/24` means the first **24 bits** are network bits, leaving:

32 - 24 = 8 host bits

The equivalent subnet mask is:

255.255.255.0

Common prefixes:

| CIDR | Subnet Mask     | Host Bits | Total Addresses |
| ---- | --------------- | --------: | --------------: |
| /16  | 255.255.0.0     |        16 |          65,536 |
| /20  | 255.255.240.0   |        12 |           4,096 |
| /24  | 255.255.255.0   |         8 |             256 |
| /25  | 255.255.255.128 |         7 |             128 |
| /26  | 255.255.255.192 |         6 |              64 |
| /27  | 255.255.255.224 |         5 |              32 |
| /28  | 255.255.255.240 |         4 |              16 |
| /30  | 255.255.255.252 |         2 |               4 |

---

## 3. Network, Host, and Broadcast Addresses

Consider:

192.168.1.0/24

The addresses are:

Network address:    192.168.1.0
Usable range:      192.168.1.1 - 192.168.1.254
Broadcast address: 192.168.1.255

In traditional IPv4 subnetting:

Usable hosts = 2^host_bits - 2

For `/24`:

2^8 - 2 = 254 usable hosts

The two excluded addresses are the **network address** and **broadcast address**.

---

## 4. Calculating the Number of Subnets

When you borrow host bits to create subnets:

Number of subnets = 2^n

where `n` is the number of borrowed bits.

### Example

You have:

192.168.1.0/24

and need **4 subnets**.

2^1 = 2   ❌
2^2 = 4   ✅

Therefore, borrow **2 bits**:

/24 + 2 = /26

You now have:

4 subnets
64 addresses per subnet
62 traditionally usable host addresses per subnet

---

## 5. Calculating Required Host Bits

If you know how many hosts you need in each subnet, determine how many host bits are required.

Formula:

2^h - 2 >= required hosts

### Example

You need at least **50 usable hosts**.

2^5 - 2 = 30   ❌
2^6 - 2 = 62   ✅


Therefore, you need **6 host bits**.

Since IPv4 has 32 bits:

32 - 6 = /26

So `/26` provides 62 traditionally usable IPv4 addresses.

---

## 6. Finding Subnet Ranges

Example:

192.168.1.0/26

A `/26` has:

256 / 4 = 64 addresses per subnet

Therefore, the subnet ranges increment by **64**

192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26

For the first subnet:

Network:    192.168.1.0
Usable:     192.168.1.1 - 192.168.1.62
Broadcast:  192.168.1.63

For the second:

Network:    192.168.1.64
Usable:     192.168.1.65 - 192.168.1.126
Broadcast:  192.168.1.127

---

## 7. The Block Size

The **block size** helps quickly determine where subnets begin and end.

For a subnet mask where the interesting octet is not `255` or `0`:

Block size = 256 - subnet mask value

Example:

/26 = 255.255.255.192

Therefore:

256 - 192 = 64

The subnets increment by 64:

0
64
128
192

Another example:

/27 = 255.255.255.224

256 - 224 = 32


So:

0
32
64
96
128
160
192
224

---

## 8. VLSM

**Variable Length Subnet Masking (VLSM)** allows different subnets within the same network to have different sizes.

Instead of giving every subnet the same number of addresses, allocate addresses based on requirements.

Example:

Large application subnet → /24
Medium application subnet → /26
Small management subnet → /28


VLSM helps reduce wasted IP addresses.

---

## 9. Private IPv4 Address Ranges

The following IPv4 ranges are reserved for private networks:

10.0.0.0/8
172.16.0.0/12
192.168.0.0/16

These addresses are commonly used inside:

* Home networks
* Corporate networks
* Cloud VPCs/VNets
* Internal applications

Private addresses are not directly routable across the public Internet.

---

## 10. Public vs Private IP Addresses

**Private IP:**

Used for internal communication.

10.0.1.10


**Public IP:**

Used for communication across the public Internet.

203.0.113.10

A cloud architecture will often use private IPs for internal resources and public IPs only where external connectivity is required.

---

## 11. Default Gateway

A **default gateway** is the router a host uses when the destination is outside its local subnet.

Example:

Client:
10.0.1.10/24

Gateway:
10.0.1.1

If the client wants to communicate with:

10.0.2.10

it recognizes that `10.0.2.10` is outside its local `/24` network and sends the traffic to its default gateway.

---

## 12. Subnetting and Routing

Subnetting divides networks; routing determines how traffic moves between them.

Example:


              Router
             /      \
            /        \
   10.0.1.0/24     10.0.2.0/24
       │                │
   Web Servers      App Servers


The router needs appropriate routes to allow traffic between the two subnets.

This becomes extremely important in cloud environments because **subnets, route tables, gateways, and security controls work together**.

---

## 13. Subnetting and Security

Subnetting can be used to create network boundaries.

For example:

VPC: 10.0.0.0/16

├── Public subnet
│   └── Load balancer
│
├── Private application subnet
│   └── Application servers
│
└── Private database subnet
    └── Database

Security controls can then restrict which subnets are allowed to communicate.

For example:

Internet
   ↓
Load Balancer
   ↓
Application Subnet
   ↓
Database Subnet

The database does not necessarily need direct Internet access.

---

## 14. Important Subnetting Formulas

### Number of subnets

2^n

`n` = number of borrowed bits.

### Total addresses per subnet

2^h

`h` = number of host bits.

### Traditionally usable IPv4 addresses

2^h - 2

### Prefix length

32 - host bits = prefix length

### Block size

256 - interesting octet of subnet mask

---

## Key Takeaways

Subnetting is fundamentally about **dividing an IP address space into smaller networks**.

Remember these concepts:

CIDR
↓
Network bits + Host bits
↓
Subnet mask
↓
Subnet size
↓
Network address
↓
Usable host range
↓
Broadcast address

For cloud security, subnetting is particularly important because it forms the foundation for **network segmentation, routing, access control, public/private architectures, and isolation of cloud resources**.
