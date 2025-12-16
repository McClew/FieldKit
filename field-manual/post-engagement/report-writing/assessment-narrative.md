---
icon: link
layout:
  width: default
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
  metadata:
    visible: true
---

# Assessment Narrative

## Attack Chain

The attack chain is our chance to show off the cool exploitation chain we took to gain a foothold, move laterally, and compromise the domain. It can be a helpful mechanism to help the reader connect the dots when multiple findings are used in conjunction with each other and gain a better understanding of why certain findings are given the severity rating that they are assigned.

For example, a particular finding on its own may be `medium-risk` but, combined with one or two other issues, could elevate it to `high-risk`, and this section is our chance to demonstrate that. A common example is using `Responder` to intercept `NBT-NS/LLMNR` traffic and relaying it to hosts where SMB signing is not present. It can get really interesting if some findings can be incorporated that might otherwise seem inconsequential, like using an information disclosure of some sort to help guide you through an LFI to read an interesting configuration file, log in to an external-facing application, and leverage functionality to gain remote code execution and a foothold inside the internal network.

There are multiple ways to present this, and your style may differ but let's walk through an example. We will start with a summary of the attack chain and then walk through each step along with supporting command output and screenshots to show the attack chain as clearly as possible.

## Tell a Story

Each vulnerability that we investigate should have a story attached to it. We should attempt to answer the following questions for each vulnerability investigated.

* What drew you to this element (vulnerability scanning results, an unidentified listening service, an uncommon open port)?
* What was the vulnerability?
* What impact does it have on the organisation?
* Did you attempt to exploit it?
* How difficult was it to construct an exploit?
* What was the result of the exploit attempt?
* Can further exploitation be attained?
* What can the organisation do to remediate the issue?
* What can the organisation do to mitigate the issue?

Reporting usually has the competing priorities of full coverage and brevity. However, your report should include details on failed attempts at exploitation as much as successful ones. This shows the target organisation where defenses are working and helps them to understand your approach to executing the test.
