This page will cover the process of adding a custom component using the template script provided in the [sample component submodule](https://github.com/nasa-itc/sample/tree/34260dd8a8d929b6d36c8805f08754aa847d6e97).

## Preparing
Start by creating a new git repository and adding your component as a submodule:
```bash
git submodule init
git submodule add -f -b main <LINK TO SUBMODULE> ./components/<COMPONENT NAME>`
```

Next, run the generation script: `./nos3/components/sample/generic_template.sh <COMPONENT NAME>`. This will create a directory in `./nos3/components` with the name you enter. Open the README file in that directory. 

**Note:** your component name must not be greater than 10 characters (there are workarounds to allow longer names but they won't be covered here).

Add the generated files to your submodule and commit them:
```bash
cd ./components/<COMPONENT NAME>
git add * && git add .gitignore
git commit -m "Initial component template based on version 0.0.0"
```

## Flight Software

Next we need to connect our component to the flight software. Start by opening `./nos3/cfg/nos3_defs/cpu1_cfe_es_startup.scr` - you will see a table that defines all the components for [[Core Flight Executive|CFE]]. The description of each field is present towards the end of the file:\

```
Startup script fields:
1. Object Type      -- CFE_APP for an Application, or CFE_LIB for a library.
2. Path/Filename    -- This is a cFE Virtual filename, not a vxWorks device/pathname
3. Entry Point      -- This is the "main" function for Apps.
4. CFE Name         -- The cFE name for the the APP or Library
5. Priority         -- This is the Priority of the App, not used for Library
6. Stack Size       -- This is the Stack size for the App, not used for the Library
7. Load Address     -- This is the Optional Load Address for the App or Library. Currently not implemented so keep it at 0x0.
8. Exception Action -- This is the Action the cFE should take if the App has an exception.
0 = Just restart the Application 
Non-Zero = Do a cFE Processor Reset
```
Most of these don't matter just for adding our component, but the first 3 are important:
- Use `CFE_APP` for object type
- Use the exact name of your component for the path/filename 
- Make sure your entry point matches the main function for your component
	- To check this, open `./nos3/components/<COMPONENT NAME>/fsw/src/<COMPONENT NAME>_app.c` and confirm the name of the main function denoted by the comment `Application entry point and main process loop`
- For the rest of the fields:
	- CFE name can be any shortened abbreviation of your component name
	- Priority depends on your needs but can be left at 0 for testing
	- Stack size also depends on your needs, but all other CFE apps have either 16384 byte or 32768 byte stacks, so choose one of those sizes
	- Load address is not implemented; keep at `0x0`
	- Exception action should be 0

Next, open `./nos3/cfg/nos3_defs/targets.cmake` and CTRL+F for `# Components` which will bring you to the list of apps for the mission. Simply add `<COMPONENT NAME>/fsw` to the end of the list.

Next, open `./nos3/cfg/nos3_defs/tables/sch_def_msgtbl.c` - this is the message definition table for the scheduler. All we have to do here is add an include for our component's message ID header file; anywhere in the `Component Include Files` section, add a new line with `#include "<COMPONENT NAME>_msgids.h"`.

In the same `tables` directory, open `to_config.c`, the [[Telemetry Output]] table definition file, and add the same include to this file's `Component Include Files` section.

Now open `./nos3/cfg/sims/nos3-simulator.xml`. Under the `<components>` element, add the following block:
```xml
<COMPONENT NAME>
	<enable>true</enable>
</COMPONENT NAME>
```

Next, from your component's README.md file, scroll down to the `Simulation` section and copy the XML block. Open `./nos3/cfg/sc-full-config.xml` and paste that block in the `<components>` section.

Finally, we need to edit two scripts in `./nos3/scripts`. First, open `docker_checkout.sh` - this is the script that is used if you want to run your component in a standalone fashion. Change lines 36 and 46 to match your component name. 

Next, open `docker_launch.sh` - this is the script that runs when you do `make launch`, so we need to add our component to the list of things to launch. On line 116 you'll find the `Component simulators` section - copy any of the lines beginning with `gnome-terminal`, paste it onto a new line, and swap out the simulator/component name for your new custom component. 

At this point, your component is fully configured to be a part of the spacecraft. You can launch the simulator or follow the instructions in your component's README to launch your component standalone with the NOS Engine and no other components.

## Ground Software
If you complete all the FSW steps, you'll be able to successfully launch the simulator with your new component running - but you won't have any way to interface with your component from COSMOS/OpenC3. This part covers the configuration required for your component to communicate with the ground station. 

### COSMOS
Open `./nos3/gsw/cosmos/config/system.txt` - on line 21 you'll see the `Components` section for declaring target components. Add a new line for your component: `DECLARE_TARGET ../../COMPONENTS/<NAME> <NAME>` and repeat below for the radio: `DECLARE_TARGET ../../COMPONENTS/<NAME> <NAME>_RADIO`.

Open `./nos3/gsw/cosmos/config/tools/cmd_tlm_server/cmd_tlm_server.txt` and add a target definition for your app under the `INTERFACE DEBUG udp_interface.rb` section: `TARGET <COMPONENT NAME>`. Do the same for the component's radio under the `INTERFACE RADIO udp_interface.rb` section.