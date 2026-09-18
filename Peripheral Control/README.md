# Peripheral Control

[Back to main story](../README.md)

## Problem Statement

The remaining gap after malware, application, and web controls was physical: removable media. Company laptops holding client and organizational data were still exposed to two related risks. One was an employee, deliberately or accidentally, walking off with sensitive files on a USB drive. The other was the reverse: someone plugging in an infected personal flash drive or phone and introducing malware directly onto the endpoint, bypassing every network and web layer control already built. This is the one class of risk that has nothing to do with what network the laptop is on. It travels with whatever gets physically plugged in.

## Procedure

I built a dedicated Peripheral Control policy, "Peripheral Policy Test," and assigned it directly to the test endpoint (LBA-RAPHAEL) rather than leaving it to inherit from a base policy. Within the policy, I selected "Control access by peripheral type and add exemptions," the more granular of the three enforcement modes, rather than a blanket disable everything or monitor only approach, since the goal was to block specific risky categories without breaking legitimate peripheral use.

The peripheral types were set individually. Secure removable storage, Removable storage, and MTP/PTP (the protocol phones and cameras use to transfer files) were all set to Block, directly targeting the two risks of data walking out and malicious files walking in. Bluetooth, floppy drive, infrared, modem, optical drive, and wireless were left on Allow, since blocking those wasn't relevant to this risk and would have caused unnecessary friction. I also enabled Desktop Messaging so a blocked action surfaces immediately to the user rather than failing silently, with the message: "Your recent action is blocked by your Organization. Reach out to your Security Administrator."

Worth noting for the documentation: while configuring the assignment, the policy briefly showed as bypassed, with another policy's settings taking precedence, the same precedence issue encountered during Application Control. This time it was caught early and resolved by confirming the policy's assignment and activation directly, and the final state confirmed Policy Active, scoped to 1 computer, 0 groups, last updated Feb 9, 2026.

## Solution

The endpoint's event log confirmed the policy wasn't just configured but actively enforcing in real time. Two separate attempts to use a USB flash disk (USBSTOR\DiskUSB_____Flash_Disk_____1100) were caught and blocked, logged at 3:47:21 AM and 3:49:07 AM on Feb 9, 2026, timestamps that line up exactly with when the policy went active. Sitting in the same event stream as the earlier web blocking and malware cleanup entries, this reinforced what was already flagged in the Web Protection section: on Sophos, every control reports into one unified timeline on the endpoint, which made it straightforward to correlate "policy went live" with "policy actually stopped something."

## Lessons Learnt

- Removable storage is a control network security simply can't reach. It's a physical vector, so it has to be solved at the device itself, regardless of how strong the network and web layers are.
- Granular, per type control, blocking storage and MTP/PTP while allowing Bluetooth and wireless, is more realistic for a workplace than an all or nothing peripheral lockdown. It addresses the actual risk without frustrating normal use.
- The recurring "policy bypassed" issue across two different policy types, Application Control and Peripheral Control, points to a broader pattern worth calling out: policy precedence in Sophos Central is easy to overlook, and verifying Active status should be a standard last step in every deployment, not an afterthought.
- Seeing the block events land in the log within seconds of the policy going active was a strong practical confirmation that configuration and real world enforcement are two different things worth checking separately.
