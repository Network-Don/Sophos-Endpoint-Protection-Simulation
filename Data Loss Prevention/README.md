# Data Loss Prevention (DLP)

[Back to main story](../README.md)

## Problem Statement

The last gap in this simulation was the most direct one: confidential company data leaving the building on a USB drive, in the hands of an employee who wasn't authorized to have it in the first place. Unlike the earlier Peripheral Control policy, which blocks or allows removable storage as a category regardless of what's on it, this risk needed something smarter. The client didn't want to block USB use entirely, since some departments legitimately needed it. What they needed was control based on the content of the file itself and who was trying to move it. Finance and salary data should never leave through a non Finance endpoint. Job application material, resumes, CVs, and similar documents should never be drafted or transferred using a company machine outside HR, since that points to an employee using office resources to look for their next job elsewhere.

## Procedure

I configured this using Sophos's Data Loss Prevention policy with a custom content rule, built around a "New Content Rule" matching specific keywords rather than file type or file name alone.

Two rules were created to test two different scenarios:

**Finance and salary data rule.** The test document was written to contain sensitive financial and HR adjacent keywords, including FINANCE, SALARY, REVENUE, PRIVATE, and CONFIDENTIAL, along with phrases like salary review information, revenue performance report, private employee data, and confidential business document. This rule was scoped to apply to every department except Account and Finance, meaning any other department attempting to move a file matching that content would be blocked, while the Finance team itself, who legitimately handle this data, would not be.

**Job application rule.** The second test document was written around recruitment and job search language, including RESUME, CURRICULUM VITAE, JOB APPLICATION, CV, CAREER OPPORTUNITY, VACANCY, RECRUITMENT, LINKEDIN PROFILE, PORTFOLIO, INTERVIEW, and EMPLOYMENT. This rule was scoped to apply to every department except Human Resources, since HR legitimately works with this kind of content daily, while any other department producing or moving a document like this on a company endpoint is a signal worth flagging.

To validate both rules, I tested two separate transfer methods on the endpoint:

1. Copying the file directly to removable storage, both to the root of the USB drive and to a subfolder inside it.
2. Opening the document and attempting to save or print it as a PDF directly to the USB drive, again testing both the root of the drive and a subfolder inside it.

## Solution

Every attempt was blocked, across both content rules and both transfer methods. Sophos Endpoint Agent intercepted each action with a real time notification naming the exact file and action attempted, for example: "Transfer of file C:\Users\LBA-Raphael\Documents\TestFile.docx. This action is prohibited by LBA. Do contact Engr Raphael if you believe this is an error."

For the direct copy attempts, the transfer was stopped before the file ever appeared in the destination folder on the USB drive. For the print or save as PDF attempts, Windows itself surfaced a permission error ("You don't have permission to save in this location. Contact the administrator to obtain permission"), confirming that the DLP policy was intercepting at the write level rather than only watching for drag and drop copy events. This mattered, since it showed the rule wasn't limited to one narrow method of exfiltration. Whether someone tried to copy the file outright or route around it by printing to PDF and saving that instead, the content match still caught it.

Both the job application content (TestFile.docx) and the finance content (Latest Test) were caught consistently across all four attempts, confirming the two custom content rules were both active and correctly scoped.

## Lessons Learnt

- Content based DLP rules are a meaningfully different, and stronger, control than blanket peripheral blocking. Peripheral Control (documented earlier) can only say yes or no to a device category. DLP can say yes or no based on what's actually inside the file and who is trying to move it, which is a much more precise match to how real data loss usually happens.
- Testing more than one exfiltration method mattered. If I had only tested a direct copy and stopped there, I wouldn't have known whether printing to PDF as a workaround would have slipped through. Confirming the policy caught both closed that gap in the test itself, not just in theory.
- Scoping a rule by exception, blocking everyone except the department that legitimately needs that content, is a more sustainable approach than trying to allow list every valid use case individually.
- A job application content rule is an unusual one to see in a technical control, but it reflects a real, non malicious insider risk that's easy to overlook: using company equipment and company time to search for or apply to another job is still a policy and productivity concern worth addressing, even though it isn't a security incident in the traditional sense.
