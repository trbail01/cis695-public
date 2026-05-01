# Camp Lab Sequence

This document proposes a six-lab sequence for a one-day IoT summer camp based on the current project structure, selected devices, and stated instructional constraints.

The sequence is designed to support beginner-friendly participation while still giving students a realistic introduction to Linux, networking, automation logic, and applied IT workflows.

---

## Recommended Day Flow

This sequence works best as a one-day camp with a short community-building activity before students begin the technical labs.

| Segment | Approx. Time | Purpose |
|---------|--------------|---------|
| Welcome and introductions | 15 min | Set expectations, explain camp purpose, assign groups |
| Team-building activity | 15-20 min | Help students meet teammates and think about smart-home problems |
| Lab 1 | 30 min | Introduce IoT roles and the Raspberry Pi environment |
| Lab 2 | 35 min | Observe sensors and state changes |
| Lab 3 | 35 min | Control smart devices through Home Assistant |
| Break / lunch | 45-60 min | Reset and informal conversation |
| Lab 4 | 45 min | Build and test automation rules |
| Lab 5 | 50-60 min | Team design challenge |
| Lab 6 | 30-40 min | Showcase, reflection, and recruitment close |

This structure keeps the day active, gives students time to talk with one another, and moves from observation to creation in a logical order.

---

## Lab 1: Intro to Smart Home Systems

**Goal:**  
Introduce students to the idea of a smart home system and help them identify the roles of controllers, sensors, and smart devices in the camp environment.

**Materials:**  
- Raspberry Pi with Raspberry Pi OS and Home Assistant access  
- Keyboard, mouse, monitor, and power supply  
- Camp network connection  
- Printed quick-start guide or instructor handout  
- One sensor and one smart device for demonstration

**Setup:**  
- Boot each Raspberry Pi before students arrive  
- Confirm students can log in to the Pi or reach the local interface assigned for the activity  
- Verify the room is connected to the camp wireless network  
- Prepare a simple diagram showing controller, sensor, and smart device roles

**Student Tasks:**  
- Identify the Raspberry Pi, sensor, and smart device in the lab setup  
- Log in to the Raspberry Pi or access the local interface provided by the instructor  
- Record the name and role of each device  
- Observe a live demonstration of a sensor causing a smart device to react  
- Explain how information moves from the physical world into the system

**Expected Outcome:**  
Students can describe a basic IoT system, identify the Raspberry Pi as the local controller, and explain the difference between sensors and smart devices.

**Recruitment Connection:**  
This lab introduces students to foundational IT topics such as systems thinking, device roles, and local infrastructure. It helps students see that IT is not only about using apps, but about understanding how connected systems work.

---

## Lab 2: Sensors and Data

**Goal:**  
Help students understand how sensors generate data and how event-driven systems use changing device states.

**Materials:**  
- Aqara Motion Sensor P1  
- Aqara Door and Window Sensor  
- Raspberry Pi with Home Assistant  
- Zigbee coordinator already paired to the room environment

**Setup:**  
- Pair sensors to the room controller before the session, or prepare one sensor for live pairing if time allows  
- Confirm sensor names appear clearly in Home Assistant  
- Place the motion sensor and door sensor where students can safely trigger them

**Student Tasks:**  
- Open the Home Assistant dashboard and locate both sensors  
- Trigger the motion sensor and observe state changes in real time  
- Open and close the door sensor to observe boolean state changes  
- Record what events happened, what values changed, and how quickly updates appeared

**Expected Outcome:**  
Students can identify the difference between motion and contact sensors, explain the idea of a trigger, and observe how data moves from a physical event into a software dashboard.

**Recruitment Connection:**  
This lab connects hands-on device interaction to data, monitoring, and systems thinking. Students get early exposure to how IT professionals work with live systems, dashboards, and troubleshooting instead of only passive classroom examples.

---

## Lab 3: Smart Devices and Control

**Goal:**  
Show students how smart devices respond to commands and reinforce the difference between devices that sense information and devices that perform actions.

**Materials:**  
- TP-Link Smart Bulb or Kasa Smart Plug  
- Connected lamp or other safe demonstration device  
- Raspberry Pi with Home Assistant  
- Existing room network and paired devices

**Setup:**  
- Confirm the smart bulb or plug is already onboarded and reachable  
- Verify the connected lamp or demonstration device is safe for classroom use  
- Reset the room to a known state before the lab  
- Prepare a simple demonstration showing on, off, and any available brightness or color controls

**Student Tasks:**  
- Locate the assigned smart device in Home Assistant  
- Identify the device name, type, and room assignment  
- Turn the device on and off using the dashboard  
- If using a bulb, test an additional setting such as brightness or color  
- Compare the smart device to the sensors from the previous lab  
- Discuss what kinds of everyday tasks could be automated using this device

**Expected Outcome:**  
Students can use Home Assistant to send a command to a smart device and explain how smart devices differ from sensors.

**Recruitment Connection:**  
Students see that IT systems do more than collect data; they also control real devices. This supports conversations about user experience, remote management, and applied technology careers.

---

## Lab 4: Automation Rules

**Goal:**  
Bring sensors and smart devices together by having students create simple automation logic in Home Assistant.

**Materials:**  
- Aqara Motion Sensor P1 or door sensor  
- TP-Link smart bulb or Kasa smart plug  
- Raspberry Pi with Home Assistant  
- Lab worksheet for logic planning

**Setup:**  
- Confirm the sensor and smart device are both online and visible in Home Assistant  
- Prepare one simple example automation for instructor demonstration  
- Reset each room device to a known state before the lab begins  
- Make sure students know which device names belong to their group

**Student Tasks:**  
- Choose a trigger such as motion detected or door opened  
- Choose an action such as turning on a bulb or smart plug  
- Build the automation in Home Assistant with instructor guidance  
- Predict what should happen when the trigger occurs  
- Test the automation and observe the result  
- Add or adjust a condition, delay, or naming step if needed

**Expected Outcome:**  
Students create and test a basic automation and can explain the rule in plain language.

**Recruitment Connection:**  
This lab gives students a rewarding build moment that combines logic, troubleshooting, and visible results. It helps frame the IT program as a place where students can design systems that solve real problems.

---

## Lab 5: Student Design Challenge

**Goal:**  
Give students more ownership by asking them to design a small IoT solution using the devices and ideas introduced earlier in the camp.

**Materials:**  
- Raspberry Pi with Home Assistant  
- Available room sensors and smart devices  
- Planning worksheet or challenge prompt sheet  
- Whiteboard or paper for sketching ideas

**Setup:**  
- Leave prior devices paired and ready for reuse  
- Prepare a short list of design prompts such as room entry alert, automatic light, or status indicator  
- Review group roles if students need structure, such as builder, tester, recorder, and presenter  
- Confirm each group has access to at least one sensor and one smart device

**Student Tasks:**  
- Form a small group and choose a design challenge prompt  
- Identify the problem the group wants to solve  
- Decide which sensor will provide input and which smart device will provide output  
- Write a simple plan describing the trigger, conditions, and action  
- Build the automation or modify an earlier one to fit the new goal  
- Test the design, note any problems, and improve it  
- Prepare a short explanation of what the system does and why the group designed it that way

**Expected Outcome:**  
Students work as a team to build or adapt a simple automation that solves a realistic problem and can be explained clearly to others.

**Recruitment Connection:**  
This lab highlights creativity, teamwork, and practical problem-solving. It helps students see that IT work often involves building solutions together rather than completing isolated tasks.

---

## Lab 6: Showcase and Reflection

**Goal:**  
Close the camp by giving students an opportunity to present their work, reflect on what they learned, and connect the experience to future study in IT.

**Materials:**  
- Raspberry Pi with Home Assistant  
- Student-built automation or device demonstration  
- Sensors and smart devices used in earlier labs  
- Reflection handout or short survey  
- Parent showcase space

**Setup:**  
- Confirm each group has a working demonstration or a backup example to discuss  
- Set a simple presentation format, such as problem, devices, automation, and result  
- Prepare reflection questions in printed or digital form  
- Organize the room so visitors can move safely between demonstrations

**Student Tasks:**  
- Review the group project and make sure the demonstration is ready  
- Decide who will introduce the problem, explain the devices, and show the result  
- Present the project to the class, instructor, or visitors  
- Demonstrate the trigger and resulting smart device action if the system is working live  
- Explain one challenge the group faced and how it was solved  
- Complete the reflection handout about skills learned and interest in future IT study

**Expected Outcome:**  
Each group completes a short presentation that demonstrates understanding of the system and reflects on the camp experience.

**Recruitment Connection:**  
The showcase gives students and families a visible example of applied IT learning. It reinforces confidence and creates a strong recruitment moment by linking hands-on success to future study in IT.

---

## Sequence Notes

- The six labs fit the current project assumptions for a single-day camp with mixed-experience high-school students.
- The sequence uses the existing instructional scope: device roles, dashboards, Home Assistant, sensors, smart devices, automation logic, teamwork, and presentation.
- Labs can be delivered in small groups of up to four students with shared room devices and per-student Raspberry Pi access.
- The final showcase supports the project goal of using hands-on IoT activities as a student recruitment tool for the IT program.
- A short Python or scripting demo can still be added as an optional extension if time, staffing, and student readiness allow.
