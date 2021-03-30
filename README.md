# LocoVIS-GIS
Locomotive Tracking and Logging GIS system for Garry's Mod Locomotive Simulation

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

Tickets use an algorithm to determine how long it *should* take a train to go from point `A` to point `B`, this allows trains to be late. When a ticket is added, if its the first ticket in the queue, it will have its departure time to the next rounded 5 minutes. Trains that are over 0 minutes late are considered "late", and any over 30 minutes late are considered "very late", this is also displayed on the map, with the color of their Train ID going from Green (ontime), to Orange (late), and Red (very late).

If a Destination you have entered doesnt exist on the current map, the ticket will have to be manually removed when the train reaches its destination.

Once a train has reached its destination, the ticket is removed from the ticket queue, and your "Completed Jobs" data gets rasied up 1.


**Command Examples:**
```
/gis 3017 job "load" "coal" "derrickson"
/gis 3017 job "load" "coal" "derrickson", "unload" "coal" "dumper"
/gis 3017 job "load" "coal" "derrickson", "unload" "coal" "dumper", "return" "coal" "wolfram"
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
