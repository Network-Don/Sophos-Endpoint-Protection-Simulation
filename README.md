# Sophos Endpoint Protection: A Solo Simulation

How a client's network only security model broke down once laptops left the office, and how each Sophos Endpoint Protection control was rebuilt, tested, and validated to close that gap.

## Background

During a cybersecurity mentoring cohort, I used a real challenge faced by one of my organization's client organizations as the basis for a hands on simulation. The client's laptops were protected while sitting on the office network through firewall rules, network level application control, and the usual perimeter defenses. The problem was what happened the moment those laptops left the building. On a home network or public Wi-Fi, none of that protection existed anymore, and employees were left exposed without realizing it.

This repository documents a solo simulation of the Sophos Endpoint Protection deployment I recommended and configured to close that gap, one endpoint, one policy at a time, with each one tested against real, observable behavior rather than just toggled on and left unverified.

A related project, where I onboarded a full cohort of students to run this same simulation together, will be documented separately.

## The Core Problem

Every control the client already had was tied to the network, not the device. That meant:

- Malware protection stopped at the office firewall. A laptop infected at home had nothing standing between it and a malicious file.
- Application control on the firewall could be bypassed entirely just by connecting through a personal VPN once remote.
- Web filtering had the same blind spot. Block a site at the office, and it's wide open the second someone's off that network.
- Removable storage was never addressed by network security at all, since it's a physical vector no firewall rule can touch.

The fix running through all four policies is the same idea applied four times: move protection from the network to the endpoint, so it travels with the device instead of staying behind at the office door.

## What Was Simulated

| Policy | Risk Addressed | Result |
|---|---|---|
| [Malware Protection](./Malware%20Protection/README.md) | Malware from unofficial downloads and installs | EICAR test file detected and traced within seconds, with a full behavioral footprint captured |
| [Application Control](./Application%20Control/README.md) | Firewall level app blocks bypassed via VPN | Document viewer and file sharing apps blocked at the endpoint, independent of network |
| [Web Protection](./Web%20Protection/README.md) | Non work, high risk browsing on office laptops | Tagged site categories blocked during working hours, confirmed via live event log |
| [Peripheral Control](./Peripheral%20Control/README.md) | Data loss and malware via removable media | USB storage and MTP/PTP blocked, with live block events captured within seconds of activation |
| [Data Loss Prevention (DLP)](./Data%20Loss%20Prevention/README.md) | Confidential files leaving through removable storage, by department | Content matched transfers blocked across both direct copy and print to PDF methods |

## A Recurring Lesson

One finding showed up more than once and is worth calling out on its own: a policy being configured is not the same as a policy being active. Twice during this simulation, in Application Control and again in Peripheral Control, a policy briefly showed as "bypassed," with another policy in the stack taking precedence. Catching this meant explicitly verifying each policy's active and assigned status as its own deployment step, not just trusting that saved settings meant enforced settings. That's a small thing that has real consequences in a live environment.

## Why This Matters

Every one of these four sections traces back to the same root cause: security tied to a location breaks the moment the device isn't in that location anymore. Endpoint level protection doesn't have that weakness, since it enforces the same policy whether the laptop is in the office, at home, or on public Wi-Fi. That's the throughline of this whole simulation, and the reason Sophos Endpoint Protection was the right recommendation for this client in the first place.

## Status

All five policies, Malware Protection, Application Control, Web Protection, Peripheral Control, and Data Loss Prevention, are now documented. This completes the initial policy set for this simulation.
