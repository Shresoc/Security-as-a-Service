# Security-as-a-Service with LimaCharlie EDR (SecOps Cloud Platform)
Creation of Entire Security-as-a-Service infrastructure with VMs, EDR, and other inbuilt Security as a Service features.

LimaCharlie is a very powerful “SecOps Cloud Platform”. It not only comes with a cross-platform EDR agent, but also handles all of the log shipping/ingestion and has a threat detection engine. I am a huge fan of LimaCharlie for many reasons, one of which is that they have a free tier for up to two systems which is what allows me to make it an instrumental part of this project.

![2 detections](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/d7b4890f-78cc-4595-8230-aa9d162eaaf4)

LimaCharlie is a game-changer in the realm of enterprise security. This platform offers a holistic approach to protection, consolidating essential cybersecurity functionalities and doing away with the hurdles of integration and the emergence of security gaps.

The SecOps Cloud Platform stands as a singular, cohesive framework for crafting bespoke security solutions. Its arsenal includes open APIs, a centralized telemetry hub, and automated detection and response systems, heralding a new age in cybersecurity.

By streamlining the acquisition, deployment, and integration of top-tier cybersecurity tools, the SecOps Cloud Platform ensures that every organization receives a defense that is not just comprehensive but also finely attuned to its unique demands. This is the future of cybersecurity, mirroring the tailored support that IT Clouds have long provided to enterprises.


# Let’s hop into the LimaCharlie web UI and check out some basic features.

A) Click “Sensors” on left menu

B) Click your active Windows sensor

![Sensor List](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/f2cfe8f9-9519-4698-921b-1febf4592c4a)

C) On the new left-side menu for this sensor, click “Processes”

![Processes ](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/7b7ec61a-6cee-40c7-942b-450e2bc1a279)

# Spend a few minutes exploring what is returned in the process tree. Hover over some of the icons to see what they represent.

1) I can’t stress enough how important it is for an analyst to have familiarity with the most common processes you’ll encounter on even a healthy system. As the saying goes, “you must know normal before you can 
   find evil.” For some helpful resources in “knowing normal”, check out the “Hunt Evil” poster from SANS and sign up for a account at EchoTrail.

2) A process carrying a valid signature (Signed) is often (almost always) going to be benign itself. However, even legitimate signed processes can be used to launch malicious processes/code.

3) One of the easiest ways to spot unusual processes is to simply look for ones that are NOT signed.
