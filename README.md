# LocoVIS-GIS
Locomotive Tracking and Logging GIS system for Garry's Mod Locomotive Simulation

## Initial Setup
### Antenna

Inside the antenna e2 is the locomotive config:
```
    PREFIX = "/gis"    #Chat Command Prefix.
    Type = 1 #0 = Other, 1 = Locomotive, 2 = Passenger, 3 = Portable, 4 = Vehicle.
    Railroad = "CP" #Railroad
    RoadNumber = "3017"   #Locomotive Road Number.
    UnitClass = "CP3017"   #Eg, "GP38-2", "DFB".
```

Variable | Takes | Description
--- | --- | ---
PREFIX | `string` | The prefix used before commands.
Type | `number` | Vehicle Type, this will mainly be Locomotive. (Passenger is for passenger specific DMUs)
Railroad | `string` | The railroad name, this should be short, use the reporting mark.
RoadNumber | `string` | Road/Runningnumber, this can be overwritten by setting a string on the `Roadnumber:string` input, used in random number generators.
UnitClass | `string` | This should either be the locomotive model, or reporting mark plus the roadnumber.

The top of the antenna has a arrow, this arrow should always be facing towards the side considered the front of the locomotive.

![](/docs/images/Screenshot_33.png)

### GIS Screen

If you are mounting a GIS Screen inside the locomotive cab, it is recommended that you set the Expression 2 model to `models/magtrainslocos/functiondisplay.mdl` (uncomment the `@model` at the top of the GIS Screen e2), and that you set `FunctionDisplayMode` in the config to `1`, this will change the display from dispatching mode, to locomotive mode, this will make the interaction be via the number keys (not the numpad).

Simply look at the EGP screen while in a linked pod controller seat, and press the according key for the button you would like to press. There are 8 buttons, it goes 1 on the far left, to 8 on the far right.

The EGP screen model can be set by putting this in console `wire_egp_model models/magtrainslocos/functiondisplay_screen.mdl`.

![](/docs/images/Screenshot_34.png)

## Wiring
### Antenna
1. `Locomotive:entity` to the Locomotive Body.
2. `RLCPT:entity` to your RLCPT2 chip (does not work with PT1 or others, this is optional, but recommended).
3. `Roadnumber:string` For random roadnumber generators, otherwise it will use the Roadnumber input inside the E2 config.
4. `Disable` This disabled the Antenna, this is for when a locomotive is in MU, note that this will also stop it from updating so you will not gain any miles on the chip while it is disabled.
5. `MPH` MPH Speed input for the chip, this MUST be wired.
### GIS Screen
1. `EGP:wirelink` EGP wirelink.
2. `Antenna:entity` This is for if you have a GIS Screen mounted within the cab of your locomotive, it gives you direct readings for the job tickets, so this is highly recommended. Note: If the antenna chip is updated or refreshed, it will unlink from the screen, just wiremod things.
3. `User:entity` This wires to the EGP (yes its an output of the EGP, this is for the touch screen functions)
4. `Driver:entity` This wires to your Pod Controller, this is for when a GIS Screen mounted within the cab of your locomotive, allows the use of your number keys for using the screen while in a seat.

## Chat Commands

Using the `PREFIX` string before your message, you can add, edit, and remove data relating to your train. Prefixs are set within the Antenna Expression 2.
```
PREFIX = "/gis"    #Chat Command Prefix.
```
1. The prefix is followed by an identifier, this can be a: Train ID, Running/Roadnumber, Radio ID, or the UID (the Antennas entity ID, ex: `2317`).
2. The identifier is followed by the change you want to make, this can be: `set` or `change`, `clr` or `clear`, `job`. **NOTE: `job` is for adding Job Tickets!**
3. The change is followed by the entry you want to change, which can be found in the table below.
4. And the entry is followed by the data you want to change the entry too

> **NOTE:** Entries that take strings, data should be nested in quotation marks `"[data here]"`

**Command Example:**
```
/gis 3017 set trainid 123
```
**Returns:**
```
LocoVis [CMD] "Train ID" Set to: 123.
```

## Command Table
Change | Entry | Takes | Description | Notes
--- | --- | --- | --- | ---
`set`,`change` | id | `non-nested string` | Sets the train ID. | No spaces, Numbers and characters are accepted.
`set`,`change` | trainid | `non-nested string` | Sets the train ID. | No spaces, Numbers and characters are accepted.
`set`,`change` | class | `non-nested string` | Sets the unit class. | Generally, you dont want to be messing with this.
`set`,`change` | unitclass | `non-nested string` | Sets the unit class. | Generally, you dont want to be messing with this.
`set`,`change` | traindescription | `nested string` | Sets the trains description. | Nested string.
`set`,`change` | description | `nested string` | Sets the trains description. | Nested string.
`set`,`change` | desc | `nested string` | Sets the trains description. | Nested string.
`set`,`change` | disc | `nested string` | Sets the trains description. | Nested string.
`clr`,`clear` | id | `none` | Clears the train ID. |
`clr`,`clear` | trainid | `none` | Clears the train ID. |
`clr`,`clear` | class | `none` | Clears the unit class. | Generally, you dont want to be messing with this.
`clr`,`clear` | unitclass | `none` | Clears the unit class. | Generally, you dont want to be messing with this.
`clr`,`clear` | traindescription | `none` | Clears the train description. |
`clr`,`clear` | description | `none` | Clears the train description. |
`clr`,`clear` | desc | `none` | Clears the train description. |
`clr`,`clear` | disc | `none` | Clears the train description. |
`version` | none | `none` | Returns the codebase version. |
`reset` | none | `none` | Resets/refreshes the e2. |
`refresh` | none | `none` | Resets/refreshes the e2. |

## Job Tickets
Job Tickets are a way of giving a set of instructions/job to a crew. Ex, a dispatcher will set a Job Ticket for train `X` to goto location `A` and unload their cars. 
1. These will be used in automatic timetable generation. 
2. Currently, anyone can set tickets on any train. 
3. The tickets automatically finish and remove when the train has reached their destination, and will move onto the next ticket (if one is available).

Job Tickets can have multiple strings, and multiple jobs set at once. Ticket commands contain 3 strings (4 optional):
1. If they are Loading, or Unloading, this can also be set to "Return" or anything you desire
2. The type of load the train is hauling, Eg: Coal, Grain, Boxcars, etc
3. The destination, where the train has to go. The table of destinations is below.
4. Origin Location, not used a whole lot, it adds an approx location if you dont add one. Used mainly in yards, to get engine from engine shed to a consist.

Tickets use an algorithm to determine how long it *should* take a train to go from point `A` to point `B`, this allows trains to be late. When a ticket is added, if its the first ticket in the queue, it will have its departure time to the next rounded 5 minutes. Trains that are over 0 minutes late are considered "late", and any over 30 minutes late are considered "very late", this is also displayed on the map, with the color of their Train ID going from Green (ontime), to Orange (late), and Red (very late).
If the ticket is second or more in the queue, when the first ticket finishes, it will add 30 minutes to the departure time, this allows crews time to do work at their destination, Eg: Take coal cars from Derrickson to the Coal Dumper, Unload coal cars (gives you 30 minutes to do this), and then however long it determines it takes to get to your next destination.

If a Destination you have entered doesnt exist on the current map, the ticket will have to be manually removed when the train reaches its destination.

Once a train has reached its destination, the ticket is removed from the ticket queue, and your "Completed Jobs" data gets rasied up 1.

## Job Ticket Command Table
Change | Entry | Takes | Description
--- | --- | --- | --- 
`job` | none | `nested strings` | Adds a Ticket
`clr`,`clear` | job | `number` | Removes a Ticket
`clr`,`clear` | jobs | `none` | Removes all jobs assigned to the locomotive

**Command Examples:**
```
/gis 3017 job "load" "coal" "derrickson"
/gis 3017 job "load" "coal" "derrickson", "unload" "coal" "dumper"
/gis 3017 job "load" "coal" "derrickson", "unload" "coal" "dumper", "return" "coal" "wolfram"
/gis 3017 clear job 12345
/gis 3017 clear jobs
```

## Destination Table
Map | Destination |
--- | ---
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "wolfram"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "wolfram yard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "wolfram station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "wolfram passenger station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragonsrock"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragons rock"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragonsrock station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragonsrock passenger station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragons rock station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragons rock passenger station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragons rock siding"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dragonsrock siding"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dumper"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "dump"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "coaldumper"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "lumber"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "lumber yard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "lumberyard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "redwall northern petrol"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "redwall northernpetrol"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "redwall np"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "np"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "northern petrol"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "northernpetrol"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "redwall siding"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "redwall station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "redwall passenger station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "kahuna"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "kahuna station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "kahuna passenger station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "kahuna northern petrol"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "kahuna northernpetrol"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "kahuna np"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "crystal lake"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "crystallake"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "scrap"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "scrapyard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "scrap yard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "blackrock"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "derrickson"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "derricksonw"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "derrickson w"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "derricksonwest"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "derrickson west"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "derrickson yard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "coalloader"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "coal loader"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "cement plant"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "cement"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "farm"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rainbow river"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rainbowriver"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rainbow river station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rainbowriver station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rainbow river passenger station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rainbow river stop"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rainbowriver stop"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "crucible"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "crucible station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "crucible passenger station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "twinbridges"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "twin bridges"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "arrowsfoley"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "arrows foley"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "arrows"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rockfort"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rockfort yard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rockfortyard"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rockfort siding"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rockfort station"
`gm_sunsetgulch`, `gm_sunsetgulch_x` | "rockfort passenger station"

## Local Files

Trips are saved to your local files on removal of the antenna chip. These can be found at:
```
\garrysmod\data\e2files\locovis\saves
```
Files names are as follows `RAILROAD_UNITCLASS_ROADNUMBER.txt`, if enough identifiers are missing to not make a file name, it will be named `unknown_DATE.txt`.
