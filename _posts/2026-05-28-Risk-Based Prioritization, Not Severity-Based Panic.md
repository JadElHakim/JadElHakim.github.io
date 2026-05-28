---
title: "Risk-Based Prioritization, Not Severity-Based Panic"
date: 2026-05-28 10:00:00 +0300
categories: [AppSec, Vulnerability Management]
---


# why risk based. 

As of 2026, and with the advancement of AI in offensive security and SAST, CVE volume is increasing, and for the subset of vulnerabilities that attackers do exploit, the exploitation window is shrinking. That combination creates a prioritization problem: teams have more findings to process, but less time to identify the ones that actually matter. 

Examining the below reference from the Zero Day Clcok, we can see 2 major trends that support this claim:

**Time to Exploit**
![Zero Day Clock time to exploit chart](/assets/img/Risk%20Matrix/zerodayclock-tto.png)

**Exploit Rate**
![Zero Day Clock exploit rate chart](/assets/img/Risk%20Matrix/Screenshot%202026-05-28%20201006.png)

1. The time to exploit a CVE is nearing <24 hours, and it will reach it sooner than expected. (At the time of writing, the figure had already moved from roughly 2.4 days to 1.6 days within a 2 weeks, reinforcing how quickly the trend is changing.).
2. The exploit rate is on a downward trend. There are too many CVEs reported and only a fraction of them are being exploited.

This means that organizations are being flooded with vulnerabilities at a rate they cannot handle, and where they most definitely cannot effectively filter through the noise and focus on remediating the exploitable and critical CVEs. Hence, the panic.

Combining these 2 factors, it is apparent that relying on CVE severity/CVSS scores alone is not sufficient. To be able to filter through the flood of CVEs, teams should focus on understading the risk posed by each CVE, and prioritize based on that.
CVSS scores are a good starting point, but they do not take into account the specific context of the organization. CVSS even misses (or under represents) some of the most critical factors that affect the risk posed by a CVE such as:
 - The presence of an exploit in the wild.
 - Visibility of the vulnerable asset to the internet.
 - If the vulnerable components process untrusted data.
 - The presence of compensating controls that mitigate the risk posed by the CVE.

Hence, it seems like the better idea is to take a risk-based approach to vulnerability management, and not just rely on severity-based panic. We can even see that the industry is moving towards this approach, namely NIST is now only looking at CVEs based on their own risk-based criteria. [NIST's 2026 NVD operations update](https://www.nist.gov/news-events/news/2026/04/nist-updates-nvd-operations-address-record-cve-growth)

# severity is not priority.
A common mistake in vulnerability management is treating severity as the same thing as priority.

Severity describes the technical characteristics of a vulnerability. Priority should describe what the organization should do about it. Those are related, but they are not the same.

A Critical CVSS vulnerability in an isolated internal component with no exploit path may deserve attention, but it may not be the first thing the team fixes. A High vulnerability in an internet-facing authentication flow with public exploit code and customer data exposure may be the real emergency.

That is why severity should be treated as an input into the decision, not the decision itself.

# defining risk-based prioritization.

Risk-based prioritization is the process of assessing the risk posed by each vulnerability and prioritizing them based on that risk. This involves taking into account the specific context of the organization, such as the assets that are vulnerable, the potential impact of an exploit, and the likelihood of an exploit being successful, etc...

This approach allows organizations to focus their resources on the vulnerabilities that pose the greatest risk, rather than trying to remediate every vulnerability regardless of its severity. 

Technically, this takes the form of a matrix scoring system, where each CVE is scored based on the various factors that affect its risk, and then prioritized based on that score.

## the matrix.

The matrix is a simple 2 vector scoring system, where one vector represents the likelihood of an exploit being successful, and the other vector represents the potential impact of an exploit. (This is not a rule of thumb and effectively each axis can represent a different factor based on the organization's context, but this is the most common approach).

## defining the vectors.

### the Y axis: likelihood.

likelihood is exactly what the word implies.

"How likely is it that this vulnerability will actualize?"

in the world of CVEs, this is affected by the following factors:

- **KEV status**: if there is a known exploit in the wild
- **PoC status**: if there is a public PoC and how mature/automatable it is.
- **EPSS score**: lower EPSS score means that the CVE is less likely to be exploited. 
- **Exposure of the vulnerable asset**: if the vulnerable asset is not exposed, then it is less likely to be exploited. e.g: a critical CVE found in an internal application that is not exposed to the internet is less likely to be exploited than a critical CVE found in a public facing application.


> These are just some of the factors that affect the likelihood of an exploit being successful, and there are many more factors that can be taken into account based on the organization's context.
{: .prompt-info }

#### Example Scoring:

Likelihood | Score | Meaning |
---|---:|---|
Very High | 5 | There is a known exploit in the wild, and the vulnerable asset is exposed to the internet. Or EPSS score is very high and there is a public PoC that is mature and automatable
High | 4 | There is a public PoC, and the vulnerable asset is exposed to the internet
Medium | 3 | There is a public PoC, but the vulnerable asset is not exposed to the internet
Low | 2 | There is no public PoC, but the vulnerable asset is exposed to the internet
Very Low | 1 | There is no public PoC, and the vulnerable asset is not exposed to the internet


> This is just a high level example of how the likelihood can be scored, and is not limited to ONLY the above factors. 
{: .prompt-info }

### the X axis: impact.


for impact, the question to ask is:

"What is the damage if this vulnerable component is exploited?"

Now, at first, this metric might sound generic, but the idea here is to ground that question in the context of the organization. Meaning, we need to look at our application and understand what is considered "Crown Jewels" and "Critical Systems" for our organization.
One way this can be done, is through classification. My approach was using 4 metrics:

- **Data Classification**: Classify data based on sensitivity/importance. Once data is classified then the impact can be quantified.
- **Blast Radius and Availability**: Would an exploit of this CVE lead to a large blast radius? would it lead to a DoS of a single microservice, or would it lead to a DoS of the entire application?
- **Regulatory Impact**: would an exploit of this CVE lead to a regulatory violation? e.g: if the CVE is found in a component that processes credit card data, then it is more likely to lead to a regulatory violation than a CVE found in a component that does not process sensitive data.
- **Financial Loss**: would an exploit of this CVE lead to a financial loss? e.g: if the CVE is found in a component that processes payments, then it is more likely to lead to a financial loss than a CVE found in a component that does not process payments.

> These are just some of the factors that affect the impact of an exploit, and there are many more factors that can be taken into account based on the organization's context.
{: .prompt-info }

#### Example Scoring:

Impact | Score | Meaning |
---|---:|---|
Negligible | 1 | Negligible business or technical impact
Low | 4 | Limited component-level impact
Medium | 9 | Meaningful service, data, or user impact, but not critical 
High | 16 | Critical business function, sensitive data, or broad blast radius
Critical | 25 | Crown-jewel system, regulatory exposure, major financial/customer impact

### scoring. 5x5 or 5x25?

The scoring can be done in a 5x5 matrix, where each axis is scored from 1 to 5, and then the scores are multiplied to get a final score. This is a simple approach that allows for easy prioritization. However, in a 5x5 matrix the band of classification is narrow and would lead to misclassified CVEs. For example, a CVE with a likelihood score of 5 and an impact score of 4 would have the same final score as a CVE with a likelihood score of 4 and an impact score of 5, even though the first CVE is more likely to be exploited than the second one.

Hence, a better approach would be to use a 5x25 matrix, where the likelihood axis is scored from 1 to 5, and the impact axis is scored from 1 to 25. basically the square of 1 to 5. This allows for a wider band of classification and more accurate prioritization. Because in product environments, not all exploitable vulnerabilities are equal. A highly exploitable issue in a low-impact component should not automatically outrank a lower-likelihood issue affecting customer data, payment flows, authentication, or core availability.

For example a CVE with a negligible impact and high likelihood would have a score of 5, while a CVE with a critical impact and low likelihood would have a score of 25. This allows for a more accurate prioritization of CVEs based on their risk. If we were to use a 5x5 matrix, both of these CVEs would have the same score of 25, which would lead to misclassification and misprioritization.
Notably, a larger band would also allow for easier communication with stakeholders and clients. However, this is a whole other topic to be discussed later.

### examples of the matrix. 

#### 5x25 risk matrix

| Likelihood \ Impact | 1 | 4 | 9 | 16 | 25 |
|---|---:|---:|---:|---:|---:|
| **5 - Very High** | 5 | 20 | 45 | 80 | 125 |
| **4 - High** | 4 | 16 | 36 | 64 | 100 |
| **3 - Medium** | 3 | 12 | 27 | 48 | 75 |
| **2 - Low** | 2 | 8 | 18 | 32 | 50 |
| **1 - Very Low** | 1 | 4 | 9 | 16 | 25 |

A possible interpretation is: 1-20 = <span class="text-success">Low</span>, 21-45 = <span class="text-warning">Medium</span>, 46-80 = <span style="color: #fd7e14;">High</span>, and 81-125 = <span class="text-danger">Critical</span>. These bands should be tuned to the organization's risk appetite.

#### matrix in action.

Example A: A Critical CVSS vulnerability in an internal-only component with no PoC, no sensitive data, and compensating controls.
- Likelihood: Low (2)
- Impact: Low (4)
- Final Score: 8 (Low Risk)

Example B: A High CVSS vulnerability in an internet-facing authentication component with public exploit code and customer data exposure.
- Likelihood: Very High (5)
- Impact: Critical (25)
- Final Score: 125 (Critical Risk)

## the point.

The point is not to replace CVSS with another perfect score. The point is to avoid treating severity as a decision. Severity is input. Risk is the decision layer.


> The above writeup is heavily focused on CVEs, but the same approach can be applied to any vulnerability, even if it does not have a CVE identifier. e.g.: For organizations that run regular internal/external pentests, the same approach can be applied to the findings of the pentests.
{: .prompt-info }

---

_This post is part of Jad's Cybersecurity Blog._
