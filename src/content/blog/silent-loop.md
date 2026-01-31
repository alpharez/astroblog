---
description: 'A single Cisco IP phone plugged into two switch ports took down our network. Spanning Tree Protocol was running, BPDUs were flowing, and STP should have detected the loop—but it didn''t. The culprit? IEEE 802.1D compliance. Devices that correctly implement the spec filter BPDUs while forwarding all other traffic, creating loops that are invisible to STP but catastrophic to your network.'
pubDate: '2026-01-31'
title: 'The Silent Loop: How a Single IP Phone Can Take Down Your Network'
---

## The Incident

High severity tickets were flooding in—users complaining about network latency and dropped connections. Initial troubleshooting revealed core switches overwhelmed by MAC address flapping. The symptoms were textbook: MAC addresses bouncing between port-channels, intermittent connectivity, and widespread network instability.

We started pulling fiber connections from the switches showing the worst offenders, methodically narrowing down the source. Eventually we traced the primary cause to a single access switch with an IP phone plugged into two separate ports. We shut down one of the ports and the network immediately stabilized.

A single phone, two cables, total network meltdown. But wait—isn't this exactly what Spanning Tree Protocol is supposed to prevent?

## How Spanning Tree Should Work

STP prevents loops by exchanging Bridge Protocol Data Units (BPDUs) between switches. Each switch announces itself, they elect a root bridge, calculate the best paths, and block redundant links. When a switch sees its own BPDUs returning on a different port, it knows there's a loop and blocks one of the paths.

This works beautifully when all devices in the path participate in STP—or at least forward the BPDUs so other devices can detect the loop. The problem arises with a very specific class of device that does neither.

## The IEEE 802.1D Reserved Address Behavior

IEEE 802.1D defines a range of reserved MAC addresses (01:80:C2:00:00:00 through 01:80:C2:00:00:0F) that compliant bridges should process locally and never forward. BPDUs use address 01:80:C2:00:00:00.

Most networking devices fall into predictable categories:

**Managed switches** run STP by default. They send and receive BPDUs, participate in root bridge elections, and block redundant paths. Loops are detected and prevented.

**Dumb hubs** have no intelligence at all—they just repeat electrical signals on all ports. BPDUs flow through them like any other traffic, so upstream switches still see their BPDUs return and can detect the loop.

**Switches with STP disabled** might sound dangerous, but they're actually not the worst case. When STP is disabled, there's no process intercepting BPDUs, so they get flooded like regular multicast frames. Upstream switches still see the loop.

**IP phones with passthrough ports** are the dangerous edge case. They contain a small internal switch that's smart enough to follow IEEE 802.1D's reserved address filtering—sinking BPDUs rather than forwarding them—but not smart enough to participate in STP. They forward all regular traffic while filtering the one thing that would reveal the loop.

## The Anatomy of a Silent Loop

Here's what happened with our phone:

1. The phone has a small internal switch connecting its LAN and PC ports
2. The upstream switch sends a BPDU out port 16 → phone receives it, filters it per 802.1D
3. The upstream switch sends a BPDU out port 44 → phone receives it, filters it
4. The switch never sees its BPDUs return—no loop detected
5. Both ports remain in forwarding state
6. Meanwhile, all data traffic flows freely through the phone between ports
7. Broadcasts multiply exponentially, MAC tables thrash, network dies

The control plane (STP) sees no loop. The data plane has a massive loop. This disconnect is the core of the problem.

## What Devices Actually Cause This?

The dangerous category is narrow but real: devices with multiple Ethernet ports that bridge traffic at Layer 2, filter reserved addresses per spec, but don't run STP.

**Confirmed risks:**

- IP phones with passthrough ports (the cause of our incident)
- Some unmanaged desktop switches (particularly older or very cheap models that implement partial 802.1D compliance)
- Multi-homed print servers or printers with multiple NICs configured to bridge

**Probably safe:**

- Dumb hubs (forward everything, including BPDUs)
- Most managed switches (run STP by default)
- Most modern unmanaged switches (either forward BPDUs or have basic loop detection)

The challenge is that you can't easily tell which category a device falls into without testing or causing an outage. The phone in our incident had been deployed for years without issue—until someone ran a second cable to it.

## Why STP Alone Isn't Enough

STP was designed for a world where bridging devices either participate in the protocol or are completely transparent to it. The IP phone sits in an uncomfortable middle ground: sophisticated enough to filter control traffic, but not sophisticated enough to do anything useful with it.

This isn't a bug in STP—it's working as designed. It's also not really a bug in the phone—it's arguably following the spec correctly. The failure is in the assumption that these two systems would never interact in this way.

## Mitigation Strategies

### Loop Detection (Not Loop Guard)

**Loop Guard** protects against unidirectional link failures where BPDUs stop arriving on a port that was receiving them. It doesn't help here—there were never BPDUs coming from the phone in the first place.

**Loop Detection** is a proprietary feature that sends probe frames and detects if they return on another port. This catches the silent loop because it operates at the data plane level, not the control plane.

Cisco:

```
interface GigabitEthernetX/X/X
  loopback-detection enable
```

FortiSwitch (managed via FortiGate):

```
config switch-controller managed-switch
  edit <switch-id>
    config ports
      edit "portX"
        set loop-guard enabled
      next
    end
  end
end
```

### MAC Move Thresholds

Configure the switch to errdisable ports when excessive MAC flapping is detected:

```
errdisable detect cause mac-flap
errdisable recovery cause mac-flap
errdisable recovery interval 300
mac address-table notification mac-move
```

This won't prevent the loop from forming, but it limits the blast radius and gives you visibility.

### BPDU Guard

BPDU guard won't catch a phone (phones don't send BPDUs), but it catches rogue switches plugged into access ports:

```
interface GigabitEthernetX/X/X
  spanning-tree portfast
  spanning-tree bpduguard enable
```

### Port Security

Limit the number of MAC addresses learned on access ports:

```
interface GigabitEthernetX/X/X
  switchport port-security
  switchport port-security maximum 5
  switchport port-security violation restrict
```

## Recommendations

1. **Enable loop detection on all access ports** — This should be standard policy, not an afterthought
2. **Enable MAC move notifications** — At minimum, get visibility into flapping
3. **Consider errdisable on mac-flap** — Test in your environment first, but this provides automatic remediation
4. **Audit your network** — Look for devices with multiple connections that shouldn't have them
5. **Educate your users and installers** — Make sure people know that "extra cables for redundancy" on end devices isn't redundancy, it's a potential outage

## Conclusion

STP is essential but insufficient. It protects against loops where all devices participate, but it's blind to loops created by devices that filter BPDUs while forwarding data traffic.

IP phones with passthrough ports sit in a particularly dangerous gap—smart enough to follow IEEE reserved address filtering, but not smart enough to participate in STP or detect loops themselves. One phone, two cables, hours of troubleshooting.

The only defense is defense in depth: STP for switch-to-switch loops, proprietary loop detection for the edge cases, and MAC move monitoring to catch what slips through.
