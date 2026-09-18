# Web Protection

[Back to main story](../README.md)

## Problem Statement

The client's concern here was less about a single incident and more about ongoing exposure: staff using their office laptops to browse sites with no real work purpose, such as football streaming sites, social media, and similar destinations, which tend to be heavily loaded with intrusive ads, redirect chains, and malvertising. On a personal device that's a personal risk. On a company owned laptop that also touches the corporate network and client data, it becomes an organizational one. The goal wasn't to lock the machine down entirely, but to draw a clear line around non work, high risk categories of browsing, especially outside working hours.

## Procedure

I built this out as a Web Control policy in Sophos Central. Rather than blocking sites one by one, I used Sophos's website tagging feature to group them under a custom tag, "Non-Work Ethical Sites" (categorized as Entertainment), and added the actual offending domains to it: facebook.com, goal.com, hesgoal.com, and netflix.com. Tagging this way meant the policy could act on the whole group at once, and new sites could be added to the tag later without rebuilding the rule.

With the tag created, I set the control action for it to Block rather than warn and allow, since these categories had no legitimate work justification. I also added a schedule to the policy, restricting enforcement to 08:00 AM to 05:00 PM, Monday through Friday, matching the actual working hours the client wanted covered, rather than a blanket 24/7 block. The policy was applied and confirmed Active, scoped to 2 users, with Web Control showing as last updated Feb 8, 2026.

## Solution

The endpoint agent's event log confirmed the policy was actively enforcing, not just configured. Multiple access attempts were caught and blocked for the test user (LBA-RAPHAEL\user), including direct hits on netflix.com and goal.com, as well as several raw IP addresses (54.246.79.9, 54.73.148.110, 2.20.242.73), a good sign the block was working at the connection level and not just matching on domain names typed into a browser.

Interestingly, the same event log also shows a couple of blocked USB flash disk events and earlier "Threats cleaned up" entries from Jan 25, which lines up with the Malware Protection testing from earlier. It's a useful reminder that on Sophos, all these controls report into one unified event stream on the endpoint, so nothing here happens in isolation, which connects directly into the Peripheral Control section.

## Lessons Learnt

- Tagging websites into a named group made the policy easier to manage and extend, instead of hardcoding individual domain rules that get messy over time.
- Scheduling enforcement to business hours, rather than blocking permanently, reflects a more realistic policy. It targets the actual risk window, such as an employee streaming football during work hours on a company laptop, without being unnecessarily restrictive after hours.
- Blocking resolved IP addresses, not just domain names, showed the policy holds even against sites trying to be reached through alternate routes.
- The unified event log across malware, web, and peripheral activity is a strong argument for endpoint centric protection: one console, one timeline, correlated visibility, something a purely network based setup can't offer once the device leaves the building.
