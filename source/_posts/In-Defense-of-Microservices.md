---
title: In Defense of Microservices
date: 2025-02-20
tags: 
  - tech
---

**In Defense of Microservices**  

<figure style="margin: 2em 0; text-align: center;">
  <img 
    style="max-width: 100%; height: auto; border-radius: 8px; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);" 
    src="/images/in-defense-blog-featured-image.jpeg" 
    alt="Software engineer working on microservices architecture diagrams while having coffee" 
    width="400"
  />
  <figcaption style="margin-top: 1em; color: #666; font-style: italic; font-size: 0.95em;">
    Late nights and microservices paired with your favourite tea
  </figcaption>
</figure>

Late one night, while sipping tea and scrolling through Youtube, I stumbled upon a title that stopped me cold. A DoorDash engineering leader was challenging everything I thought I knew about microservices, calling them “technical debt.” My first reaction? A defensive *“Wait, really?”* followed by a reluctant *“Okay, maybe he's got a point.”* Let me explain why this struck such a nerve.

### **When Microservices Get Personal**  

I've been in the trenches with both monoliths and microservices. Early in my career, I worked almost exclusively with microservices—it was the de facto architecture style I knew. Fast-forward to today, and while I still appreciate microservices for the right use cases, I've also discovered the elegant simplicity of monoliths, which eliminate so much DevOps overhead for many teams.

The DoorDash example—500 services, 100 just to place an order—made me shudder. I’ve had to debug gnarly latency issues for a 20 ms regression at p99 across a few microservice hops. It wasn't fun, but atleast it felt tractable. Debugging across 100 just feels like a nightmare without extensive tooling. But calling microservices “technical debt” feels like blaming a hammer for a crooked nail. Yes, they introduce complexity—but they solve a problem thats much harder with monoliths.

The real issue isn’t the pattern — it’s how we wield it.

---

### **Evolution of a Monolith gone wrong**  

The podcast framed this as a monolith-vs-microservices cage match. But in my experience, architecture isn’t a religion — it’s a series of compromises.

In early 2017, I worked on a service that grew into a dreaded monolith over six years of development, with a team of 40+ engineers actively working on it. We didn't have a dedicated tooling team or a DevOps team—just engineers trying to push out features to a growing customer base. We reached the inflection point where splitting was necessary three years too late. We didn’t split because architecture diagrams told us to—we did it because production incidents screamed at us to. Here moving to a microservices architecture earlier would have been a better choice.

---

### **The Tooling Trap **  

The podcast nailed something I’ve grumbled about for years: tooling.

You can't compare a startup where engineers are manually SSH-ing into EC2 instances to deploy code vs a mature FAANG CI/CD tooling. The architectural choices are going to be different in those scenarios. But that wasn’t microservices’ fault. Sometimes you are moving fast and haven't yet invested in the tooling. Tools matter. With the right tooling, microservices can feel less like a liability and more like a superpower.

---

### **What I’m Hoping For Next**  

The podcast ended with a call for a “middle ground” between monoliths and microservices. I’d reframe it: Let’s get ruthlessly pragmatic about distributed systems.

A decade ago, I loved microservices. Today? I’m cautiously optimistic. Projects like Istio are easing the pain. Serverless lets teams focus on logic, not infrastructure. And after years of cargo-culting “best practices,” I’ve learned to ask:  

- *Does this solve a real problem?*
- *Can we go another year without doing this year and regreting it?*
- *Will this let us ship faster, or add bureaucracy?*

My rule of thumb now? Start boring. Get the monolith right. Split when you’re screaming. And for heaven’s sake, invest in your tooling at the right time. 

---

### **The Question We Should Be Asking**  

The microservices debate often feels like developers arguing about favorite text editors. What if we shifted the conversation? Instead of “monoliths vs. microservices,” let’s ask:  

- *How do we build systems that bend without breaking as teams scale?*
- *How do we prevent architecture from becoming innovation tax?*

I don’t have all the answers. But after 12 years of watching trends come and go, here’s what I know: The best architecture doesn't come with a prescription—it’s the one your team really needs but doesn't know it yet and once its there, you can't ever imagine going back.