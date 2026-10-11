# TechNova Network Lab

Cisco Packet Tracer lab for a multi-floor company network. Coursework
assignment (jointly assigned with a teammate) — designed, built, and
documented entirely by me. I later taught my teammate to rebuild it
from scratch.

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
- `show vlan brief`, `show interfaces trunk` → VLANs assigned,
  trunk allowing 10,20,30,40,50,60
- ICMP ping inside each VLAN
- ICMP ping across VLANs (inter-VLAN routing works)
- ICMP ping R1 → R2 (RIP v2 routes learned)
- SSH login from a management host

## What I'd do differently — the /29 lesson

I divided every VLAN using /29 subnets — 8 addresses, 6 usable. The
current devices fit:

| VLAN | Department | Devices | Fits in /29? |
|---|---|---|---|
| 10 | Administration | 2 | Yes |
| 20 | HR | 1 | Yes |
| 30 | Finance | 1 | Yes |
| 40 | Management | 1 | Yes |
| 50 | Development | 3 | Yes |
| 60 | Server Operations | 1 | Yes |

But there is **zero growth headroom.** Development already has 3 of
its 6 usable addresses used. If the department hires one more person
next month, that host cannot get an IP without renumbering the VLAN.

The right design starts from host count per department *plus* a
growth margin, and uses VLSM — variable-length masks — so each subnet
is sized to its department. FLSM /29 was a shortcut that passed the
rubric but would fail in a real office.

This was the single most valuable mistake in the project.

## What Packet Tracer simulated

Packet Tracer is a simulator. It does not model real CPU load, real
cable faults, or real switch ASIC behavior. All results here are
simulation results.

## Tools

Cisco Packet Tracer 8.x.

## License

MIT.