# lessons-learned
Collection of summaries and lessons learned for various development tasks\projects.

## Shadow Test Development (01/2023 - 07/2023)

### Overview

List of deliverables produced during this development:
- [Software Test Suite (sts.exe)](https://github.com/zspace/system.next/tree/4fcdabe0a3cbf488d76e2d5311cb5c2a6b913c95/src/sts)
- [Test Fixture Firmware](https://github.com/zspace/system.stylus-test)
- [Report Visualization Post-Processing Script](https://github.com/zspace/system.next/tree/2023-07-18/src/sts/visualization)
- [Various Shared Documents](https://drive.google.com/drive/folders/1wXSDqC41-oXLo9gca-oo_jmC6_VZsKjb?usp=sharing)
  
All software development was tracked using Jira tickets:
- PEHW-2
- PEHW-3
- PEHW-4
- PEHW-7
- PEHW-8
- PEHW-9
- PEHW-10
- PEHW-12
- PEHW-21
- PEHW-37

Each of my commits were automatically linked via Jira/GitHub integration using PEHW-X tags within my 
git commit messages. The intent of these tickets were to track features/deliverables defined at the 
outset of the project. However, no subsequent meetings to discuss specific ticket progress were ever 
scheduled. All weekly progress reports were handled in a overall Shadow Stylus progress meeting which 
covered ALL developmental tasks (hardware/software/manufacturing). This method of update worked, but 
if the long term intent is to track progress using some issue tracking system, then some amount of 
additional management to both establish accountability and to help prioritize tasks.

### Test Fixture Firmware/Hardware Development
Test fixture firmware/hardware development was a smooth process. Getting the initial test 
fixture firmware up and running was straightforward due to the decision to use the same 
microcontroller as a Shadow type stylus (RP2040). Developing libraries to interface with the 
TCA9548A I2C multiplexer and Si1134 UV/Visible light sensors were trivial. All communication 
was verified using a logic analyzer. This task is about as aligned with my skillset as tasks 
can get, so very little risk was expected and very few problems arose as a result.

The [test fixture PCB](https://drive.google.com/drive/folders/11jU83LX4VfU5nPACNiEmDud2YjdDnlWJ?usp=drive_link)
could be improved in several ways:  
- Utilize multiple layers. The initial layout was to try to get all the routing done on a single layer with 
as short of traces as possible which led to an odd looking board. Per recommendation, a 2nd ground layer was 
added for additional structural support, but overall layout could have been improved by adding vias and 
using the 2nd layer for signals as well.
- Connectors. Use some form of connector for the Si1134 sensors. De-soldering wires to replace parts is 
annoying.
- Only assemble as many boards as needed. Some of the above issues could have been resolved in a board spin 
but because I had already soldered most of our part supply to the initial layout, the additional cost 
of purchasing additional components didn't make sense.
- Design board in conjunction with overall mechanical structure for proper wire routing. Initial 
PCB design was a simple translation from breadboard to PCB without much thought for how this was going 
to fit into a final hardware design. Bigger picture of final product needs to be considered.

The design of the mechanical test fixture was difficult due to the lack of design experience as a result 
of not having a mechanical engineer on staff. This task was passed around to multiple parties, and in my 
opinion, took much longer than it should have for a relatively simple design that has very few moving parts. 
Not having someone whose primary task was to complete the test fixture mechanical design early on resulted 
in the test fixture being the major blocker for hardware/software integration.

### Software Test Suite (STS) Development
The initial design goals were:
- Minimize training by making new test application as similar to the Classic test application as possible.
- Simple user interface.
- English/Chinese localization.
- Multiple styluses testable simultaneously (in parallel).

Keeping the application as similar to the Classic test application meant that STS was also going to be
a console application. For all new Window's based console application, it is recommended to ["replace the
classic Windows Console API with virtual terminal sequences."](https://learn.microsoft.com/en-us/windows/console/classic-vs-vt).
This is a great idea from a portability standpoint to try and use virtual terminal sequences, but from a 
practical standpoint, Windows Console API calls are still required to turn on this feature in the first 
place, which completely kills the idea of true portability for your program. Anecdotally, it also appears 
that only an ill defined subset of virtual sequences have actually been implemented in Windows terminal, 
meaning that some formatting tasks still require Windows Console API calls, further destroying the idea 
of portability.

English/Chinese localization was stressed at the offset of the project, but it appears that operators 
could have been trained on a purely english system. This was my first program that had some degree of 
localization built in so it was a good learning experience nonetheless but it's still to be seen how 
much this feature will be used in practice. Due to supporting both English/Chinese, input to STS was 
also limited to keystrokes common between all keyboard mappings and character sets. Simple, but 
limiting.

A far more robust option would have been to ditch the idea of creating a console applicaton and use a 
GUI framework that natively supports localization and multi-threading. The action of pressing a rendered 
button is much more easily translateable to any language. Trying to find a font that both supports 
English and Chinese characters and looks okay is much easier said than done. Although antiquated by 
today's standards, a simple C# Windows Form Application would have probably provided a much better user 
experience. Having to basically design all of the localization, multi-threading, and window drawing 
operations were in hindsight an unnecessary effort when an off the shelf framework could have been used.

### Miscellaneous
For all deliverables, more robust startup/continuous built-in tests (SBIT/CBIT) could be implemented to 
identify and handle more failures in a deterministic manner. Given the small audience for most of these 
deliverables, and the expected short manufacturing run of Shadow type styluses, the decision to not do 
a full failure analysis of the test fixture system is acceptable. Regression testing could also be automated, 
as most of my testing was done using a special debug console only accesible by running the Debug build of sts.exe.

Trying to be save cost by repurposing a Mako laptop as a test PC was the wrong decision in hindsight. 
The majority of the software development cycle was conducted on a much more capable Inspire laptop. This 
capability masked some inefficiencies in my code, but it also introduced some seemingly new errors that 
were never observed during development; specifically the 'device cannot start' issue (PEHW-37) which was 
only ever observed on a far less capable Mako laptop. 

The final STS application is more robust due to integration testing on a Mako laptop, but it's hard not to 
think that there was major time wasted chasing bugs specific to a platform that we now not going to use for 
testing. Ultimately, the cost of procurring an Inspire laptop specific for factory testing is far less than 
the engineering cost it took chasing bugs that only ever manifested on a platform that we are no longer even 
going to use since it's incapable of running the final stylus functional test.

