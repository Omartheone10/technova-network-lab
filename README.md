# TechNova Network Lab

Cisco Packet Tracer lab for a multi-floor company network. Coursework
assignment (jointly assigned with a teammate) — designed, built, and
documented entirely by me.

## Scenario

TechNova Solutions is a multi-floor office with six departments:
Administration, HR, Finance, Management, Development, and Server
Operations. The company has a small branch office that must
communicate with the main office via dynamic routing. Each department
is isolated into its own VLAN, with controlled inter-VLAN
communication. Management requires access to all departments and
servers.

## Topology

- 2 routers, 2 switches, 11 end devices
- Main office: R1 + SW1 + 6 VLANs on router-on-a-stick
- Branch office: R2 + SW2 + 2 hosts
- WAN link between R1 and R2 running RIP v2

## What I built

- **VLANs:** 10 Administration, 20 HR, 30 Finance, 40 Management,
  50 Development, 60 Server Operations
- **Inter-VLAN routing:** router-on-a-stick (6 subinterfaces on R1)
- **Trunking:** SW1 Gi0/1, allowed VLANs 10,20,30,40,50,60
- **Routing:** RIP v2 between R1 and R2
- **Addressing:** /29 FLSM, one subnet per VLAN + one for the branch
  WAN link
- **SSH:** enabled on R1, R2, SW1 (`transport input ssh` on vty)
- **Hardening:** `banner motd`, `service password-encryption`,
  `enable secret`, port-security with sticky MACs, unused ports
  administratively shut down

## Verified

- `show ip ssh` → SSH Enabled, version 1.99
- `show ip interface brief` → active ports up, unused ports
  administratively down

## What I'd do differently — the /29 lesson

I divided every VLAN using /29 subnets — 8 addresses, 6 usable. One
usable address goes to the router subinterface gateway. That leaves
**5 usable hosts per VLAN**, no matter how many the department has.

| VLAN | Department | Devices | Usable |
|---|---|---|---|
| 10 | Administration | 2 | 5 |
| 20 | HR | 1 | 5 |
| 30 | Finance | 1 | 5 |
| 40 | Management | 1 | 5 |
| 50 | Development | 3 | 5 |
| 60 | Server Operations | 1 | 5 |

Development is at 3 of 5. Two more hires and the VLAN is full.

The right design starts from host count per department plus a growth
margin and uses VLSM — variable-length masks — so each subnet is
sized to its department. FLSM /29 was a shortcut that fit the
assignment but would fail in a real office the moment one department
grew.

This was the single most valuable mistake in the project.

## What Packet Tracer simulated

Packet Tracer is a simulator. It does not model real CPU load, real
cable faults, or real switch ASIC behavior. All results here are
simulation results.

## Tools

Cisco Packet Tracer.