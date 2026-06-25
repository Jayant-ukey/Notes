
**Q1. What was the toughest production issue you handled?**

For this type of question, interviewers expect the **STAR format**:
- **S**ituation – What was the context?
- **T**ask – What was your responsibility?
- **A**ction – What steps did YOU take?
- **R**esult – What was the outcome?

Your answer already has a good technical story, just needed better structure. Here's the improved version:

> During one of our production releases, some clients with higher usage started facing critical issues with their system. All the ports were getting occupied, leading to a **port exhaustion issue**, which was causing the application to crash frequently.
>
> As part of the team responsible for resolving this, my immediate task was to first stabilize the system and then find the root cause. Initially, we restarted the application to free up the occupied ports and bring the system back to a stable state.
>
> Then I deep-dived into the codebase and identified that there were multiple places where **HttpConnections were being created but never closed properly.** I fixed this by correctly closing them using **try-with-resources** and delivered the fix as a patch to production.
>
> After applying the patch, the issue was completely resolved and no client faced that problem again. This was one of the most challenging production issues I handled, as it required both quick thinking to minimize impact and careful analysis to fix the root cause permanently.

---

**Q2. Why are you changing jobs?**

Interviewers expect this answer to be:
- **Positive** – should not sound like you're running away from something
- **Forward-looking** – focused on growth, not complaints
- **Genuine & concise** – honest but professional

Your answer was on the right track but needed a stronger, more confident tone:

> I have had a great learning experience at GlobalLogic over the past 4 years, and I am truly grateful for the opportunities I got there. However, I now feel that I have reached a point where I want to **challenge myself further** and explore new work environments and technologies.
>
> I am looking for an opportunity where I can **broaden my expertise**, take on new kinds of challenges, and continue growing both technically and professionally. I believe this role aligns well with where I want to take my career next.

---

**Q3. How do you handle tight deadlines?**

Your answer has the right idea but is **too short** for a managerial round. Interviewers expect a more complete answer with a **real example** to back it up. Here's the improved version:

> Whenever I am given a tight deadline, the first thing I do is **break down the task into smaller subtasks** so that I have a clear picture of what needs to be done.
>
> Then I **identify dependencies and blockers early** — for example, if I need something from the DevOps team or from a developer working on another service, I raise it immediately so that they can start working on it in parallel and I don't end up waiting for them at the last moment.
>
> Once that is done, I **prioritize the tasks based on their importance and impact**, and focus on completing the high-priority ones first.
>
> For example, in one of our releases, we had a very tight deadline and multiple things to deliver. I broke down the work, identified that I needed an environment setup from the DevOps team early on, raised it immediately, and by the time I finished my development, the environment was already ready. This helped us deliver on time without any last-minute rush.
>
> I also make sure to **keep my lead informed** about the progress regularly, so that if there is any risk to the deadline, it can be flagged and handled early.

---

**Q4. Describe a conflict in your team.**

Your answer has a **great real story** — this is exactly what interviewers want! It just needed better structure and flow. Using the **STAR format** here:

> Once, two testers raised tickets for a similar problem but with slightly different descriptions. Since the descriptions looked different, the tickets were treated as two separate issues and were assigned to two different developers.
>
> Both developers resolved their respective tickets, but using completely different approaches. When it came to merging, both were firm on their own solutions and could not agree on which one to go with. My lead then asked me to **review both implementations and share my opinion.**
>
> I carefully reviewed both solutions and found that both were technically correct. However, on deeper analysis, I noticed that **one developer had reused existing methods** that were already available in the codebase, making the code cleaner and more efficient. The other developer had written a completely new implementation from scratch, which was also correct but added unnecessary complexity.
>
> I then arranged a meeting with both developers and my lead, and **presented both approaches clearly** — explaining what each one did and listing the pros of each. I then recommended going with the solution that reused existing methods, giving specific reasons like **code cleanliness, reusability, and maintainability.**
>
> Once I explained the reasoning clearly and objectively, the other developer also agreed, and the conflict was resolved smoothly. This experience taught me that conflicts are best resolved through **facts, clear communication, and mutual respect** rather than just opinions.

---

**Q5. How do you communicate with clients?**

Your answer covers all the right points! It just needed better structure, flow, and a small example to make it stronger for a managerial round:

> Whenever I interact with clients, the first thing I do is **listen carefully to their requirements** to understand what they actually need, rather than assuming things.
>
> Before committing to anything, if I am not fully sure about something, I **ask for some time to analyse it properly** and then come back with a clear and confident answer. I believe it is always better to take time and give the right answer than to commit to something incorrect.
>
> I also make sure to provide **regular status updates and progress reports**, and if there are any blockers or risks, I highlight them early so that the client is never caught off guard.
>
> When dealing with **non-technical clients**, I avoid using technical jargon and instead explain things in terms of **business impact** — for example, instead of saying "we fixed a memory leak," I would say "we resolved an issue that was causing the application to crash, and it is now stable."
>
> Additionally, I always **document the discussion points** from every meeting and share a meeting summary with the client, so that everyone is on the same page and there is no miscommunication later.

---

**Q6. What is your biggest achievement in your last project?**

Your answer has a **genuinely impressive story** but it lacks structure and impact. A bigger issue is that the answer doesn't highlight **your skills and what made YOU stand out.** Here's the improved version using STAR format:

> One of my biggest achievements was **independently investigating and fixing a long-standing issue** in a service that no one in the team had touched for a very long time.
>
> This service was created at the very beginning of the project and its job was to **automatically clean up unused old data** from the system and database. Over time, clients started reporting that this service was not working properly. Since the data was not being cleaned automatically, the support team had to manually run scripts to clean the client systems, which was inefficient and time-consuming.
>
> The challenge was that **no developer in the team had worked on this service before.** Everyone knew what it was supposed to do, but no one fully understood how it worked internally.
>
> I took ownership of this task, went through the entire codebase of the service from scratch, understood its functionality deeply, identified the root cause of the issue, and resolved it completely.
>
> After my fix, **the service started working as intended** — automatically cleaning up the unused data without any manual intervention from the support team. This was a long-running issue that had been affecting the client for a long time, and being the person who finally resolved it gave me a great sense of accomplishment.
>
> This achievement also showed me that with **patience, ownership, and willingness to explore unknown code**, any problem can be solved.


---

**Q7. Are you comfortable learning new technologies?**

Your answer is good but **too short and casual** for a managerial round. It needs more confidence, structure, and depth. Here's the improved version:

> Yes, absolutely. I genuinely enjoy picking up new technologies whenever the need arises.
>
> One of the best examples of this is when I worked on a **POC (Proof of Concept)** that required me to use **AWS services** which I had not worked with before. I started by going through the **official AWS documentation**, which gave me a strong foundation. Wherever I found something difficult to understand, I took help from our **internal LinkedIn Learning platform**, which helped me fill the gaps quickly.
>
> By combining both resources, I was able to **learn and implement the required AWS services successfully** in the POC within the expected timeline.
>
> I believe that in today's fast-moving tech world, the ability to **learn quickly and adapt** is just as important as the existing skills you have. Whenever I approach a new technology, I first try to understand the **core concept and purpose** behind it, and then move to hands-on implementation — which I find is the fastest way to get comfortable with something new.

---

**Q8. Explain Your Current Project End-to-End**

Your answer is **already very well written** — it covers all the right areas. It just needed minor language polish, a smoother flow, and a stronger closing. Here's the improved version:

> My current project is the **LifeImage Local Application**, which operates in the **healthcare sector.**
>
> The core purpose of the application is to enable hospitals and medical groups to **securely share medical images** such as X-Rays, MRIs, and other medical documents — either from one hospital to another, or between different groups within the same hospital. This helps medical professionals access patient imaging data quickly, which is critical in healthcare.
>
> **Tech Stack:**
> The backend is built on **Java and Spring Boot**, with support for multiple databases — **PostgreSQL, MySQL, and Oracle** depending on the client environment. The application is hosted on **GCP (Google Cloud Platform).** We use **Jenkins** for CI/CD pipelines and **Splunk** for log monitoring and alerting.
>
> **My Role:**
> As a **Senior Software Engineer**, my responsibilities include designing and developing **REST APIs**, implementing new features, resolving production issues, and continuously improving the **security and performance** of the application.
>
> **Team Structure & Process:**
> Our team consists of backend developers, frontend developers, a QA team, a Scrum Master, and a Product Owner. We follow **Agile Scrum methodology** with 2-week sprints, and I actively participate in sprint planning, daily standups, and backlog grooming sessions.
>
> **Key Contributions:**
> Some of my notable contributions to this project include resolving the **port exhaustion issue** that was critically impacting clients, implementing **performance improvements**, and making **security enhancements** — all of which directly improved application stability, performance, and client satisfaction.


---

**Q9. What Was the Toughest Production Issue You Handled?**

This answer is **excellently written** — structure, flow, technical depth, and the closing lesson are all spot on. This is exactly what a managerial round interviewer expects. ✅

Just one small enhancement I'd suggest — adding a **team coordination aspect** to make it sound more senior-level:

Replace the last paragraph with:

> The key lesson I took from this was the importance of **resource management in Java** and how issues that seem invisible in low-traffic environments can become critical failures at scale. I also coordinated with the team to review other parts of the codebase for similar patterns, and advocated for adding **connection-related metrics to our Splunk monitoring dashboard** so such issues could be caught earlier in the future.

Everything else is perfect — no changes needed! 👍

---

**Q10. What Was Your Contribution Compared to the Team's?**

Your points are all **valid and strong**, but they are written in a rough bullet format. For a managerial round, this needs to be delivered as a **confident, well-structured answer.** Here's the improved version:

> Within our team of 5 developers, my role goes beyond just individual development contributions.
>
> I serve as the **first point of contact for all junior developers** in the team. Whenever they face any technical challenge or need guidance, they come to me first. I try my best to help them with proper suggestions, and only if something is beyond my understanding do we escalate it to the Resource Manager. This has helped the team resolve blockers faster without always depending on the RM.
>
> I am also the **primary point of contact for the Application Security team** for all Checkmarx-related discussions. I represent our team in those meetings, understand the security findings, and coordinate with the developers to get them resolved.
>
> Additionally, **in the absence of our Resource Manager**, I take ownership of the **Sprint Review presentations** and represent the team in front of stakeholders, ensuring continuity and smooth communication.
>
> On the technical side, I independently own and manage critical modules such as **Janitor and EHR**, which are key parts of the application. The Janitor module in particular was a long-standing challenge that no one had fully explored before, and I took complete ownership of understanding and fixing it.
>
> Overall, I see my role as a combination of **technical ownership, team mentorship, and stakeholder communication** — which I believe is what differentiates a senior contributor from the rest of the team.

---
