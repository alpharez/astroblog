---
description: I built a network engineering tycoon game. Place routers, route packets, fight cyber threats, and try not to go bankrupt. Everything's broken and it's definitely your fault.
pubDate: '2026-03-01'
title: 'Packet Tycoon: Blame it on the Network'
---

I built a game about the thing I do for a living — and somehow it's fun.

**Packet Tycoon: Blame it on the Network** is a network engineering simulation where you build, route, and defend computer networks using real networking concepts. Not "inspired by" networking. Not "loosely based on" networking. Actual L2/L3 separation, real routing protocols, legitimate congestion mechanics, and cyber threats that behave like real cyber threats.

<iframe frameborder="0" src="https://itch.io/embed/4334382?dark=true" width="552" height="167"><a href="https://steve3279.itch.io/packet-tycoon">Packet Tycoon by steve3279</a></iframe>

## Why This Exists

Every network engineer has stared at a topology diagram and thought, "this would make a great game." The pressure of growing demand, the satisfaction of fixing a bottleneck, the panic when a link goes down and traffic starts rerouting through your backup path that was never meant to handle this much load — that's gameplay. We just didn't have the game yet.

Packet Tycoon fills that gap. It's what happens when you take the dopamine hit of watching green links turn yellow turn red — and then fixing it — and wrap it in a tycoon economy.

## How It Works

You start with a handful of endpoints on a single LAN. Connect them through switches. Watch packets flow. Simple.

Then a second color of endpoint appears. Your switches can't bridge different networks — you need a router. Now you're making routing decisions. Traffic grows. Demand escalates. You're upgrading links from Cat5e to Cat6a to fiber. You're deploying OSPF because static routes can't keep up. You're placing firewalls because script kiddies showed up and they're flooding your links with junk traffic.

The color system is the game's silent tutorial. Same-color endpoints talk through switches (Layer 2). Different colors need routers (Layer 3). No tutorial popup needed — you learn by doing, exactly how most of us learned networking in the first place.

## Zones

The latest update introduced **zones** — Campus, Residential, Commerce, Industrial — each generating different types of demand. A campus zone needs file servers and DNS. Residential wants web and streaming. Commerce needs databases and payment processing.

This means servers actually matter now. You can't just lay cable — you need infrastructure. DNS became a prerequisite for cross-zone traffic. Network engineers will feel that one in their bones.

Zones also solved a visual problem. Random endpoint placement made for ugly, chaotic networks. With zones, endpoints cluster naturally into regions connected by backbone links. It looks like a real network diagram. Because it basically is one.

## The Endgame

Once your network gets big enough, you earn an ASN and BGP unlocks. Now you're peering with AI-run neighboring networks, climbing ISP tiers, selling transit to smaller networks, and managing the politics of peering agreements. The game goes from "build a LAN" to "run an internet backbone."

More peers means more money. More money means more traffic. More traffic means more congestion, more attack surface, and more ways for everything to go catastrophically wrong. Just like real life.

## Built With Godot

The whole thing is built in Godot 4.x with GDScript. The simulation layer is pure data — a graph of nodes and edges with traffic state — completely separated from the visual layer. This means deterministic simulation, easy speed control, and saveable/replayable networks.

Packet animations are the core visual identity. Little dots flowing along links, bunching up at congested points, bursting into red sparks when dropped. You can *see* your network working. Or not working. Usually not working.

## Play It

Packet Tycoon is available now on itch.io for Windows and Linux. It's early, it's rough around some edges, but the core loop is there and it's genuinely fun — especially if you've ever been the person everyone blames when the network goes down.

Because let's be honest. It's always the network's fault.

[Play Packet Tycoon on itch.io →](https://steve3279.itch.io/packet-tycoon)
