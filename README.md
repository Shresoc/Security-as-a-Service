# Security-as-a-Service with LimaCharlie EDR (SecOps Cloud Platform)
Creation of entire Security-as-a-Service infrastructure with VMs, EDR, and other inbuilt Security as a Service features.
![First](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/2a5d43f9-8867-4e83-94b3-f744c38a19ed)

LimaCharlie is a very powerful “SecOps Cloud Platform”. It not only comes with a cross-platform EDR agent, but also handles all of the log shipping/ingestion and has a threat detection engine. I am a huge fan of LimaCharlie for many reasons, one of which is that they have a free tier for up to two systems which is what allows me to make it an instrumental part of this project.

![2 detections](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/d7b4890f-78cc-4595-8230-aa9d162eaaf4)

LimaCharlie is a game-changer in the realm of enterprise security. This platform offers a holistic approach to protection, consolidating essential cybersecurity functionalities and doing away with the hurdles of integration and the emergence of security gaps.

The SecOps Cloud Platform stands as a singular, cohesive framework for crafting bespoke security solutions. Its arsenal includes open APIs, a centralized telemetry hub, and automated detection and response systems, heralding a new age in cybersecurity.

By streamlining the acquisition, deployment, and integration of top-tier cybersecurity tools, the SecOps Cloud Platform ensures that every organization receives a defense that is not just comprehensive but also finely attuned to its unique demands. This is the future of cybersecurity, mirroring the tailored support that IT Clouds have long provided to enterprises.


# LimaCharlie web UI.

A) Click “Sensors” on left menu

B) Click your active Windows sensor

![Sensor List](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/f2cfe8f9-9519-4698-921b-1febf4592c4a)

C) On the new left-side menu for this sensor, click “Processes”

![Processes ](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/7b7ec61a-6cee-40c7-942b-450e2bc1a279)

# Exploring what is returned in the process tree. 

1) I can’t stress enough how important it is for an analyst to have familiarity with the most common processes you’ll encounter on even a healthy system. As the saying goes, “you must know normal before you can 
   find evil.” For some helpful resources in “knowing normal”, check out the “Hunt Evil” poster from SANS and sign up for a account at EchoTrail.

2) A process carrying a valid signature (Signed) is often (almost always) going to be benign itself. However, even legitimate signed processes can be used to launch malicious processes/code.

3) One of the easiest ways to spot unusual processes is to simply look for ones that are NOT signed.

![Unusual Process](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/e5245b2c-115c-482a-b1b2-6822ca35290b)

4) In my example, my C2 implant shows as not signed, and is also active on the network.

![Unusual Process 2](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/17bfc122-ae6d-48ce-8878-75d6e0f323eb)

5) Notice how quickly we are able to identify the destination IP this process is communicating with.

   # “Network” tab on the left-side menu

   ![Networks](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/94d286f5-3cbd-4e25-b7a7-abe26da018f0)

   # “File System” tab on the left-side menu

   ![File Systems Downloads](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/4f641947-177b-458b-a16d-597c9d7008af)

   1) Browse to the location we know our implant to be running from - C:\Users\User\Downloads

   2) Inspect the hash of the suspicious executable by scanning it with VirusTotal.
  
![Virus Total](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/dd3470f2-0a7e-4ce6-951e-659a209dc0ee)

3) Pro Tip: While it says “Scan with VirusTotal,” what it’s actually doing is querying VirusTotal for the hash of the EXE. If the file is a common/well-known malware sample, you will know it right away. However, “Item not found” on VT does not mean that this file is innocent  just that it’s never been seen before by VirusTotal. This is an important lesson for any analyst to learn — if you already suspect a file to be possible malware, but VirusTotal has never seen it before, trust your gut. This actually makes a file even more suspicious because nearly everything has been seen by VirusTotal, so your sample may have been custom-crafted/targeted which ups the ante a bit. In a mature SOC, this would likely affect the TLP of the IOC and/or case itself.

#  “Timeline” on the left-side menu of our sensor. This is a near real-time view of EDR telemetry + event logs streaming from this system.

![Time line](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/bf10ab8c-0b90-41b5-a0fe-a3389e7c33ff)

![Time line 3](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/c80aeef8-f71c-4336-82f0-c089c2dce6ad)


1) filtering your timeline with known IOCs (indicators of compromise) such as the name of implant or the known C2 IP address.
   
![Time Line suspicious Process](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/d36d2a3c-f842-41f0-91fd-bb9dc8b8f531)

2) If you scroll back far enough, should be able to find the moment your implant was created on the system, and when it was launched shortly after, and the network connections it created immediately after.

3) Examine the other events related to your implant process, you’ll see it is responsible for other events such as “SENSITIVE_PROCESS_ACCESS” from when we enumerated our privileges in an earlier step. This particular event will be useful later on when we craft our first detection rule.

   # <B> Now Let’s Detect It </B>
a) Since lsass.exe is a known sensitive process often targeted by credential dumping tools, any good EDR will generate events for this.

b) Drill into the Timeline of your Windows VM sensor and use the “Event Type Filters” to filter for “SENSITIVE_PROCESS_ACCESS” events.

   1) There will likely be many of these, but pick any one of them as there isn’t much else on this system that will be legitimately accessing lsass
      
      ![Sensitive Process](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/6d60a77d-6851-488f-bb72-750bec459944)

  2) Now that we know what the event looks like when credential access occurred, we have what we need to craft a detection & response (D&R) rule that would alert anytime this activity occurs.

      a) Click the button "Craft detection rule button" to begin building a detection rule based on this event.

     ![Build D  R Rule for sensitive processes](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/1b17468c-54dd-4253-b115-5d523885bd3e)

 
    b) In the “Detect” section of the new rule, remove all contents and replace them with this
         
         event: SENSITIVE_PROCESS_ACCESS
         op: ends with
         path: event/*/TARGET/FILE_PATH
         value: lsass.exe
    c) We’re specifying that this detection should only look at SENSITIVE_PROCESS_ACCESS events where the victim or target process ends with lsass.ex
       For posterity let me state, this rule would be very noisy and need further tuning in a production environment, but for the purpose of this learning exercise, simple is better.
   
   d) In the “Respond” section of the new rule, remove all contents and replace them with this

      - action: report
        name: LSASS access
   e) We’re telling LimaCharlie to simply generate a detection “report” anytime this detection occurs. For more advanced response capabilities, check out the docs. We could ultimately tell this rule to do all          sorts of things, like terminate the offending process chain, etc. Let’s keep it simple for now.
   
   ![Detection Rule ](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/2e2888a4-96fb-4e4d-8e7b-c38d999f56db)

   f) Scroll to the bottom of the raw event and click “Test Event” to see if our detection would work against this event.

 ![Test Event](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/58b6d568-30fa-494d-9ed1-2d20d3e9abcb)
      
   
   g) Notice that we have a “Match” and the D&R engine tells you exactly what it matched on.


# Let’s Be Bad Again, Now with Detections!

1) You’ve just detected a threat with your own detection signature! Expand a detection to see the raw event

![Detections ](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/c05d0d8d-d524-4be1-a75c-a1a3811d1dac)

2) Notice you can also go straight to the timeline where this event occurred by clicking “View Event Timeline” from the Detection entry.
   
![Event Timeline](https://github.com/Shresoc/Security-as-a-Service/assets/168186856/b9a0ca94-5fc8-4f3e-addc-c0446c62d0f4)


