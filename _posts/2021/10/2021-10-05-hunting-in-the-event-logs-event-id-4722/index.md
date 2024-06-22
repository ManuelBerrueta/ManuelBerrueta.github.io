---
title: "Hunting in the Event Logs - Event ID 4722"
date: "2021-10-05"
tags: 
  - "4720"
  - "4722"
  - "4726"
  - "4732"
  - "aad"
  - "ad"
  - "cybersecurity"
  - "detection"
  - "eventviewer"
  - "evtx"
  - "infosec"
  - "security"
  - "threathunting"
  - "windows-2"
  - "windowsevents"
---

##### Quick event log review and threat hunting in the logs from the trenches post.

When reviewing an alert for `4722: A user account was enabled` the event is broken down into two parts, `Subject` and `Target`:  
**Subject**:This section is related to the account that was used to enable the account.  
**Target:** This section is related to the account that was enabled.

While it is simple to tell from the title what this event is related to, there is some other interesting facts about this event, the `4722` is an event created after a `4720` event.

The `4720` Event is `4720: A user account was created`. Both of these together are of interest, especially for sensitive resources.

Continuing the hunt you may find `4732: A member was added to a security-enabled local group`. If they are added to the group `Users`, this might be normal if a new user is being added legitimately, where legitimately is the keyword. But on the other hand, if we see another entry for the same event `4732`, but instead this new user is being adding to the group `Administrators` that might be of additional interest.

Additionally, covering their tracks you may see an Event ID of `4726: A user account was deleted`, following the deletion of an account.

#### Reference:

- [https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4722](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4722)
- [https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4720](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4720)
- [https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4722](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4722)
- [https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4726](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4726)
- [https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4726](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4726)
