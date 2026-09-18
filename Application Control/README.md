# Application Control

[Back to main story](../README.md)

## Problem Statement

Even after malware protection was in place, the client's exposure wasn't fully closed. Earlier in this engagement, an Application Control policy had already been configured at the network layer, on the firewall, to block staff from reaching certain risky or non compliant websites and applications. That worked while employees were physically on the office network, but the moment staff went remote, they could simply connect through a personal VPN and route straight around the firewall's control entirely. The network was enforcing a rule it could no longer see once the device left its perimeter, the same underlying weakness the malware scenario had already surfaced, just showing up in a different control.

The fix had to move with the device, not stay tied to the network it happened to be sitting on. That meant translating the same intent, restrict certain application categories, into an endpoint level Application Control policy that applies regardless of what network the laptop is using.

## Procedure

On Sophos Central, I built an Application Control policy, named "Application Control Test" in this simulation, scoped to the single test endpoint. Two application categories were targeted for control: Document viewer (2 of 69 available applications selected) and File sharing application (1 of 112 selected), representative of the kind of lightweight document viewer and media or file sharing tools staff had been using to move files outside sanctioned channels.

For detection behavior, I enabled "Detect controlled applications when users access them," with the response set to Block the detected application rather than just allow and notify, since the intent was active prevention, not passive monitoring. Scheduled and on demand scan detection was left off, since the priority for this test was real time access blocking. I also enabled Desktop Messaging so a blocked user gets immediate, clear feedback rather than a silent failure. The custom message reads: "This Application is not allowed by your organization. Consult the IT Admin for more details."

## Solution

Checking the endpoint's applied policies view confirmed the policy was live and correctly bound to the device (LBA-RAPHAEL) under "Endpoint Protection: Application Control (device)," sitting alongside the rest of the policy stack: Device Encryption, DLP, Windows Firewall, Peripheral Control, Threat Protection, Update Management, and Web Control.

One important detail surfaced during setup that's worth documenting honestly: the policy view flagged that it was currently bypassed, with settings deferred to another policy in the stack. That's a real and useful finding rather than a flaw to hide. It's a reminder that in Sophos Central, policy precedence and layering matter as much as the individual policy's settings, and a policy existing isn't the same as a policy actively enforcing.

## Lessons Learnt

- A control configured at the network boundary is only as strong as the boundary itself. VPN use from a personal network fully bypassed it, which is exactly why the same intent needed to be re implemented at the endpoint.
- Blocking by application category, rather than one off allow and deny per app, scales better for a policy meant to catch a class of risky tools, not just a specific known bad actor.
- Desktop Messaging isn't cosmetic. Clear in the moment feedback to the user reduces IT support tickets and helps staff understand why something was blocked, rather than assuming it's a system error.
- Policy precedence is a real operational risk. Discovering the policy was "bypassed" by another policy in the stack was a good reminder to always verify enforcement status, not just configuration, since a misconfigured hierarchy can silently neutralize an otherwise correct policy.
