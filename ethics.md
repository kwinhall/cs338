# Ethical analysis of a security-related scenario
By Kendra Winhall

For this assignment, I choose the second scenario.

## A. Identify the main ethical question or questions faced by the main character ("you") in the scenario.

There are a couple main ethical questions in this scenario:
* Now that I have learned that the CEO wants to generate additional revenue by selling anonymized location data--in violation of the company's stated mission--what should I do? Some more specific considerations include:
    * If the company eventually claims to decide to not sell location data, how do I know that's the truth?
    * Do I have an ethical responsibility to report this? If so, to whom?
    * Do I have an ethical responsibility to find out who the CEO was talking to at the conference (to figure out which companies are selling their users' data)? If so, do I have an ethical responsibility to report this, and to whom?
* Now that I have learned that users' old location data is in the API's archived web logs--in violation of the company's stated mission--what should I do? Some more specific considerations include:
    * Do I have an ethical responsibility to report this? If so, to whom?
    * Is it more ethical to wait and give a chance for the company to fix it? Or is it more ethical to tell users immediately?
    * If I do reveal this bug and it harms the company, this could harm the livelihoods of employees that were not involved in this problem. Is this ethical?
    * The CEO currently wants to sell users' location data, and doesn't want to fix the bug. If the company does claim to fix this bug and scrub the old data, do I trust that they actually did?
    * Even if the bug is fixed quickly, do users still deserve to know?
    * Even if the bug is fixed quickly, does the general tech world still deserve to know about this bug so that other companies with the same issue could fix it? Or could this result in them selling their users' data too?
* The user's location is sent as a GET parameter and is in the URL. This is not a secure way to treat sensitive location data! What should I do? 

## B. For each stakeholder (or category of stakeholders) in the scenario, identify the stakeholder's relevant rights.

* The users of the app are one group of stakeholders in this scenario. They have the following rights:
    * The right to be informed about how their personal data is being collected, stored, and used.
    * The right to opt out from the sale of their personal data.
    * The right to make the company delete their personal data.
* Another group of stakeholders in this scenario is employees of the company. 
    * If employees report the company for its data collection practices (or its failure to disclose them to users), they have the right to be protected from retaliation.
* The Board of Directors is another group of stakeholders in this scenario. They have the following rights:
    * The right to know about important matters that could affect the company, like the company's data collection practices (and whether it has informed users of how their data is used). 
    * The right to hire or fire a CEO.

## C. List any information missing from the scenario that you would like to have to help you make better choices.

* How are users currently informed about how their personal data is being collected, stored, and used?
* Do users currently have the option to opt out of selling their personal data?
* Do users currently have the option to delete their personal data?
* Currently, users believe that their location data is discarded immediately, but we've just discovered that it's being stored indefinitely. What's the company's plan for informing the users of this?

## D. Describe your possible actions, and discuss the likely consequences of those actions.

* I could immediately report these problems through internal channels, like HR, compliance, or a hotline.
    * This could be a good idea, especially if I do it through an anonymous hotline. It brings attention to the issue without harming the company's reputation. 
    * However, whistleblowing through internal channels also puts less pressure on the company, and it might not change anything. In addition, if the company finds out that I reported these problems, my job could be at risk.
* I could immediately report these problems through external channels, like the news or the government.
    * This could be a good idea because whistleblowing through external channels puts more pressure and accountability on the company while also informing users and the general public of the situation. 
    * However, it might be overkill to immediately go to external channels without giving the company a chance to fix this issue first. In addition, it's unlikely that I could stay anonymous, so I'd lose my job, and my friends/coworkers would probably stop talking to me.
* I could keep my concerns to myself and wait and see what happens before making a decision.
    * This could be a good idea because it gives time for the company to make the right decision without any conflict happening.
    * However, if I wait too long, the company does nothing, and then user data is compromised, then that's bad; I knew harm could happen, yet I failed to prevent it.
* I could consult with the CTO or other employees before making a decision.
    * This could be a good idea because I'd get the perspective of other people in the company, and they might have more information or insight.
    * However, if the CEO finds out that I've been talking to other employees about this, we may face consequences.
* I could do nothing.
    * This could be a good idea because I get to keep my job and not have any conflict with others in the company.
    * However, this could result in users' location data being stored and sold without their knowledge or consent. I knew harm could happen, yet I failed to prevent it.

## E. Discuss whether the ACM Code of Ethics and Professional Conduct offers any relevant guidance.

Here are the relevant section numbers and excerpts:

Section 1.6:

> Only the minimum amount of personal information necessary should be collected in a system. The retention and disposal periods for that information should be clearly defined, enforced, and communicated to data subjects. Personal information gathered for a specific purpose should not be used for other purposes without the person's consent...

* This guidance implies that the company should discard all saved location data from the API's archived web logs (and redesign the current system that stores user location data). 
* In addition, this guidance implies that the company should not sell users' location data without their consent.

Section 1.7: 

> Computing professionals are often entrusted with confidential information such as ... client data ... Computing professionals should protect confidentiality except in cases where it is evidence of the violation of law, of organizational regulations, or of the Code.

* Similar to the previous point, this guidance implies that the company should discard users' saved location data, redesign the current system that saves users' location data, and refrain from selling users' location data.

Section 2.9: 

> Robust security should be a primary consideration when designing and implementing systems.

* This guidance implies that the company should change its current practice of sending the user's location as a GET parameter and putting it in the URL. Instead, the company should replace this system with something more secure.
* In addition, this guidance implies that the company should discard saved location data, so that in the case of an attack, user location data is not compromised.

Section 3.1: 

> People—including users, customers, colleagues, and others affected directly or indirectly—should always be the central concern in computing.

* This guidance implies that the company should protect user data instead of selling it and profiting off of it.


## F. Describe and justify your recommended action, as well as your answers to any other questions you presented in part A.

The main issues I'm concerned about here are:

* Users' location data is not treated securely--it's stored in the URL
* Users' location data is stored long-term (in violation of the company's mission)
* The CEO wants to sell users' location data (in violation of the company's mission)
* Users do not seem to be informed of the above issues

Here's how I would handle the situation:

* First, I would talk to the CTO about her impressions of the meeting. If she is concerned about the same issues that I am, then I could encourage her to take action--she is more influential in the company than I am.
* If the CTO doesn't want to take any action, I would wait a few days and see if the company addresses any of the issues or comes to a formal decision about next steps. This gives the company a chance to do the right thing.
* If the company doesn't address these issues--or if it decides to sell users' location data and/or continue storing it--then I would report the issue internally through an anonymous hotline. This gives the company a push to fix the issues without putting myself at risk.
* If the company did not fix these issues, then I would warn them anonymously that I would report these issues through external channels. This hopefully would make the company realize that they will face consequences if they don't act immediately.
* If none of this worked, I would consult a lawyer and figure out the best way to report this issue through external channels. Since I have exhausted other channels, I can feel justified putting my job and the company at risk in order to protect users.
