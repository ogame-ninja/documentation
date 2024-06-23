# Scripting Manual

# Intro

NJA Scripts are running in a supervised environment allowing to :

- have access to NJA API: NJA internal data structures and low-level functions
- stop/start/create scripts at any moment
- schedule tasks

For scripting <a href="https://github.com/mattn/anko">anko</a> Programming Language is used.

# Compiled script

[Documentation for compiled script](/doc/scripting/compiled)

# Programming Basics

## Code Comments

```go
// All below are comments and are not executed. This one is so called "one line comment", it comment out everything after '//' symbol

/*
Next one is
 several
  lines
   comment
*/
```

## Variables

```go
// Define a global variable called 'name' and assigning it with a value
name = "Johny"

// Changing the value of variable from 'Johny' to 'Tommy' by assigning (=) a new value
name = "Tommy" 
```

## Special Scripts

You can create a script with the exact name:  
`!global.ank` any variable or function inside will be available from any of your scripts.  
`!sleep.ank` will be executed before the bot goes to sleep mode.  
`!wake.ank` will be executed after the bot exit the sleep mode.  


## Special Functions

If your script has a function `func OnExit() {}`, it will be executed after the script is done being executed.

## Loops

```go
for i = 0; i < 5; i++ {
    Print(i)
}
```

```go
for el in [10, 20, 30, 40, 50] {
    Print(el)
}
```

```go
for key, value in {"a": "b", "c": "d", 1: 12, true: false} {
    Print(key, value)
}
```

```go
for shipID in ShipsArr {
    Print(shipID)
}
```

## How to sort

```go
sort = import("sort")
arr = [["c", 3], ["a", 1], ["b", 2]]
sort.Slice(arr, func(i, j) { return arr[i][1] < arr[j][1] })
Print(arr) // [[[a 1] [b 2] [c 3]]]
```

## How to use json

```go
json = import("encoding/json")
s = `{"a": 1, "b": 2.1, "c": "a string", "d": true}`
out = {}
json.Unmarshal(s, &out)
Print(out["a"])

out, err = JsonDecode(s) // This would also work
Print(out, err)
```
```go
fmt = import("fmt")
json = import("encoding/json")
s = NewShipsInfos()
s.Set(SMALLCARGO, 1)
s.Set(LARGECARGO, 2)
bytes, _ = json.Marshal(s)
Print(fmt.Sprintf("%s", bytes))
```

## How to debug errors

### syntax error

If you receive a message that looks like:  
```go
22:20:11 ERROR [[9:24] syntax error]
```
It means that you have a syntax error at line **9**, charaSendMessagecter **24**.  

### does not support member operation
```go
22:36:13 ERROR [[46:34] type invalid does not support member operation]
```
Means that you are trying to call a "method" on a variable that is not an "object".  
It would usually look like `notAnObject.method()`.  
One way to know what's going on is to `Print(notAnObject)`,
and rerun the script to know what's the value of `notAnObject`.  

### function wants X arguments but received Y
```go
23:10:21 ERROR [[24:23] function wants 1 arguments but received 3]
```
This happens when you call a function that only takes X argument, but you are giving it Y.  
Example:  
```go
planet = GetPlanet(1, 2, 3) // Triggers error: function wants 1 arguments but received 3
```
Should actually be something like:  
```go
planet = GetPlanet("1:2:3")
```

### no member named 'xxx' for struct
```go
23:56:42 ERROR [[29:12] no member named 'max' for struct]
```
This occurs when you try to get a property that does not exists.  
```go
planet, _ = GetPlanet("1:2:3")
Print(planet.Temperature.max) // Triggers error: no member named 'max' for struct
Print(planet.Temperature.Max) // This is the correct name
```

### function wants argument type X but received type Y
```go
00:02:03 ERROR [[73:40] function wants argument type func() but received type bool]
```
This happens when the wrong type of argument is given to a function.  
```go
func callback() {
    return true
}
ExecIn(1000, callback()) // Trigger error: function wants argument type func() but received type bool
                         // In this example, you are actually calling the callback function and giving
                         // "true" as a parameter to ExecIn.
ExecIn(1000, callback)   // This is the correct way.
Sleep(2000)
```

# Scripting API

## Constants

```go
ShipsArr = [SMALLCARGO, LARGECARGO, LIGHTFIGHTER, HEAVYFIGHTER, CRUISER, BATTLESHIP, COLONYSHIP, RECYCLER, ESPIONAGEPROBE, BOMBER, DESTROYER, DEATHSTAR, BATTLECRUISER, CRAWLER, REAPER, PATHFINDER]
DefencesArr = [ROCKETLAUNCHER, LIGHTLASER, HEAVYLASER, GAUSSCANNON, IONCANNON, PLASMATURRET, SMALLSHIELDDOME, LARGESHIELDDOME, ANTIBALLISTICMISSILES, INTERPLANETARYMISSILES]
TechnologiesArr = [ESPIONAGETECHNOLOGY, COMPUTERTECHNOLOGY, WEAPONSTECHNOLOGY, SHIELDINGTECHNOLOGY, ARMOURTECHNOLOGY, ENERGYTECHNOLOGY, HYPERSPACETECHNOLOGY, COMBUSTIONDRIVE, IMPULSEDRIVE, HYPERSPACEDRIVE, LASERTECHNOLOGY, IONTECHNOLOGY, PLASMATECHNOLOGY, INTERGALACTICRESEARCHNETWORK, ASTROPHYSICS, GRAVITONTECHNOLOGY]
LfTechnologiesArr = [INTERGALACTICENVOYS, HIGHPERFORMANCEEXTRACTORS, FUSIONDRIVES, STEALTHFIELDGENERATOR, ORBITALDEN, RESEARCHAI, HIGHPERFORMANCETERRAFORMER, ENHANCEDPRODUCTIONTECHNOLOGIES, LIGHTFIGHTERMKII, CRUISERMKII, IMPROVEDLABTECHNOLOGY, PLASMATERRAFORMER, LOWTEMPERATUREDRIVES, BOMBERMKII, DESTROYERMKII, BATTLECRUISERMKII, ROBOTASSISTANTS, SUPERCOMPUTER, VOLCANICBATTERIES, ACOUSTICSCANNING, HIGHENERGYPUMPSYSTEMS, CARGOHOLDEXPANSIONCIVILIANSHIPS, MAGMAPOWEREDPRODUCTION, GEOTHERMALPOWERPLANTS, DEPTHSOUNDING, IONCRYSTALENHANCEMENTHEAVYFIGHTER, IMPROVEDSTELLARATOR, HARDENEDDIAMONDDRILLHEADS, SEISMICMININGTECHNOLOGY, MAGMAPOWEREDPUMPSYSTEMS, IONCRYSTALMODULES, OPTIMISEDSILOCONSTRUCTIONMETHOD, DIAMONDENERGYTRANSMITTER, OBSIDIANSHIELDREINFORCEMENT, RUNESHIELDS, ROCKTALCOLLECTORENHANCEMENT, CATALYSERTECHNOLOGY, PLASMADRIVE, EFFICIENCYMODULE, DEPOTAI, GENERALOVERHAULLIGHTFIGHTER, AUTOMATEDTRANSPORTLINES, IMPROVEDDRONEAI, EXPERIMENTALRECYCLINGTECHNOLOGY, GENERALOVERHAULCRUISER, SLINGSHOTAUTOPILOT, HIGHTEMPERATURESUPERCONDUCTORS, GENERALOVERHAULBATTLESHIP, ARTIFICIALSWARMINTELLIGENCE, GENERALOVERHAULBATTLECRUISER, GENERALOVERHAULBOMBER, GENERALOVERHAULDESTROYER, EXPERIMENTALWEAPONSTECHNOLOGY, MECHANGENERALENHANCEMENT, HEATRECOVERY, SULPHIDEPROCESS, PSIONICNETWORK, TELEKINETICTRACTORBEAM, ENHANCEDSENSORTECHNOLOGY, NEUROMODALCOMPRESSOR, NEUROINTERFACE, INTERPLANETARYANALYSISNETWORK, OVERCLOCKINGHEAVYFIGHTER, TELEKINETICDRIVE, SIXTHSENSE, PSYCHOHARMONISER, EFFICIENTSWARMINTELLIGENCE, OVERCLOCKINGLARGECARGO, GRAVITATIONSENSORS, OVERCLOCKINGBATTLESHIP, PSIONICSHIELDMATRIX, KAELESHDISCOVERERENHANCEMENT]
LfTechnologiesHumansArr = [INTERGALACTICENVOYS, HIGHPERFORMANCEEXTRACTORS, FUSIONDRIVES, STEALTHFIELDGENERATOR, ORBITALDEN, RESEARCHAI, HIGHPERFORMANCETERRAFORMER, ENHANCEDPRODUCTIONTECHNOLOGIES, LIGHTFIGHTERMKII, CRUISERMKII, IMPROVEDLABTECHNOLOGY, PLASMATERRAFORMER, LOWTEMPERATUREDRIVES, BOMBERMKII, DESTROYERMKII, BATTLECRUISERMKII, ROBOTASSISTANTS, SUPERCOMPUTER]
LfTechnologiesRocktalArr = [VOLCANICBATTERIES, ACOUSTICSCANNING, HIGHENERGYPUMPSYSTEMS, CARGOHOLDEXPANSIONCIVILIANSHIPS, MAGMAPOWEREDPRODUCTION, GEOTHERMALPOWERPLANTS, DEPTHSOUNDING, IONCRYSTALENHANCEMENTHEAVYFIGHTER, IMPROVEDSTELLARATOR, HARDENEDDIAMONDDRILLHEADS, SEISMICMININGTECHNOLOGY, MAGMAPOWEREDPUMPSYSTEMS, IONCRYSTALMODULES, OPTIMISEDSILOCONSTRUCTIONMETHOD, DIAMONDENERGYTRANSMITTER, OBSIDIANSHIELDREINFORCEMENT, RUNESHIELDS, ROCKTALCOLLECTORENHANCEMENT]
LfTechnologiesMechasArr = [CATALYSERTECHNOLOGY, PLASMADRIVE, EFFICIENCYMODULE, DEPOTAI, GENERALOVERHAULLIGHTFIGHTER, AUTOMATEDTRANSPORTLINES, IMPROVEDDRONEAI, EXPERIMENTALRECYCLINGTECHNOLOGY, GENERALOVERHAULCRUISER, SLINGSHOTAUTOPILOT, HIGHTEMPERATURESUPERCONDUCTORS, GENERALOVERHAULBATTLESHIP, ARTIFICIALSWARMINTELLIGENCE, GENERALOVERHAULBATTLECRUISER, GENERALOVERHAULBOMBER, GENERALOVERHAULDESTROYER, EXPERIMENTALWEAPONSTECHNOLOGY, MECHANGENERALENHANCEMENT]
LfTechnologiesKaeleshArr = [HEATRECOVERY, SULPHIDEPROCESS, PSIONICNETWORK, TELEKINETICTRACTORBEAM, ENHANCEDSENSORTECHNOLOGY, NEUROMODALCOMPRESSOR, NEUROINTERFACE, INTERPLANETARYANALYSISNETWORK, OVERCLOCKINGHEAVYFIGHTER, TELEKINETICDRIVE, SIXTHSENSE, PSYCHOHARMONISER, EFFICIENTSWARMINTELLIGENCE, OVERCLOCKINGLARGECARGO, GRAVITATIONSENSORS, OVERCLOCKINGBATTLESHIP, PSIONICSHIELDMATRIX, KAELESHDISCOVERERENHANCEMENT]
LfBuildingsHumansArr = [RESIDENTIALSECTOR, BIOSPHEREFARM, RESEARCHCENTRE, ACADEMYOFSCIENCES, NEUROCALIBRATIONCENTRE, HIGHENERGYSMELTING, FOODSILO, FUSIONPOWEREDPRODUCTION, SKYSCRAPER, BIOTECHLAB, METROPOLIS, PLANETARYSHIELD]
LfBuildingsRocktalArr = [MEDITATIONENCLAVE, CRYSTALFARM,RUNETECHNOLOGIUM, RUNEFORGE, ORIKTORIUM, MAGMAFORGE, DISRUPTIONCHAMBER, MEGALITH, CRYSTALREFINERY, DEUTERIUMSYNTHESISER, MINERALRESEARCHCENTRE, ADVANCEDRECYCLINGPLANT]
LfBuildingsMechasArr = [ASSEMBLYLINE, FUSIONCELLFACTORY, ROBOTICSRESEARCHCENTRE,UPDATENETWORK, QUANTUMCOMPUTERCENTRE, AUTOMATISEDASSEMBLYCENTRE, HIGHPERFORMANCETRANSFORMER, MICROCHIPASSEMBLYLINE,PRODUCTIONASSEMBLYHALL, HIGHPERFORMANCESYNTHESISER, CHIPMASSPRODUCTION, NANOREPAIRBOTS]
LfBuildingsKaeleshArr = [SANCTUARY, ANTIMATTERCONDENSER, VORTEXCHAMBER, HALLSOFREALISATION,FORUMOFTRANSCENDENCE, ANTIMATTERCONVECTOR, CLONINGLABORATORY, CHRYSALISACCELERATOR, BIOMODIFIER,PSIONICMODULATOR, SHIPMANUFACTURINGHALL, SUPRAREFRACTOR]
BuildingsArr = [METALMINE, CRYSTALMINE, DEUTERIUMSYNTHESIZER, SOLARPLANT, FUSIONREACTOR, SOLARSATELLITE, METALSTORAGE, CRYSTALSTORAGE, DEUTERIUMTANK, SHIELDEDMETALDEN, UNDERGROUNDCRYSTALDEN, SEABEDDEUTERIUMDEN, ALLIANCEDEPOT, ROBOTICSFACTORY, SHIPYARD, RESEARCHLAB, MISSILESILO, NANITEFACTORY, TERRAFORMER, SPACEDOCK, LUNARBASE, SENSORPHALANX, JUMPGATE]
PlanetBuildingsArr = [METALMINE, CRYSTALMINE, DEUTERIUMSYNTHESIZER, SOLARPLANT, FUSIONREACTOR, SOLARSATELLITE, METALSTORAGE, CRYSTALSTORAGE, DEUTERIUMTANK, SHIELDEDMETALDEN, UNDERGROUNDCRYSTALDEN, SEABEDDEUTERIUMDEN, ALLIANCEDEPOT, ROBOTICSFACTORY, SHIPYARD, RESEARCHLAB, MISSILESILO, NANITEFACTORY, TERRAFORMER, SPACEDOCK]
MoonBuildingsArr = [SOLARSATELLITE, METALSTORAGE, CRYSTALSTORAGE, DEUTERIUMTANK, ROBOTICSFACTORY, SHIPYARD, LUNARBASE, SENSORPHALANX, JUMPGATE]
```
```go
// Missions
ATTACK             = 1
GROUPEDATTACK      = 2
TRANSPORT          = 3
PARK               = 4
PARKINTHATALLY     = 5
SPY                = 6
COLONIZE           = 7
RECYCLEDEBRISFIELD = 8
DESTROY            = 9
MISSILEATTACK      = 10
EXPEDITION         = 15
SEARCHFORLIFEFORMS = 18

// Speed
TEN_PERCENT          = 1
TWENTY_PERCENT       = 2
THIRTY_PERCENT       = 3
FORTY_PERCENT        = 4
FIFTY_PERCENT        = 5
SIXTY_PERCENT        = 6
SEVENTY_PERCENT      = 7
EIGHTY_PERCENT       = 8
NINETY_PERCENT       = 9
HUNDRED_PERCENT      = 10
FIVE_PERCENT         = 0.5 // Detailed speeds for General class only
FIFTEEN_PERCENT      = 1.5
TWENTY_FIVE_PERCENT  = 2.5
THIRTY_FIVE_PERCENT  = 3.5
FORTY_FIVE_PERCENT   = 4.5
FIFTY_FIVE_PERCENT   = 5.5
SIXTY_FIVE_PERCENT   = 6.5
SEVENTY_FIVE_PERCENT = 7.5
EIGHTY_FIVE_PERCENT  = 8.5
NINETY_FIVE_PERCENT  = 9.5

// Celestial types
PLANET_TYPE = 1
DEBRIS_TYPE = 2
MOON_TYPE   = 3
```
```go
TELEGRAM_CHAT_ID = (alias to telegram chat id bot setting)
```
```go
DISCORD_WEBHOOK = (alias to discord webhook from bot setting)
```
```go
OGAME_SERVER = (ogame server information)
```
```go
__FILE__ // Name of current script file
```
```go
__IS_CLOUD__ // Either or not the software is running in cloud
__IS_SELF_HOST__ // Either or not the software is running in self-host
```
```go
__LICENSE_UUID__         // UUID of current license
__LICENSE_USERNAME__     // Username of current license
__LICENSE_EMAIL__        // Email of current license
__LICENSE_BOTS_ALLOWED__ // Number of bots allowed by current license
```
```go
__VERSION__ // Ninja version of the software currently running
```
```go
CookieDomain // Domain of the cookie. Cloud instances should have a value of "subdomain.ogame.ninja"
```
```go
GALAXIES // Number of galaxies in the universe
SYSTEMS  // Number of systems in a galaxy
```

## Packages

```go
encoding/json
fmt
io/ioutil
math
math/rand
net/http // Self-host only
net/url
regexp
sort
strconv
strings
sync
time
```

### Http requests (self-host only)

```go
http = import("net/http")
ioutil = import("io/ioutil")

res, _ = http.Get("https://www.google.com")
by, _ = io.ReadAll(res.Body)
defer res.Body.Close()
Print(Bytes2Str(by))

res, _ = http.PostForm("https://httpbin.org/post", {"abc": ["def"]})
by, _ = io.ReadAll(res.Body)
defer res.Body.Close()
Print(Bytes2Str(by))
```

## Internal Types

### IVMBot
```go
// IVMBot interface for safe usage of Bots features in scripts.
type IVMBot interface {
	Start() error
	Stop()
	GetID() int64
	GetUniverse() string
	GetLang() string
	GetPlayerName() string
	GetPlayerID() int64
	GetServerID() int64 // nja internal database id
	GetServerNumber() int64 // s152-en --> 152
	StartScript(name string) error
	StopScript(name string) error
	PauseScript(name string) error
	ResumeScript(name string) error
	IsPausedScript(name string) bool
	IsScriptRunning(name string) bool
	DeleteScript(name string) error
	SetScriptRunAtStart(name string, runAtStart bool) error
	Publish(msg interface{}) (ok bool)
}
```

### ID
```go
// ID represent an ogame id
type ID int64

// Methods
IsSet() bool
Int64() int64
String() string
IsFacility() bool
IsResourceBuilding() bool
IsBuilding() bool
IsTech() bool
IsDefense() bool
IsShip() bool
```

### CharacterClass
```go
type CharacterClass int64
```

```go
NO_CLASS   // 0
COLLECTOR  // 1
GENERAL    // 2
DISCOVERER // 3
```

### AllianceClass
```go
type AllianceClass int64
```

```go
NO_ALLIANCE_CLASS // 0
WARRIOR           // 1
TRADER            // 2
RESEARCHER        // 3
```

### Celestial
```go
// Celestial ...
type Celestial interface {
    GetID() CelestialID
    GetType() CelestialType
    GetName() string
    GetDiameter() int64
    GetCoordinate() Coordinate
    GetFields() Fields
    GetResources() (Resources, error)
    GetResourcesDetails() (ResourcesDetails, error)
    GetFacilities() (Facilities, error)
    SendFleet(ShipsInfos, Speed, Coordinate, MissionID, Resources, int64) (Fleet, error)
    EnsureFleet([]Quantifiable, Speed, Coordinate, MissionID, Resources, int64) (Fleet, error)
    GetDefense() (DefensesInfos, error)
    GetShips() (ShipsInfos, error)
    BuildDefense(defenseID ID, nbr int64) error
    ConstructionsBeingBuilt() (ID, int64, ID, int64)
    GetProduction() ([]Quantifiable, int64, error)
    GetResourcesBuildings() (ResourcesBuildings, error)
    Build(id ID, nbr int64) error
    BuildBuilding(buildingID ID) error
    BuildTechnology(technologyID ID) error
    CancelResearch() error
    CancelBuilding() error
    CancelLfBuilding() error
}
```

### Planet

```go
// Planet ogame planet object
type Planet struct {
	Img         string
	ID          PlanetID
	Name        string
	Diameter    int64
	Coordinate  Coordinate
	Fields      Fields
	Temperature Temperature
	Moon        *Moon
}

// Methods
// all methods of Celestial interface
BuildCancelable(id ID) error
GetResourceSettings() (ResourceSettings, error)
BuildShips(shipID ID, nbr int64) error
GetResourcesProductions() (Resources, error)
FlightTime(destination Coordinate, speed Speed, ships ShipsInfos, mission MissionID) (secs, fuel int64)
SendIPM(planetID PlanetID, coord Coordinate, nbr int64, priority ID) (int64, error)
```

### Moon

```go
// Moon ogame moon object
type Moon struct {
	ID         MoonID
	Img        string
	Name       string
	Diameter   int64
	Coordinate Coordinate
	Fields     Fields
}

// Methods
// all methods of Celestial interface
Phalanx(coord Coordinate) ([]Fleet, error)
```

### CelestialType
Type of celestial object (planet/moon/debris)
```go
type CelestialType int64
```
```go
PLANET_TYPE // 1
DEBRIS_TYPE // 2
MOON_TYPE   // 3
```

### EspionageReportType
```go
type EspionageReportType int64
```
```go
// Action message received when an enemy is seen near your planet
const Action EspionageReportType = 0

// Report message received when you spied on someone
const Report EspionageReportType = 1
```

### Coordinate
```go
type Coordinate struct {
	Galaxy   int64
	System   int64
	Position int64
	Type     CelestialType
}

// Methods
Equal(v Coordinate) bool
String() string
IsPlanet() bool
IsMoon() bool
IsDebris() bool
Planet() Coordinate
Moon() Coordinate
Debris() Coordinate
```
```go
// Compare two coordinates
coordinate1 = NewCoordinate(1, 2, 3, PLANET_TYPE)
coordinate2 = NewCoordinate(1, 2, 3, PLANET_TYPE)
if coordinate1.Equal(coordinate2) {
    Print("The coordinates are equal")
}

// Use the properties
Print("coordinate1 is in galaxy " + coordinate1.Galaxy)
```

### Temperature
```go
// Temperature planet temperature values
type Temperature struct {
	Min int64
	Max int64
}

// Methods
Mean() int64
```

### Fields
```go
// Fields planet fields stats
type Fields struct {
	Built int64
	Total int64
}
```

### Resources
```go
type Resources struct {
	Metal      int64
	Crystal    int64
	Deuterium  int64
	Energy     int64
	Darkmatter int64
}

// Methods
String() string
Total() int64
Value() int64
Sub(v Resources) Resources
Add(v Resources) Resources
Mul(scalar int64) Resources
Div(price Resources) int64
CanAfford(cost Resources) bool
Gte(val Resources) bool
Lte(val Resources) bool
```
```go
metal = 1
crystal = 2
deuterium = 3
res1 = NewResources(metal, crystal, deuterium)
res2 = NewResources(1, 1, 1)
res3 = NewResources(3, 2, 1)
Print("Total: ", res1.Total())                  // Total: 6
Print("Value: ", res1.Value())                  // Value: 14
Print("Can afford: ", res1.CanAfford(res2))     // Can afford: true
Print("Can afford: ", res1.CanAfford(res3))     // Can afford: false
Print("Mul: ", res1.Mul(3))                     // Mul: [3|6|9]
Print("Div: ", NewResources(9, 9, 9).Div(res1)) // Div: 3
```

### ResourcesDetails
```go
type ResourcesDetails struct {
	Metal struct {
		Available         int64
		StorageCapacity   int64
		CurrentProduction int64
	}
	Crystal struct {
		Available         int64
		StorageCapacity   int64
		CurrentProduction int64
	}
	Deuterium struct {
		Available         int64
		StorageCapacity   int64
		CurrentProduction int64
	}
	Energy struct {
		Available         int64
		CurrentProduction int64
		Consumption       int64
	}
	Darkmatter struct {
		Available int64
		Purchased int64
		Found     int64
	}
}

// Methods
Available() Resources
```

### UserInfos
```go
// Currently logged in player information
type UserInfos struct {
	PlayerID     int64
	PlayerName   string
	Points       int64
	Rank         int64
	Total        int64
	HonourPoints int64
}
```

### PlayerDataShort

```go
// PlayerDataShort information returned by ogame xml API (/api/players.xml)
type PlayerDataShort struct {
	ID             int64
	Name           string
	IsAdmin        bool
	IsInactive     bool
	IsLongInactive bool
	Vacation       bool
	AllianceID     *int64
}
```

### PlayerData
```go
// PlayerData information returned by ogame xml API
type PlayerData struct {
	ID                        int64
	Name                      string
	Timestamp                 int64
	PointsTotal               int64
	PointsEconomy             int64
	PointsResearch            int64
	PointsMilitary            int64
	PointsMilitaryBuilt       int64
	PointsMilitaryDestroyed   int64
	PointsMilitaryLost        int64
	PointsHonor               int64
	PositionTotal             int64
	PositionEconomy           int64
	PositionResearch          int64
	PositionMilitary          int64
	PositionMilitaryBuilt     int64
	PositionMilitaryDestroyed int64
	PositionMilitaryLost      int64
	PositionHonor             int64
	MilitaryShips             int64
	Celestials                []struct {
		ID         ogame.CelestialID
		Name       string
		Coordinate ogame.Coordinate
	}
	Alliance *struct {
		ID   int64
		Name string
		Tag  string
	}
}
```

### Auction
```go
type Auction struct {
	HasFinished         bool
	Endtime             int64
	NumBids             int64
	CurrentBid          int64
	AlreadyBid          int64
	MinimumBid          int64
	DeficitBid          int64
	HighestBidder       string
	HighestBidderUserID int64
	Ref                 string
	CurrentItem         string
	CurrentItemLong     string
	Inventory           int64
	Token               string
	ResourceMultiplier  struct {
		Metal     float64
		Crystal   float64
		Deuterium float64
		Honor     int64
	}
	Resources map[string]interface{}
}
```

### Fleet

```go
// Fleet represent a player fleet information
type Fleet struct {
	Mission        MissionID
	ReturnFlight   bool
	ID             FleetID
	Resources      Resources
	Origin         Coordinate
	Destination    Coordinate
	Ships          ShipsInfos
	StartTime      time.Time
	ArrivalTime    time.Time
	BackTime       time.Time
	ArriveIn       int64
	BackIn         int64
	UnionID        int64
	TargetPlanetID int64
}
```

### ShipsInfos
```go
// ShipsInfos represent a planet ships information
type ShipsInfos struct {
	LightFighter   int64
	HeavyFighter   int64
	Cruiser        int64
	Battleship     int64
	Battlecruiser  int64
	Bomber         int64
	Destroyer      int64
	Deathstar      int64
	SmallCargo     int64
	LargeCargo     int64
	ColonyShip     int64
	Recycler       int64
	EspionageProbe int64
	SolarSatellite int64
	Crawler        int64
	Reaper         int64
	Pathfinder     int64
}

// Methods

Cargo(techs IResearches, lfBonuses LfBonuses, characterClass CharacterClass, multiplier float64, probeRaids bool) int64 // probeRaids determines if spys in your uni have cargo or not
Speed(techs IResearches, lfBonuses LfBonuses, characterClass CharacterClass) int64
ToQuantifiables() []Quantifiable
FromQuantifiables(in []Quantifiable) (out ShipsInfos)
Has(v ShipsInfos) bool
FleetValue(LfBonuses) int64
FleetCost(LfBonuses) Resources
CountShips() int64
ByID(id ID) int64
String() string
ToPtr() *ShipsInfos
Add(v ShipsInfos)
AddShips(shipID ID, nb int64)
SubShips(shipID ID, nb int64)
Set(id ID, val int64)
Equal(other ShipsInfos) bool
HasShips() bool
HasFlyableShips() bool
```

### DefensesInfos
```go
// DefensesInfos represent a planet defenses information
type DefensesInfos struct {
	RocketLauncher         int64
	LightLaser             int64
	HeavyLaser             int64
	GaussCannon            int64
	IonCannon              int64
	PlasmaTurret           int64
	SmallShieldDome        int64
	LargeShieldDome        int64
	AntiBallisticMissiles  int64
	InterplanetaryMissiles int64
}

// Methods
AttackableValue() int64
String() string
ByID(id ID) int64
Set(id ID, val int64)
```

### AttackEvent
```go
// AttackEvent all information available about an enemy attack
type AttackEvent struct {
	ID              int64
	MissionType     MissionID
	Origin          Coordinate
	Destination     Coordinate
	DestinationName string
	ArrivalTime     time.Time
	ArriveIn        int64
	AttackerName    string
	AttackerID      int64
	Missiles        int64
	Ships           *ShipsInfos
}

// Methods
String() string
```

### SystemInfos
```go
// SystemInfos planets information for a specific system
type SystemInfos struct {
	ExpeditionDebris struct {
		Metal             int64
		Crystal           int64
		PathfindersNeeded int64
	}
	Events struct {
		Darkmatter  int64
		HasAsteroid bool
	}
}

// Methods
Galaxy() int64
System() int64
Position(idx int64) *PlanetInfos
```


### PlanetInfos
```go
// PlanetInfos public information of a planet in the galaxy page
type PlanetInfos struct {
	ID              int64
	Activity        int64 // no activity: 0, active: 15, inactive: [16, 59]
	Name            string
	Img             string
	Coordinate      Coordinate
	Administrator   bool
	Inactive        bool
	Vacation        bool
	StrongPlayer    bool
	Newbie          bool
	HonorableTarget bool
	Banned          bool
	Debris          struct {
		Metal           int64
		Crystal         int64
		Deuterium       int64
		RecyclersNeeded int64
	}
	Moon   *MoonInfos
	Player struct {
		ID         int64
		Name       string
		Rank       int64
		IsBandit   bool
		IsStarlord bool
	}
	Alliance *AllianceInfos
}
```

### AllianceInfos
```go
// AllianceInfos public information of an alliance in the galaxy page
type AllianceInfos struct {
	ID     int64
	Name   string
	Rank   int64
	Member int64
}
```

### MoonInfos
```go
// MoonInfos public information of a moon in the galaxy page
type MoonInfos struct {
	ID       int64
	Diameter int64
	Activity int64
}
```

### Facilities
```go
// Facilities represent a planet facilities information
type Facilities struct {
	RoboticsFactory int64
	Shipyard        int64
	ResearchLab     int64
	AllianceDepot   int64
	MissileSilo     int64
	NaniteFactory   int64
	Terraformer     int64
	SpaceDock       int64
	LunarBase       int64
	SensorPhalanx   int64
	JumpGate        int64
}

// Methods
ByID(id ID) int64
String() string
```

### LifeformType
```go
type LifeformType int64

const (
	NoneLfType LifeformType = iota
	Humans
	Rocktal
	Mechas
	Kaelesh
)
```

### LfBuildings
```go
// LfBuildings lifeform buildings
type LfBuildings struct {
	LifeformType               LifeformType
	ResidentialSector          int64 // 11101 // Lifeform (humans)
	BiosphereFarm              int64 // 11102
	ResearchCentre             int64 // 11103
	AcademyOfSciences          int64 // 11104
	NeuroCalibrationCentre     int64 // 11105
	HighEnergySmelting         int64 // 11106
	FoodSilo                   int64 // 11107
	FusionPoweredProduction    int64 // 11108
	Skyscraper                 int64 // 11109
	BiotechLab                 int64 // 11110
	Metropolis                 int64 // 11111
	PlanetaryShield            int64 // 11112
	MeditationEnclave          int64 // 12101 // Lifeform (rocktal)
	CrystalFarm                int64 // 12102
	RuneTechnologium           int64 // 12103
	RuneForge                  int64 // 12104
	Oriktorium                 int64 // 12105
	MagmaForge                 int64 // 12106
	DisruptionChamber          int64 // 12107
	Megalith                   int64 // 12108
	CrystalRefinery            int64 // 12109
	DeuteriumSynthesiser       int64 // 12110
	MineralResearchCentre      int64 // 12111
	AdvancedRecyclingPlant     int64 // 12112
	AssemblyLine               int64 // 13101 // Lifeform (mechas)
	FusionCellFactory          int64 // 13102
	RoboticsResearchCentre     int64 // 13103
	UpdateNetwork              int64 // 12304
	QuantumComputerCentre      int64 // 13105
	AutomatisedAssemblyCentre  int64 // 13106
	HighPerformanceTransformer int64 // 13107
	MicrochipAssemblyLine      int64 // 13108
	ProductionAssemblyHall     int64 // 13109
	HighPerformanceSynthesiser int64 // 13110
	ChipMassProduction         int64 // 13111
	NanoRepairBots             int64 // 13112
	Sanctuary                  int64 // 14101 // Lifeform (kaelesh)
	AntimatterCondenser        int64 // 14102
	VortexChamber              int64 // 14103
	HallsOfRealisation         int64 // 14104
	ForumOfTranscendence       int64 // 14105
	AntimatterConvector        int64 // 14106
	CloningLaboratory          int64 // 14107
	ChrysalisAccelerator       int64 // 14108
	BioModifier                int64 // 14109
	PsionicModulator           int64 // 14110
	ShipManufacturingHall      int64 // 14111
	SupraRefractor             int64 // 14112
}

// Methods
ByID(id ID) int64
String() string
```

### Researches
```go
// Researches represent player's researches
type Researches struct {
	EnergyTechnology             int64
	LaserTechnology              int64
	IonTechnology                int64
	HyperspaceTechnology         int64
	PlasmaTechnology             int64
	CombustionDrive              int64
	ImpulseDrive                 int64
	HyperspaceDrive              int64
	EspionageTechnology          int64
	ComputerTechnology           int64
	Astrophysics                 int64
	IntergalacticResearchNetwork int64
	GravitonTechnology           int64
	WeaponsTechnology            int64
	ShieldingTechnology          int64
	ArmourTechnology             int64
}

// Methods
ToPtr() *Researches
ByID(id ID) int64
String() string
```

### LfResearches

```go
type LfResearches struct {
	IntergalacticEnvoys               *int64 // 11201 // Humans techs
	HighPerformanceExtractors         *int64 // 11202
	FusionDrives                      *int64 // 11203
	StealthFieldGenerator             *int64 // 11204
	OrbitalDen                        *int64 // 11205
	ResearchAI                        *int64 // 11206
	HighPerformanceTerraformer        *int64 // 11207
	EnhancedProductionTechnologies    *int64 // 11208
	LightFighterMkII                  *int64 // 11209
	CruiserMkII                       *int64 // 11210
	ImprovedLabTechnology             *int64 // 11211
	PlasmaTerraformer                 *int64 // 11212
	LowTemperatureDrives              *int64 // 11213
	BomberMkII                        *int64 // 11214
	DestroyerMkII                     *int64 // 11215
	BattlecruiserMkII                 *int64 // 11216
	RobotAssistants                   *int64 // 11217
	Supercomputer                     *int64 // 11218
	VolcanicBatteries                 *int64 // 12201 // Rocktal techs
	AcousticScanning                  *int64 // 12202
	HighEnergyPumpSystems             *int64 // 12203
	CargoHoldExpansionCivilianShips   *int64 // 12204
	MagmaPoweredProduction            *int64 // 12205
	GeothermalPowerPlants             *int64 // 12206
	DepthSounding                     *int64 // 12207
	IonCrystalEnhancementHeavyFighter *int64 // 12208
	ImprovedStellarator               *int64 // 12209
	HardenedDiamondDrillHeads         *int64 // 12210
	SeismicMiningTechnology           *int64 // 12211
	MagmaPoweredPumpSystems           *int64 // 12212
	IonCrystalModules                 *int64 // 12213
	OptimisedSiloConstructionMethod   *int64 // 12214
	DiamondEnergyTransmitter          *int64 // 12215
	ObsidianShieldReinforcement       *int64 // 12216
	RuneShields                       *int64 // 12217
	RocktalCollectorEnhancement       *int64 // 12218
	CatalyserTechnology               *int64 // 13201 // Mechas techs
	PlasmaDrive                       *int64 // 13202
	EfficiencyModule                  *int64 // 13203
	DepotAI                           *int64 // 13204
	GeneralOverhaulLightFighter       *int64 // 13205
	AutomatedTransportLines           *int64 // 13206
	ImprovedDroneAI                   *int64 // 13207
	ExperimentalRecyclingTechnology   *int64 // 13208
	GeneralOverhaulCruiser            *int64 // 13209
	SlingshotAutopilot                *int64 // 13210
	HighTemperatureSuperconductors    *int64 // 13211
	GeneralOverhaulBattleship         *int64 // 13212
	ArtificialSwarmIntelligence       *int64 // 13213
	GeneralOverhaulBattlecruiser      *int64 // 13214
	GeneralOverhaulBomber             *int64 // 13215
	GeneralOverhaulDestroyer          *int64 // 13216
	ExperimentalWeaponsTechnology     *int64 // 13217
	MechanGeneralEnhancement          *int64 // 13218
	HeatRecovery                      *int64 // 14201 // Kaelesh techs
	SulphideProcess                   *int64 // 14202
	PsionicNetwork                    *int64 // 14203
	TelekineticTractorBeam            *int64 // 14204
	EnhancedSensorTechnology          *int64 // 14205
	NeuromodalCompressor              *int64 // 14206
	NeuroInterface                    *int64 // 14207
	InterplanetaryAnalysisNetwork     *int64 // 14208
	OverclockingHeavyFighter          *int64 // 14209
	TelekineticDrive                  *int64 // 14210
	SixthSense                        *int64 // 14211
	Psychoharmoniser                  *int64 // 14212
	EfficientSwarmIntelligence        *int64 // 14213
	OverclockingLargeCargo            *int64 // 14214
	GravitationSensors                *int64 // 14215
	OverclockingBattleship            *int64 // 14216
	PsionicShieldMatrix               *int64 // 14217
	KaeleshDiscovererEnhancement      *int64 // 14218
}
```

### LfBonuses

```go
type LfBonuses struct {
	LfShipBonuses   LfShipBonuses
	CostTimeBonuses CostTimeBonuses
}

type CostTimeBonuses map[ID]CostTimeBonus

type LfShipBonuses map[ID]LfShipBonus

type CostTimeBonus struct {
	Cost     float64
	Duration float64
}

type LfShipBonus struct {
	ID                  ID
	StructuralIntegrity float64
	ShieldPower         float64
	WeaponPower         float64
	Speed               float64
	CargoCapacity       float64
	FuelConsumption     float64
}
```

### TechnologyDetails
```go
type TechnologyDetails struct {
	TechnologyID       ID
	ProductionDuration time.Duration
	Price              Resources
	Level              int64
	TearDownEnabled    bool
}
```

### ResourcesBuildings
```go
// ResourcesBuildings represent a planet resource buildings
type ResourcesBuildings struct {
	MetalMine            int64
	CrystalMine          int64
	DeuteriumSynthesizer int64
	SolarPlant           int64
	FusionReactor        int64
	SolarSatellite       int64
	MetalStorage         int64
	CrystalStorage       int64
	DeuteriumTank        int64
}

// Methods
ByID(id ID) int64
String() string
```

### EspionageReportSummary
```go
// EspionageReportSummary summary of espionage report
type EspionageReportSummary struct {
	ID             int64
	Type           EspionageReportType
	From           string
	Target         Coordinate
	LootPercentage float64
}
```

### CombatReportSummary
```go
// CombatReportSummary summary of combat report
type CombatReportSummary struct {
	ID           int64
	Origin       *Coordinate
	Destination  Coordinate
	AttackerName string
	DefenderName string
	Loot         int64
	Metal        int64
	Crystal      int64
	Deuterium    int64
	DebrisField  int64
	CreatedAt    time.Time
}
```

### EspionageReport
```go
// EspionageReport detailed espionage report
type EspionageReport struct {
	Resources
	ID                           int64
	Username                     string
	CharacterClass               CharacterClass
	AllianceClass                AllianceClass
	LastActivity                 int64
	CounterEspionage             int64
	APIKey                       string
	HasFleetInformation          bool
	HasDefensesInformation       bool
	HasBuildingsInformation      bool
	HasResearchesInformation     bool
	HonorableTarget              bool
	IsBandit                     bool
	IsStarlord                   bool
	IsInactive                   bool
	IsLongInactive               bool
	MetalMine                    *int // ResourcesBuildings
	CrystalMine                  *int
	DeuteriumSynthesizer         *int
	SolarPlant                   *int
	FusionReactor                *int
	SolarSatellite               *int
	MetalStorage                 *int
	CrystalStorage               *int
	DeuteriumTank                *int
	RoboticsFactory              *int // Facilities
	Shipyard                     *int
	ResearchLab                  *int
	AllianceDepot                *int
	MissileSilo                  *int
	NaniteFactory                *int
	Terraformer                  *int
	SpaceDock                    *int
	LunarBase                    *int
	SensorPhalanx                *int
	JumpGate                     *int
	EnergyTechnology             *int // Researches
	LaserTechnology              *int
	IonTechnology                *int
	HyperspaceTechnology         *int
	PlasmaTechnology             *int
	CombustionDrive              *int
	ImpulseDrive                 *int
	HyperspaceDrive              *int
	EspionageTechnology          *int
	ComputerTechnology           *int
	Astrophysics                 *int
	IntergalacticResearchNetwork *int
	GravitonTechnology           *int
	WeaponsTechnology            *int
	ShieldingTechnology          *int
	ArmourTechnology             *int
	RocketLauncher               *int // Defenses
	LightLaser                   *int
	HeavyLaser                   *int
	GaussCannon                  *int
	IonCannon                    *int
	PlasmaTurret                 *int
	SmallShieldDome              *int
	LargeShieldDome              *int
	AntiBallisticMissiles        *int
	InterplanetaryMissiles       *int
	LightFighter                 *int // Fleets
	HeavyFighter                 *int
	Cruiser                      *int
	Battleship                   *int
	Battlecruiser                *int
	Bomber                       *int
	Destroyer                    *int
	Deathstar                    *int
	SmallCargo                   *int
	LargeCargo                   *int
	ColonyShip                   *int
	Recycler                     *int
	EspionageProbe               *int
	Crawler                      *int
	Reaper                       *int
	Pathfinder                   *int
	Coordinate                   Coordinate
	Type                         EspionageReportType
	Date                         time.Time
}

// Methods
ResourcesBuildings() *ResourcesBuildings
Facilities() *Facilities
Researches() *Researches
ShipsInfos() *ShipsInfos
DefensesInfos() *DefensesInfos
PlunderRatio(CharacterClass) float64
Loot(CharacterClass) Resources
```

### Server
```go
// OGame server information
type Server struct {
	Language      string
	Number        int64
	Name          string
	PlayerCount   int64
	PlayersOnline int64
	Opened        string
	StartDate     string
	EndDate       *string
	ServerClosed  int64
	Prefered      int64
	SignupClosed  int64
	Settings      struct {
		AKS                      int64
		FleetSpeedWar            int64
		FleetSpeedHolding        int64
		FleetSpeedPeaceful       int64
		WreckField               int64
		ServerLabel              string
		EconomySpeed             int64
		PlanetFields             int64
		UniverseSize             int64 // Nb of galaxies
		ServerCategory           string
		EspionageProbeRaids      int64
		PremiumValidationGift    int64
		DebrisFieldFactorShips   int64
		DebrisFieldFactorDefence int64
	}
}
```

### ResourceSettings
```go
// ResourceSettings represent a planet resource settings
type ResourceSettings struct {
	MetalMine            int64
	CrystalMine          int64
	DeuteriumSynthesizer int64
	SolarPlant           int64
	FusionReactor        int64
	SolarSatellite       int64
    Crawler              int64
}

// Methods
String() string
```

### PhalanxReport
```go
type PhalanxReport struct {
	ID        int64
	SessionID int64
	Data      phalanxReportData
	CreatedAt time.Time
}
```

### phalanxReportData
```go
type phalanxReportData []ogame.Fleet
```

### FarmSession
```go
type FarmSession struct {
	ID                       int64
	BotID                    int64
	State                    string
	PlanetID                 CelestialID
	RangeGalaxy              int64
	RangeFrom                int64
	RangeTo                  int64
	NbProbes                 int64
	PctCargo                 int64
	CreatedAt                time.Time
	MinimumResourcesToAttack int64
	MinimumDefensesToIgnore  int64
	MinimumMetalStorage      int64
	MinimumCrystalStorage    int64
	MinimumDeuteriumTank     int64
	MinimumPlayerRank        int64
	TotalTargets             int64
	EspionageProbeRaids      bool
	Phase                    int64
	Progress                 int64
	ProgressAttacks          int64
	FarmType                 int64
	FuelConsumption          int64
	MetalRatio               float64
	CrystalRatio             float64
	DeuteriumRatio           float64
	FarmSpeed                Speed
	FastAttacking            bool
	UsePathfinders           bool
	SkipAfter                int64
	AttackFromNearestPlanet  bool
	AttackFromNearestMoon    bool
	DeleteCombatReports      bool
	Planet                   BotPlanet
}

// Methods
TotalSystems() int64
Pause()
Resume()
```

### FarmAttack
```go
type FarmAttack struct {
	Session     *FarmSession
	CelestialID ogame.CelestialID
	Ships       ogame.ShipsInfos
	Where       ogame.Coordinate
	Speed       ogame.Speed
}
```

### FleetBuilder
```go
type FleetBuilder struct {
}

// Methods
SetOrigin(v interface{}) *FleetBuilder
SetDestination(v interface{}) *FleetBuilder
SetSpeed(speed Speed) *FleetBuilder
SetResources(resources Resources) *FleetBuilder
SetMetal(metal int64) *FleetBuilder
SetCrystal(crystal int64) *FleetBuilder
SetDeuterium(deuterium int64) *FleetBuilder
SetAllResources() *FleetBuilder
SetAllMetal() *FleetBuilder
SetAllCrystal() *FleetBuilder
SetAllDeuterium() *FleetBuilder
SetMinimumDeuterium(minimumDeuterium int64) *FleetBuilder
SetMission(mission MissionID) *FleetBuilder
SetDuration(expeditiontime int64) *FleetBuilder
SetUnionID(unionID int64) *FleetBuilder
AddShips(id ID, nbr int64) *FleetBuilder
SetShips(ships ShipsInfos) *FleetBuilder
SetAllShips() *FleetBuilder
SetRecallIn(secs int64) *FleetBuilder
FlightTime() (secs, fuel int64)
SendNow() (Fleet, error)
OnError(clb func(error)) *FleetBuilder
OnSuccess(clb func(Fleet)) *FleetBuilder
```

### FarmSessionBuilder
```go
type FarmSessionBuilder struct {
}

// Methods
SetOrigin(v interface{}) *FarmSessionBuilder
SetRange(galaxy, systemFrom, systemTo int64) *FarmSessionBuilder
SetProbes(nbProbes int64) *FarmSessionBuilder
SetAdditionalCargo(pctCargo int64) *FarmSessionBuilder
SetMinimumResourcesToAttack(minimumResourcesToAttack int64) *FarmSessionBuilder
SetMinimumDefensesToIgnore(minimumDefensesToIgnore int64) *FarmSessionBuilder
SetMinimumStorageToIgnore(metal, crystal, deuterium int64) *FarmSessionBuilder
SetMinimumPlayerRank(rank int64) *FarmSessionBuilder
SetEspionageProbeRaids(enabled bool) *FarmSessionBuilder
SetUsePathfinders(enabled bool) *FarmSessionBuilder
SetIgnoreActivity(enabled bool) *FarmSessionBuilder
SetFastAttacking(enabled bool) *FarmSessionBuilder
SetDeleteCombatReports(enabled bool) *FarmSessionBuilder
SetAttackFromNearestPlanet(enabled bool) *FarmSessionBuilder
SetAttackFromNearestMoon(enabled bool) *FarmSessionBuilder
SetFarmSpeed(Speed) *FarmSessionBuilder
SetPriorityRatio(metal, crystal, deuterium float64) *FarmSessionBuilder
BuildFarmSession() (*FarmSession, error)
```

### ACSFlightTime
```go
type ACSFlightTime struct {
	ArriveIn      int64
	HumanArriveIn string
	ArriveAt      time.Time
	HumanArriveAt string
	Fuel          int64
	Groups        []ACSFlightTimeGroup
}
```

### ACS
```go
type ACS struct {
}

// Methods
GetFlightTimes() ACSFlightTime
AddGroup(delaySecs int64, speed Speed, ships ShipsInfos) *ACS
Execute()
ExecuteAsync()
GetUnionID() int64
GetFleetIDs() []FleetID
```

### ChatMsg
```go
type ChatMsg struct {
	SenderID      int64
	SenderName    string
	AssociationID int64
	Text          string
	ID            int64
	Date          int64
}
```


### TelegramMessage
```go
type TelegramMessage struct {
	MessageID int64
	From      struct {
		ID           int64
		IsBot        bool
		FirstName    string
		LastName     string
		Username     string
		LanguageCode string
	}
	Chat struct {
		ID        int64
		FirstName string
		LastName  string
		Username  string
		Type      string
	}
	ReplyToMessage struct {
		MessageID int
		From      struct {
			ID           int64
			IsBot        bool
			FirstName    string
			LastName     string
			LanguageCode string
		}
		Chat struct {
			ID        int64
			FirstName string
			LastName  string
			Type      string
		}
		Date int
		Text string
	}
	Date int64
	Text string
}
```

### TelegramCallbackQuery
```go
type TelegramCallbackQuery struct {
	ID   string `json:"id"`
	From struct {
		ID           int64  `json:"id"`
		IsBot        bool   `json:"is_bot"`
		FirstName    string `json:"first_name"`
		LastName     string `json:"last_name"`
		LanguageCode string `json:"language_code"`
	} `json:"from"`
	Message struct {
		MessageID int `json:"message_id"`
		From      struct {
			ID        int64  `json:"id"`
			IsBot     bool   `json:"is_bot"`
			FirstName string `json:"first_name"`
			Username  string `json:"username"`
		} `json:"from"`
		Chat struct {
			ID        int64  `json:"id"`
			FirstName string `json:"first_name"`
			LastName  string `json:"last_name"`
			Type      string `json:"type"`
		} `json:"chat"`
		Date        int    `json:"date"`
		Text        string `json:"text"`
		ReplyMarkup struct {
			InlineKeyboard [][]struct {
				Text         string `json:"text"`
				CallbackData string `json:"callback_data"`
			} `json:"inline_keyboard"`
		} `json:"reply_markup"`
	} `json:"message"`
	ChatInstance string `json:"chat_instance"`
	Data         string `json:"data"`
}
```

### TelegramUpdate
```go
type TelegramUpdate struct {
	UpdateID      int64                 `json:"update_id"`
	Message       TelegramMessage       `json:"message"`
	CallbackQuery TelegramCallbackQuery `json:"callback_query"`
}
```

### DMCost
```go
// DMCost information for fast build using DM
type DMCost struct {
	Cost                int64
	CanBuy              bool  // Either or not we have enough DM
	Complete            bool  // false means we will halve the time, true will complete
	OGameID             ID    // What we are going to build
	Nbr                 int64 // Either the amount of ships/defences or the building/research level
	BuyAndActivateToken string
	Token               string
}
```

### DMCosts
```go
// DMCosts all information for fast build using DM
type DMCosts struct {
	Buildings DMCost
	Research  DMCost
	Shipyard  DMCost
}
```

### TempFile
```go
type TempFile struct {
}

// Methods
Write(b []byte) (int, error)
Close() error
```

### Item 
```go
type Item struct {
	Ref          string
	Name         string
	Image        string
	ImageLarge   string
	Title        string
	Rarity       string
	Amount       int64
	AmountFree   int64
	AmountBought int64
}
```

### ActiveItem 
```go
type ActiveItem struct {
	ID            int64
	Ref           string
	Name          string
	TimeRemaining int64
	TotalDuration int64
	ImgSmall      string
}
```

### Quantifiable
```go
// Quantifiable is a tuple with an ID and a number
type Quantifiable struct {
	ID  ID
	Nbr int64
}
```

### Highscore
```go
// Highscore 
type Highscore struct {
	NbPage   int64
	CurrPage int64
	Category int64 // 1:Player, 2:Alliance
	Type     int64 // 0:Total, 1:Economy, 2:Research, 3:Military, 4:Military Built, 5:Military Destroyed, 6:Military Lost, 7:Honor
	Players  []HighscorePlayer
}
```

### HighscorePlayer
```go
// HighscorePlayer
type HighscorePlayer struct {
	Position     int64
	ID           int64
	Name         string
	Score        int64
	AllianceID   int64
	HonourPoints int64
	Homeworld    Coordinate
	Ships        int64 // When getting military type
}
```


### SimulatorResult

```go
// SimulatorResult result returned when running a simulation
type SimulatorResult struct {
	TacticalRetreat string
	Simulations     int
	AttackerWin     int
	DefenderWin     int
	Draw            int
	Rounds          int
	AttackerLosses  Resources
	AttackerProfit  Resources
	DefenderLosses  Resources
	DefenderProfit  Resources
	Debris          Resources
	PossiblePlunder Resources
	CapturedPlunder Resources
	PctCaptured     int
	Recycler        int
	LCNeeded        int
	SCNeeded        int
	Moonchance      int
	Logs            string
	FlightTime      int
	Fuel            int

	AttackerSmallCargoRemaining     int
	AttackerLargeCargoRemaining     int
	AttackerLightFighterRemaining   int
	AttackerHeavyFighterRemaining   int
	AttackerCruiserRemaining        int
	AttackerBattleshipRemaining     int
	AttackerColonyShipRemaining     int
	AttackerRecyclerRemaining       int
	AttackerEspionageProbeRemaining int
	AttackerBomberRemaining         int
	AttackerSolarSatelliteRemaining int
	AttackerDestroyerRemaining      int
	AttackerDeathstarRemaining      int
	AttackerBattlecruiserRemaining  int
	AttackerCrawlerRemaining        int
	AttackerReaperRemaining         int
	AttackerPathfinderRemaining     int

	DefenderSmallCargoRemaining      int
	DefenderLargeCargoRemaining      int
	DefenderLightFighterRemaining    int
	DefenderHeavyFighterRemaining    int
	DefenderCruiserRemaining         int
	DefenderBattleshipRemaining      int
	DefenderColonyShipRemaining      int
	DefenderRecyclerRemaining        int
	DefenderEspionageProbeRemaining  int
	DefenderBomberRemaining          int
	DefenderSolarSatelliteRemaining  int
	DefenderDestroyerRemaining       int
	DefenderDeathstarRemaining       int
	DefenderBattlecruiserRemaining   int
	DefenderCrawlerRemaining         int
	DefenderReaperRemaining          int
	DefenderPathfinderRemaining      int
	DefenderRocketLauncherRemaining  int
	DefenderLightLaserRemaining      int
	DefenderHeavyLaserRemaining      int
	DefenderGaussCannonRemaining     int
	DefenderIonCannonRemaining       int
	DefenderPlasmaTurretRemaining    int
	DefenderSmallShieldDomeRemaining int
	DefenderLargeShieldDomeRemaining int
}
```

### ExpeditionsConfigs

```go
// ExpeditionsConfigs ...
type ExpeditionsConfigs struct {
	Strategy                 int64
	WaveDelayMin             int64
	WaveDelayMax             int64
	DelayMin                 int64
	DelayMax                 int64
	MinimumPathfinders       int64
	PathfindersHome          int64
	LargeCargoHome           int64
	SmallCargoHome           int64
	EarlyPathfinders         int64
	EarlyPFSecsMin           int64
	EarlyPFSecsMax           int64
	MinSpeedEarlyPathfinders ogame.Speed
	RecycleDebris            bool
	Sheets                   []Sheet
}
```

### Sheet

```go

// Sheet ...
type Sheet struct {
	UUID                   string
	Name                   string
	Active                 bool
	IgnoreMinimumDeuterium bool
	SlotsAllowed           int64
	Origin                 ogame.CelestialID
	SystemFrom             int64
	SystemTo               int64
	ExpeditionSpeed        ogame.Speed
	Duration               int64
	SmallCargo             int64
	LargeCargo             int64
	LightFighter           int64
	HeavyFighter           int64
	Cruiser                int64
	Battleship             int64
	ColonyShip             int64
	Recycler               int64
	EspionageProbe         int64
	Bomber                 int64
	Destroyer              int64
	Deathstar              int64
	Battlecruiser          int64
	Reaper                 int64
	Pathfinder             int64
	SmallCargoAuto         bool
	LargeCargoAuto         bool
	LightFighterAuto       bool
	HeavyFighterAuto       bool
	CruiserAuto            bool
	BattleshipAuto         bool
	ColonyShipAuto         bool
	RecyclerAuto           bool
	EspionageProbeAuto     bool
	BomberAuto             bool
	DestroyerAuto          bool
	DeathstarAuto          bool
	BattlecruiserAuto      bool
	ReaperAuto             bool
	PathfinderAuto         bool
	AutoFleet              bool // Let the bot build a fleet
}
```

## Channels (events)

### OnAttackCh
```go
// Triggered when a new attack is detected
OnAttackCh chan AttackEvent
```

### OnAttackDoneCh
```go
// Triggered once an attack is done
OnAttackDoneCh chan AttackEvent
```

### OnAttackCancelledCh
```go
// Triggered when an attack is cancelled
OnAttackCancelledCh chan AttackEvent
```

### OnRepatriateCompletedCh
```go
// Triggered when repatriate process is completed
OnRepatriateCompletedCh chan Resources
```

### OnQuitCh
```go
// Triggered when `Exit` is called
OnQuitCh chan struct{}
```
```go
ExecIn(2000, func() {
    Exit()
})
<-OnQuitCh
Print("Exiting")
```

### OnPhalanxReport
```go
// Triggered when a phalanx report is created
OnPhalanxReport chan PhalanxReport
```
```go
for {
    report = <-OnPhalanxReport
    Print("new phalanx report : " + report)
}
```

### OnFarmSessionStart
```go
// Triggered when a farm session starts
OnFarmSessionStart chan *FarmSession
```
```go
for {
    s = <-OnFarmSessionStart
    Print("Start new session " + s.ID)
}
```

### OnFarmSessionAllSpySent
```go
// Triggered when all targets are spied
OnFarmSessionAllSpySent chan *FarmSession
```
```go
for {
    s = <-OnFarmSessionAllSpySent
    Print("All targets spied for session " + s.ID)
}
```

### OnFarmSessionBeforeAttack
```go
// Triggered before an attack is sent
OnFarmSessionBeforeAttack chan FarmAttack
```
```go
for {
    a = <-OnFarmSessionBeforeAttack
    Print("Sending an attack " + a.Session.ID)
}
```

### OnFarmSessionAttacksSent
```go
// Triggered when all attacks are sent
OnFarmSessionAttacksSent chan *FarmSession
```
```go
for {
    s = <-OnFarmSessionAttacksSent
    Print("All attacks sent for session " + s.ID)
}
```

### OnFarmSessionDone
```go
// Triggered when all attacks are back home
OnFarmSessionDone chan *FarmSession
```
```go
for {
    s = <-OnFarmSessionDone
    Print("All attacks are back home for session " + s.ID)
}
```

### OnGalaxyScanDone
```go
// Triggered when the galaxy scan is completed
OnGalaxyScanDone chan struct{}
```
```go
for {
    <-OnGalaxyScanDone
    Print("Galaxy scan is completed")
}
```

### OnFleetDispatch
```go
// Triggered when a fleet is dispatched
OnFleetDispatch chan Fleet
```

### OnBotCh
```go
// Triggered when a bot publish a message directly to the current bot
OnBotCh chan interface{}
```

### OnTelegramUpdateReceivedCh
```go
// Triggered when an update is received from telegram
OnTelegramUpdateReceivedCh chan TelegramUpdate
```
```go
for {
    m = <-OnTelegramUpdateReceivedCh
    Print(m)
}
```

### OnTelegramMessageReceivedCh
```go
// Triggered when a private message is received from telegram
OnTelegramMessageReceivedCh chan TelegramMessage
```
```go
for {
    m = <-OnTelegramMessageReceivedCh
    Print(m.Text)
}
```

### OnChatMessageReceivedCh
```go
// Triggered when a private message is received in ogame (unstable)
OnChatMessageReceivedCh chan ChatMsg
```
```go
for {
    m = <-OnChatMessageReceivedCh
    Print("From: " + m.SenderName + ", msg: " + m.Text)
}
```

## Global API

### BotID
```go
// BotID contains the current bot ID
BotID int64
```
```go
Print(BotID) // 1
```

### BotsCount
```go
// BotsCount returns the total number of bots in the database
BotsCount() int64
```

### OwnBots
```go
// OwnBots returns a list of the bots that you have created
OwnBots() []IVMBot
```
```go
for bot in OwnBots() {
    Print(bot.GetID(), bot.GetPlayerName())
}
```

### AllBots
```go
// AllBots returns a list of the bots that you can access (own bots + shared bots)
AllBots() []IVMBot
```

### GetBotByID
```go
// GetBotByID return a bot or nil if the bot does not exists
GetBotByID(botID int64) IVMBot
```
```go
bot = GetBotByID(2)
if bot != nil {
  Print(bot.GetPlayerName())
}
```

### StartRemoteScript
```go
StartRemoteScript(addr string, botID int64, apiKey, script string) error
```
```go
Print(StartRemoteScript("https://something.ogame.ninja", 1, "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", "main.ank"))
```

### StopRemoteScript
```go
StopRemoteScript(addr string, botID int64, apiKey, script string) error
```
```go
Print(StopRemoteScript("https://something.ogame.ninja", 1, "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", "main.ank"))
```

### Sleep
```go
// Sleeps a defined number of milliseconds
Sleep(milliseconds int64)
```
```go
Print("Before")
Sleep(5 * 60 * 1000) // Execution of the script will stop here for 5min
Print("After")
```

### SleepMs
```go
// Alias to "Sleep" function
SleepMs(milliseconds int64)
```

### SleepSec
```go
// Sleeps a defined number of seconds
SleepSec(seconds int64)
```
```go
Print("Before")
SleepSec(5) // Execution of the script will stop here for 5sec
Print("After")
```

### SleepMin
```go
// Sleeps a defined number of minutes
SleepMin(minutes int64)
```
```go
Print("Before")
SleepMin(5) // Execution of the script will stop here for 5min
Print("After")
```

### SleepRandMs
```go
// Sleep a random duration within a range given in milliseconds
SleepRandMs(min, max int64)
```
```go
Print("Before")
SleepRandMs(1000, 3000) // Code execution pause here for a random time between 1000ms and 3000ms
Print("After")
```

### SleepRandSec
```go
// Sleep a random duration within a range given in seconds
SleepRandSec(min, max int64)
```
```go
Print("Before")
SleepRandSec(1, 3) // Code execution pause here for a random time between 1s and 3s
Print("After")
```

### SleepRandMin
```go
// Sleep a random duration within a range given in minutes
SleepRandMin(min, max int64)
```
```go
Print("Before")
SleepRandMin(1, 3) // Code execution pause here for a random time between 1min and 3min
Print("After")
```

### SleepRandHour
```go
// Sleep a random duration within a range given in hours
SleepRandHour(min, max int64)
```
```go
Print("Before")
SleepRandHour(1, 3) // Code execution pause here for a random time between 1hour and 3hours
Print("After")
```

### SleepUntil
```go
// Sleep until a specified time
SleepUntil(timeStr string)
```
```go
Print("Before")
SleepDur("01:02:03") // Code execution pause here until 1h2.3
Print("After")
```

### SleepDur
```go
// Sleep for duration
SleepDur(dur time.Duration)
```
```go
time = import("time")
Print("Before")
dur = 3 * time.Seconds
SleepDur(dur) // Code execution pause here for a durayion of 3sec
Print("After")
```

### Random
```go
// Random generates a number between min and max inclusively
Random(min, max int64)
```
```go
randomDelay = Random(1000, 5000)
Sleep(randomDelay)
Print("Done sleeping")
```

### Print
```go
// Print some variables
Print(args ...interface{})
```
```go
Print("Example", 123, true)
```

### print
```go
// Same as Print
print(args ...interface{})
```

### LogDebug
```go
// Same as Print
LogDebug(args ...interface{})
```

### LogInfo
```go
// Creates an Info log
LogInfo(args ...interface{})
```

### LogWarn
```go
// Creates an Warn log
LogWarn(args ...interface{})
```

### LogError
```go
// Creates an Error log
LogError(args ...interface{})
```


### ClearOut
```go
// Clears the output
ClearOut()
```

### RepatriateNow
```go
// Trigger the repatriate feature now and reset the timer
RepatriateNow()
```
```go
RepatriateNow()
```

### RepatriateSetAllDestinations
```go
// Set the repatriate destination for all planets and moons 
RepatriateSetAllDestinations(wrapper.IntoCelestial)
```
```go
RepatriateSetAllDestinations("1:2:3")

planet = GetCachedPlanets()[0]
RepatriateSetAllDestinations(planet)

coord, _ = ParseCoord("1:2:3")
RepatriateSetAllDestinations(coord)
```

### TriggerFleetSave
```go
// Trigger fleet save using "sleep mode" configurations
TriggerFleetSave()
```

### GetNextSleepTime
```go
GetNextSleepTime() *time.Time
```

### GetNextWakeTime
```go
GetNextWakeTime() *time.Time
```

### AddItemToQueue
```go
// Add something into a planet/moon build queue
// The "nbr" parameter is only useful for ships and defenses.
// To "tear down" a building, give a "nbr" of -1.
AddItemToQueue(celestialID CelestialID, id ID, nbr int64) error
```

```go
planet = GetCachedPlanets()[0]
err = AddItemToQueue(planet.GetID(), SOLARPLANT, 0) // Build a "solar plant"
Print(err)
err = AddItemToQueue(planet.GetID(), SOLARPLANT, -1) // Tear down a "solar plant"
Print(err)
err = AddItemToQueue(planet.GetID(), BOMBER, 1)
Print(err)
err = AddItemToQueue(planet.GetID(), ROCKETLAUNCHER, 2)
Print(err)
err = AddItemToQueue(planet.GetID(), BOMBER, 0) // Invalid number to add
Print(err)
```

### ClearAllConstructionQueues
```go
// Clear all construction queues of the bot
ClearAllConstructionQueues() error
```

### GetSlot
```go
// GetSlot wait for a slot from the pool
GetSlot(initiator string) <-chan struct{}
```
```go
origin = "1:2:3"
initiator = "someName"
SetMaxSlotsFor(initiator, 2) // Set the max number of slots for "someName" to 2

<-GetSlot(initiator)
fleet, _ = SendDiscoveryFleet2(origin, "1:2:1")
CreateFleet(fleet.ID, initiator) // Ensure the bot knows that the fleet.ID is created by "someName"

<-GetSlot(initiator)
fleet, _ = SendDiscoveryFleet2(origin, "1:2:2")
CreateFleet(fleet.ID, initiator)

<-GetSlot(initiator) // Will wait here until a slot is available for "someName"
fleet, _ = SendDiscoveryFleet2(origin, "1:2:3")
CreateFleet(fleet.ID, initiator)
```

### CreateFleet
```go
// CreateFleet register a FleetID in the database so that the bot knows that the fleet is created by the initiator
CreateFleet(fleetID ogame.FleetID, initiator string)
```

### GetPrice
```go
// Returns the price of 'entity'. 'nbr' is either a building/research level or a defense/ship quantity.
GetPrice(entityID ID, nbr int64) (Resources)
```
```go
price = GetPrice(LIGHTFIGHTER, 5)
Print("The price of 5 LF is ", price) // [15,000|5,000|0]

price = GetPrice(METALMINE, 3)
Print("The price of metal mine level 3 is ", price) // [135|33|0]
```

### GetPriceLfResearch
```go
// The price for lifeform research depends on some lifeform building on the planet that tries to build the research.
func(celestiaID CelestialID, id ogame.ID, nbr int64) Resources
```

### ConstructionTime
```go
// ConstructionTime returns the duration it takes to build a given level.
// Second argument is either the building level or the number of ships/defences to build
ConstructionTime(ogame.ID, int64, ogame.Facilities) time.Duration
```
```go
celestial = GetCachedCelestials()[0]
facilities, _ = celestial.GetFacilities()
Print(ConstructionTime(METALMINE, 20, facilities))      // 9m4s
Print(ConstructionTime(LARGECARGO, 20, facilities))     // 16m0s
Print(ConstructionTime(ROCKETLAUNCHER, 20, facilities)) // 2m40s
```

### GetRequirements
```go
// Get direct requirements of a given ID
GetRequirements(id ogame.ID) map[ogame.ID]int64
```
```go
Print(GetRequirements(LIGHTLASERnot )) // map[Shipyard:2 LaserTechnology:3]
```

### IsAvailable
```go
// IsAvailable return either or not an ID is available given the buildings & researches
IsAvailable(id ogame.ID, celestialID ogame.CelestialID, supplies ogame.ResourcesBuildings, facilities ogame.Facilities, researches ogame.Researches, energy int64) bool
```
```go
c = GetCachedCelestial("1:2:3")
supplies, facilities, _, _, researches, _ = GetTechs(c.GetID())
Print(IsAvailable(LIGHTLASER, c.GetID(), supplies, facilities, researches, 0))
```

### SolarSatelliteProduction
```go
// Returns the energy production of solar satellite.
SolarSatelliteProduction(temp Temperature, nbr int64) (energy int64)
```
```go
temp = NewTemperature(-12, 40)
energy = SolarSatelliteProduction(temp, 123)
Print("Energy produced is ", energy)

planet = GetCachedPlanets()[0]
energy = SolarSatelliteProduction(planet.Temperature, 123)
Print("Energy produced is ", energy)
```

### IgnorePlanet
```go
// Adds a planet to the avoid list
IgnorePlanet(int64) error
```
```go
IgnorePlanet(1234567)
```

### IgnorePlayer
```go
// Adds a player to the avoid list
IgnorePlayer(int64) error
```
```go
IgnorePlayer(1234567)
```

### IgnoreAlliance
```go
// Adds an alliance to the avoid list
IgnoreAlliance(int64) error
```
```go
IgnoreAlliance(1234567)
```

### IsPlanetIgnored
```go
// True if planet id is in ignored list
IsPlanetIgnored(int64) bool
```
```go
if IsPlanetIgnored(1234567) {
    Print("Planet "+1234567+" is in the list")
}
```

### IsPlayerIgnored
```go
// True if player id is in ignored list
IsPlayerIgnored(int64) bool
```
```go
if IsPlayerIgnored(1234567) {
    Print("Player "+1234567+" is in the list")
}
```

### IsAllianceIgnored
```go
// True if alliance id is in ignored list
IsAllianceIgnored(int64) bool
```
```go
if IsAllianceIgnored(1234567) {
    Print("Alliance "+1234567+" is in the list")
}
```

### ExecIn
```go
// Schedules an execution after 'execIn' milliseconds. Execution will be asynchronous!
ExecIn(milliseconds int64, clb func()) context.CancelFunc
```
```go
func callback() {
    Print("5min passed")
}

ExecIn(5 * 60 * 1000, callback) // callback will be called after 5min
```

```go
// You can also cancel a scheduled job
cancelFn = ExecIn(5 * 60 * 1000, callback)
Sleep(5 * 1000)
cancelFn() // Cancel the job, callback won't be executed
```

```go
// call function with sleep from a loop
func callback() {
    Print("5min passed")
    Sleep(60*1000)
    Print("6min passed")
}

for {
    ExecIn(Random(30*1000, 60*1000), callback)
    Sleep(10*1000)
}
```

### ExecInCh
```go
// Schedules an execution after 'execIn' milliseconds
ExecInCh(milliseconds int64, clb func()) <-chan struct{}
```

### ExecInSync
```go
// Synchronous version of ExecIn
ExecInSync(milliseconds int64, clb func()) context.CancelFunc
```

### ExecAt
```go
// Schedules a one time callback execution at specific time. ExecAt is asynchronous.
ExecAt(at interface{}, clb func()) context.CancelFunc
```
```go
time = import("time")
ExecAt("17:23:00", func() {
    Print("callback is executed at ", time.Now())
})
<-OnQuitCh // Blocks forever so that ExecAt can be executed
```

### ExecAtCh
```go
// Schedules a one time callback execution at specific time.
ExecAtCh(at interface{}, clb func()) <-chan struct{}
```
```go
time = import("time")
Print("Before")
<-ExecAtCh("17:23:00", func() {
    Print("callback is executed at ", time.Now())
}) // Script will block here until callback is done executing at 17h23
Print("After")

// Evacuate fleet at specified time
<-ExecAtCh("17:23:00", func() {
    f = NewFleet()
    f.SetOrigin("M:1:2:3")
    f.SetDestination("M:1:2:4")
    f.SetSpeed(TEN_PERCENT)
    f.SetMission(PARK)
    f.SetAllResources()
    f.SetAllShips()
    fleet, err = f.SendNow()
    Print(fleet.ID, err)
})

// Works with time.Time objects
execTime = time.Now().Add(10 * time.Second)
<-ExecAtCh(execTime, func() {
	Print("callback is executed at ", time.Now())
})
```

### ExecAtSync
```go
// Synchronous version of ExecAt
ExecAtSync(at any, clb func())
```

### IntervalExec
```go
// Schedules an execution on regular intervals
IntervalExec(milliseconds int64, clb func()) context.CancelFunc
```
```go
func callback() {
    Print("5min passed")
}

IntervalExec(5 * 60 * 1000, callback) // callback will be called every 5min
```
```go
// You can also cancel an interval
cancelFn = IntervalExec(5 * 60 * 1000, callback)
Sleep(5 * 1000)
cancelFn() // Cancel the interval, callback won't be executed
```

### CronExec
```go
// Schedules an execution via crontab expression
CronExec(spec string, clb func()) (EntryID, error)
```
```go
func callback() {
    Print("Callback executed")
}

CronExec("0 43 15 * * *", callback) // Execute callback at 15h43 every day
CronExec("@16h43", callback)        // Execute callback at 16h43 every day
CronExec("16:43:12", callback)      // Execute callback at 16h43m12s every day
<-OnQuitCh // Blocks forever so that cronexec can be executed
```

### RemoveCron
```go
// Remove a schduled cron job
RemoveCron(EntryID)
```
```go
func callback() {
    Print("Callback executed")
}
entryID, _ = CronExec("0 43 15 * * *", callback)
Sleep(1000)
RemoveCron(entryID) // Remove scheduled cronjob 
```

### RangeCronExec
```go
// Schedule a callback to be executed in a time range.
// If we currently are in the range, the callback will be scheduled in the remaining time of the range.
// RangeCronExec("00:00:00", "00:15:00", "Do something", func() {  })
// Logs if not in range: Do something at 00:00:00 (+ delay up to 15m)
// Logs if in range:     Do something in 5m10s at 00:05:10
//     (5m10s later)     Do something
RangeCronExec(timeString1, timeString2, msg string, callback func()) (EntryID, error)
```
```go
func callback() {
    Print("Callback executed between 0h and 0h15")
}

RangeCronExec("00:00:00", "00:15:00", "Do something", callback) // Execute callback between 0h and 0h15 every day
<-OnQuitCh // Blocks forever so that cronexec can be executed
```

### Clock
```go
// Return the current time
Clock() (hour, min, sec int64)
```
```go
hour, min, sec = Clock()
if hour >= 15 && min >= 43 {
    Print("Do something")
}
```

### Date
```go
// Return the current date
Date() (year, month, day int64)
```
```go
year, month, day = Date()
Print("Today's date is "+year+"-"+month+"-"+day)
```

### Weekday
```go
// Return the week day (Sunday = 0, Monday = 1... Saturday = 6)
Weekday() int64
```
```go
Print("Today's week day is "+Weekday())
```

### GetTimestamp
```go
// Returns a Unix time, the number of seconds elapsed since January 1, 1970 UTC.
GetTimestamp() int64
```
```go
ts = GetTimestamp()
Print(ts)
```

### Unix
```go
// Unix returns the local Time corresponding to the given Unix time, sec seconds and nsec nanoseconds since January 1, 1970 UTC. 
Unix(sec int64, nsec int64) Time
```
```go
ts = GetTimestamp()
Print(Unix(ts, 0))
```

### OnStateChange
```go
// Register a callback which is executed every time the bot change state
OnStateChange(clb func(locked bool, actor string))
```
```go
OnStateChange(func(botLocked, actor) {
    Print(botLocked, actor)    
})

<-OnQuitCh // Block forever
```

### RecruitOfficer
```go
// Recruit an officer.
// Typ -> 2: Commander | 3: Admiral | 4: Engineer | 5: Geologist | 6: Technocrat
// Days: 7 or 90
RecruitOfficer(typ, days int64) error
```

### HasCommander
```go
// Returns either or not the player has commander.
HasCommander() bool
```

### HasAdmiral
```go
// Returns either or not the player has admiral.
HasAdmiral() bool
```

### HasEngineer
```go
// Returns either or not the player has engineer.
HasEngineer() bool
```

### HasGeologist
```go
// Returns either or not the player has geologist.
HasGeologist() bool
```

### HasTechnocrat
```go
// Returns either or not the player has technocrat.
HasTechnocrat() bool
```

### GetHomeWorld
```go
GetHomeWorld() Celestial
```
```go
homeworld = GetHomeWorld()
Print(homeworld.GetName(), homeworld.GetID())
```

### SetHomeWorld
```go
SetHomeWorld(CelestialID)
```
```go
celestial = GetCachedCelestials()[0]
SetHomeWorld(celestial.GetID())
```

### EnableNJA
```go
EnableNJA()
```
```go
EnableNJA()
```

### DisableNJA
```go
DisableNJA()
```
```go
DisableNJA()
```

### IsNJAEnabled
```go
// Either or not communication with ogame server is enabled
IsNJAEnabled() bool
```
```go
if IsNJAEnabled() {
    Print("yes")
}
```

### IsActive
```go
// Either or not the application will start the bot when it starts
IsActive() bool
```
```go
if IsActive() {
    Print("yes")
}
```

### IsStarted
```go
// Either or not the bot is currently started
IsStarted() bool
```
```go
if IsStarted() {
    Print("yes")
}
```

### Login
```go
// Login to ogame server
Login() error
```
```go
Logout()
Sleep(5 * 60 * 1000) // Sleep 5min
Login()
```

### Logout
```go
// Logout the bot from ogame server
Logout()
```
```go
Logout()
Sleep(5 * 60 * 1000) // Sleep 5min
Login()
```

### IsLoggedIn
```go
// IsLoggedIn returns true if the bot is currently logged-in, otherwise false
IsLoggedIn() bool
```

### GetUniverseName
```go
GetUniverseName() string
```
```go
Print(GetUniverseName()) // Wurren
```

### IsPioneers
```go
// IsPioneers returns true if the bot is uses pioneers lobby
IsPioneers() bool
```

### IsUnderAttack
```go
// Returns true if the user is under attack, false otherwise
IsUnderAttack() bool
```
```go
isUnderAttack = IsUnderAttack()
Print(isUnderAttack)
```

### CharacterClass
```go
// Returns the bot's character class
CharacterClass() CharacterClass
```
```go
Print(CharacterClass())
```

### IsCollector
```go
IsCollector() bool
```

### IsGeneral
```go
IsGeneral() bool
```

### IsDiscoverer
```go
IsDiscoverer() bool
```

### SendMessage
```go
// Sends a message to playerID
SendMessage(playerID int64, message string) error
```
```go
SendMessage(1234567, "Sup!")
```

### SendMessageAlliance
```go
// Sends a message to associationID
SendMessageAlliance(associationID int64, message string) error
```
```go
SendMessageAlliance(537, "Sup!")
```

### GetCelestial
```go
// GetCelestial get the player's planet/moon
GetCelestial(interface{}) (Celestial, error)
```
```go
coord = NewCoordinate(1, 2, 3, PLANET_TYPE)
celestial, err = GetCelestial(coord)
Print(celestial.GetName())
```

### GetCelestials
```go
// GetCelestial get the player's planets & moons
GetCelestials() ([]Celestial, error)
```
```go
celestials, err = GetCelestials()
Print("We have " + len(celestials) + " celestials")
```

### GetPlanet
```go
// GetPlanet get the player's planet
GetPlanet(interface{}) (Planet, error)
```
```go
coord = NewCoordinate(1, 2, 3, PLANET_TYPE)
planet, err = GetPlanet(coord)
Print(planet.GetName())
```

### GetPlanets
```go
// GetPlanets get the player's planets
GetPlanets() []Planet
```
```go
planets = GetPlanets()
Print("We have " + len(planets) + " planets")
```

### GetMoon
```go
// GetMoon get the player's planets
GetMoon(interface{}) (Moon, error)
```
```go
coord = NewCoordinate(1, 2, 3, MOON_TYPE)
moon, err = GetMoon(coord)
Print(moon.GetName())
```

### GetMoons
```go
// GetMoon get the player's moons
GetMoons() []Moon
```
```go
moons = GetMoons()
Print("We have " + len(moons) + " moons")
```

### GetCachedPlanets
```go
// GetCachedPlanets return planets from cached value
GetCachedPlanets() []Planet
```

### GetCachedMoons
```go
// GetCachedMoons return moons from cached value
GetCachedMoons() []Moon
```

### GetCachedCelestial
```go
// GetCachedCelestial return celestial from cached value
GetCachedCelestial(interface{}) Celestial
```

### GetCachedCelestials
```go
// GetCachedCelestials get all cached celestials
GetCachedCelestials() []Celestial
```

### GetCachedPlayer
```go
// GetCachedPlayer returns currently logged in cached player information
GetCachedPlayer() UserInfos
```

### PlayersData
```go
// Get all players information using ogame xml API (/api/players.xml)
PlayersData() ([]PlayerDataShort, error)
```

### PlayerDataByID
```go
// Get a player information with their player ID using ogame xml API
PlayerDataByID(playerID int64) (PlayerData, error)
```

### PlayerDataByName
```go
// Get a player information with their nickname using ogame xml API
PlayerDataByName(nick string) (PlayerData, error)
```

### FindDebrisFieldWithMinimumTravelTime
```go
// Finds debris field that is at a minimum travel time at slowest speed
FindDebrisFieldWithMinimumTravelTime(origin Coordinate, ships ShipsInfos, minimumTravelTime time.Duration) (destination Coordinate, travelTime time.Duration, fuel int64, err error)
```

```go
time = import("time")
planet = GetCachedPlanets()[0]
ships, _ = planet.GetShips()
minimumTravelTime = 7 * time.Hour
debrisCoordinate, travelTime, fuel, err = FindDebrisFieldWithMinimumTravelTime(planet.GetCoordinate(), ships, minimumTravelTime)
Print(debrisCoordinate, travelTime, fuel, err)
```

### FindEmptyPlanetWithMinimumTravelTime
```go
// Finds empty planet that is at a minimum travel time at slowest speed
FindEmptyPlanetWithMinimumTravelTime(origin Coordinate, ships ShipsInfos, minimumTravelTime time.Duration) (destination Coordinate, travelTime time.Duration, fuel int64, err error)
```

```go
time = import("time")
planet = GetCachedPlanets()[0]
ships, _ = planet.GetShips()
minimumTravelTime = 7 * time.Hour
emptyPlanetCoordinate, travelTime, fuel, err = FindEmptyPlanetWithMinimumTravelTime(planet.GetCoordinate(), ships, minimumTravelTime)
Print(emptyPlanetCoordinate, travelTime, fuel, err)
```

### FindInactivePlanetWithMinimumTravelTime
```go
// Finds an inactive planet that is at a minimum travel time at slowest speed
FindInactivePlanetWithMinimumTravelTime(origin Coordinate, ships ShipsInfos, minimumTravelTime time.Duration, withMoon bool) (destination Coordinate, travelTime time.Duration, fuel int64, err error)
```

```go
time = import("time")
planet = GetCachedPlanets()[0]
ships, _ = planet.GetShips()
minimumTravelTime = 7 * time.Hour
inactivePlanetCoordinate, travelTime, fuel, err = FindInactivePlanetWithMinimumTravelTime(planet.GetCoordinate(), ships, minimumTravelTime, false)
Print(inactivePlanetCoordinate, travelTime, fuel, err)
```


### FindAbandonedPlanetWithMinimumTravelTime
```go
// Finds an abandoned planet that is at a minimum travel time at slowest speed
FindAbandonedPlanetWithMinimumTravelTime(origin Coordinate, ships ShipsInfos, minimumTravelTime time.Duration, withMoon bool) (destination Coordinate, travelTime time.Duration, fuel int64, err error)
```

```go
time = import("time")
planet = GetCachedPlanets()[0]
ships, _ = planet.GetShips()
minimumTravelTime = 7 * time.Hour
abandonedPlanetCoordinate, travelTime, fuel, err = FindAbandonedPlanetWithMinimumTravelTime(planet.GetCoordinate(), ships, minimumTravelTime, false)
Print(abandonedPlanetCoordinate, travelTime, fuel, err)
```

### GetSystemsInRange
```go
// GetSystemsInRange returns the systems in a radius around the origin
GetSystemsInRange(originSystem, radius int64) []int64
```
```go
Print(GetSystemsInRange(3, 0)) // [3]
Print(GetSystemsInRange(3, 1)) // [2, 3, 4]
Print(GetSystemsInRange(3, 2)) // [1, 2, 3, 4, 5]
Print(GetSystemsInRange(3, 3)) // [499, 1, 2, 3, 4, 5, 6]
```

### GetSystemsInRangeAsc
```go
// GetSystemsInRangeDesc returns the systems in a radius around the origin sorted by distance (closer to farther away)
GetSystemsInRangeAsc(originSystem, radius int64) []int64
```
```go
Print(GetSystemsInRangeAsc(3, 0)) // [3]
Print(GetSystemsInRangeAsc(3, 1)) // [3, 2, 4]
Print(GetSystemsInRangeAsc(3, 2)) // [3, 2, 4, 1, 5]
Print(GetSystemsInRangeAsc(3, 3)) // [3, 2, 4, 1, 5, 499, 6]
```

### GetSystemsInRangeDesc
```go
// GetSystemsInRangeDesc returns the systems in a radius around the origin sorted by distance (farther away to closer)
GetSystemsInRangeDesc(originSystem, radius int64) []int64
```
```go
Print(GetSystemsInRangeDesc(3, 0)) // [3]
Print(GetSystemsInRangeDesc(3, 1)) // [2, 4, 3]
Print(GetSystemsInRangeDesc(3, 2)) // [1, 5, 2, 4, 3]
Print(GetSystemsInRangeDesc(3, 3)) // [499, 6, 1, 5, 2, 4, 3]
```

### Shuffle
```go
// Shuffle a slice in place
Shuffle(slice []interface{})
```
```go
a = [1, 2, 3, 4, 5]
Shuffle(a)
Print(a)
```

### GetResources
```go
GetResources(CelestialID) (Resources, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
resources, err = GetResources(celestial.GetID())

// This also work
resources, err = celestial.GetResources()
```

### GetResourcesDetails
```go
// GetResourcesDetails gets the resources details
GetResourcesDetails(CelestialID) (ResourcesDetails, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
resourcesDetails, err = GetResourcesDetails(celestial.GetID())

// This also work
resourcesDetails, err = celestial.GetResourcesDetails()
```

### GetAllResources
```go
// GetAllResources gets the resources of all planets and moons
GetAllResources() (map[CelestialID]Resources, error)
```
```go
resources, _ = GetAllResources()
Print(resources)
```

### Abandon
```go
// Abandon a planet
Abandon(interface{}) error
```

### CollectAllMarketplaceMessages
```go
// CollectAllMarketplaceMessages collect all marketplace messages
CollectAllMarketplaceMessages() error
```

### CollectMarketplaceMessage
```go
// CollectMarketplaceMessage collect marketplace message
CollectMarketplaceMessage(msg MarketplaceMessage) error
```

### GetDMCosts
```go
// GetDMCosts returns fast build with DM information
GetDMCosts(celestialID CelestialID) (DMCosts, error)
```
```go
c = GetCachedCelestial("1:2:3")
info, err = GetDMCosts(c.GetID())
Print(info, err)
```

### UseDM
```go
// UseDM use dark matter to fast build
// typ can be buildings/research/shipyard
UseDM(typ string, celestialID CelestialID) error
```
```go
c = GetCachedCelestial("1:2:3")
UseDM("shipyard", c.GetID())
```

### GetItems
```go
// GetItems get all items information
GetItems(celestialID CelestialID) ([]Item, error)
```

### GetActiveItems
```go
// GetActiveItems get all active items information
GetActiveItems(celestialID CelestialID) ([]ActiveItem, error)
```

### ActivateItem
```go
// ActivateItem activate an item
ActivateItem(string, CelestialID) error
```
```go
c = GetCachedCelestial("1:2:3")
ActivateItem("de922af379061263a56d7204d1c395cefcfb7d75", c.GetID()) // Activate "Bronze Metal Booster"
```

### OfferSellMarketplace
```go
// Create a sell offer on the marketplace 
OfferSellMarketplace(itemID interface{}, quantity, priceType, price, priceRange int64, celestialID CelestialID) error
```
```go
OfferSellMarketplace(LIGHTFIGHTER, 10, 1, 40000, 20, 0) // Sell ships for metal
OfferSellMarketplace(1, 100000, 2, 200000, 20, 0)       // Sell metal for crystal
OfferSellMarketplace("de922af379061263a56d7204d1c395cefcfb7d75", 1, 2, 50000, 20, 0) // Sell "Bronze Metal Booster" for crystal
```

### OfferBuyMarketplace
```go
// Create a buy offer on the marketplace
OfferBuyMarketplace(itemID interface{}, quantity, priceType, price, priceRange int64, celestialID CelestialID) error
```
```go
OfferBuyMarketplace(LIGHTFIGHTER, 10, 1, 40000, 20, 0)
```

### AttackStrength
```go
// Return the attack strength of an entity
AttackStrength(id ogame.ID, researches ogame.Researches) (attackStrength int64)
```
```go
researches = DBGetResearches()
Print(AttackStrength(LIGHTFIGHTER, researches))
```

### ShipsAttackStrength
```go
ShipsAttackStrength(ships ogame.ShipsInfos, researches ogame.Researches) (attackStrength int64)
```

### ShipsAttackStrengthUsingOwnResearches
```go
ShipsAttackStrengthUsingOwnResearches(ships ogame.ShipsInfos) (attackStrength int64)
```
```go
celestial = GetCachedCelestials()[0]
ships, _ = celestial.GetShips()
Print(ShipsAttackStrengthUsingOwnResearches(ships))
```

### GetHighscore
```go
// Get the highscores information
// category -> 1:player, 2:alliance
// typ -> 0:Total, 1:Economy, 2:Research, 3:Military, 4:Military Built, 5:Military Destroyed, 6:Military Lost, 7:Honor
// page must be greater than or equal to 1
GetHighscore(category, typ, page int64) (Highscore, error)
```
```go
highscore, _ = GetHighscore(1, 1, 1) // First highscore page of Players Economy
Print(highscore.Players[0])
```

### GetFleets
```go
// Get the player's own fleets activities
GetFleets() ([]Fleet, Slots)
```
```go
fleet, slots = GetFleets()
for f in fleet {
    Print(f)
}
```

### GetAttacks
```go
// GetAttacks get enemy fleets attacking you
GetAttacks() []AttackEvent
```
```go
attacks = GetAttacks()
for attack in attacks {
    Print(attack)
}
```

### GetAttacksUsing
```go
// GetAttacksUsing get enemy fleets attacking you using specified celestial for the check
GetAttacks(CelestialID) []AttackEvent
```

```go
celestial = GetCachedCelestial("1:2:3")
attacks = GetAttacksUsing(celestial.GetID())
for attack in attacks {
    Print(attack)
}
```

### GalaxyInfos
```go
// GalaxyInfos get information of all planets and moons of a solar system
GalaxyInfos(galaxy, system int64) (SystemInfos, error)
```
```go
systemInfo, _ = GalaxyInfos(4, 116)
for i = 1; i <= 15; i++ {
    planetInfo = systemInfo.Position(i)
    if planetInfo != nil {
        Print(planetInfo.Name)
    }
}
```

### GalaxyInfosUsing
```go
// GalaxyInfos get information of all planets and moons of a solar system using specified celestial for the check
GalaxyInfosUsing(galaxy, system int64, celestialID CelestialID) (SystemInfos, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
systemInfo, _ = GalaxyInfosUsing(4, 116, celestial.GetID())
for i = 1; i <= 15; i++ {
    planetInfo = systemInfo.Position(i)
    if planetInfo != nil {
        Print(planetInfo.Name)
    }
}
```

### GetPlanetInfo
```go
// GetPlanetInfo get information of a planet and moon
GetPlanetInfo(coordinate interface{}) (PlanetInfos, error)
```
```go
planetInfos, _ = GetPlanetInfo("4:116:6")

coord = NewCoordinate(4, 116, 6, PLANET_TYPE)
planetInfos, _ = GetPlanetInfo(coord)
```

### GetPlanetInfoUsing
```go
// GetPlanetInfo get information of a planet and moon
GetPlanetInfoUsing(coordinate interface{}, celestialID CelestialID) (PlanetInfos, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
planetInfos, _ = GetPlanetInfoUsing("4:116:6", celestial.GetID())
```

### GetDepartureTime
```go
// Get the departure time to arrive at desired arrival time
GetDepartureTime(arrival, origin, destination interface{}, ShipsInfos, Speed, MissionID) Time
```
```go
s1 = NewShipsInfos()
s1.Set(LARGECARGO, 10)

// Example 1
departureTime = GetDepartureTime("16:00:00", "4:208:8", "4:212:10", *s1, HUNDRED_PERCENT, TRANSPORT)
Print("Departure time should be " + departureTime)

// Example 2
time = import("time")
desiredArrivalTime = time.Now().Add(3 * time.Hour)
departureTime = GetDepartureTime(desiredArrivalTime, "4:208:8", "4:212:10", *s1, HUNDRED_PERCENT, TRANSPORT)
Print("Departure time should be " + departureTime)

// Example 3
origin = NewCoordinate(4, 208, 8, PLANET_TYPE)
destination = NewCoordinate(4, 212, 10, PLANET_TYPE)
departureTime = GetDepartureTime(desiredArrivalTime, origin, destination, *s1, HUNDRED_PERCENT, TRANSPORT)
Print("Departure time should be " + departureTime)

<-ExecAtCh(departureTime, func() {
    // Send fleet here
})
```

### LowestSpeed
```go
// Get the departure time to arrive at desired arrival time
LowestSpeed(arrival, origin, destination interface{}, ShipsInfos) (speed Speed, fuel int64, departureTime Time, err error)
```
```go
s1 = NewShipsInfos()
s1.Set(LARGECARGO, 10)

// Example 1
speed, fuel, departureTime, err = LowestSpeed("16:00:00", "4:208:8", "4:212:10", *s1)
Print("Departure time should be " + departureTime)

// Example 2
time = import("time")
desiredArrivalTime = time.Now().Add(3 * time.Hour)
speed, fuel, departureTime, err = LowestSpeed(desiredArrivalTime, "4:208:8", "4:212:10", *s1)
Print("Departure time should be " + departureTime)

// Example 3
origin = NewCoordinate(4, 208, 8, PLANET_TYPE)
destination = NewCoordinate(4, 212, 10, PLANET_TYPE)
speed, fuel, departureTime, err = LowestSpeed(desiredArrivalTime, origin, destination, *s1)
Print("Departure time should be " + departureTime)

<-ExecAtCh(departureTime, func() {
    // Send fleet here with proper speed
})
```

### Simulator
```go
// Simulator runs a battle simulation.
// 
// Cloud limits: 500k ships, 5 simulations
// 25M points / minute. Calling "Simulator" will cost (nb ships * simulations) points.
//
// Self-host have no limit
Simulator(payload map[string]map[string]interface{}) (simulator.SimulatorResult, error)
```

```go
payload = {
    "Attacker": {
        "HyperspaceTechnology": 1, // Default to your tech
        "Weapon"              : 1, // Default to your tech
        "Shield"              : 1, // Default to your tech
        "Armour"              : 1, // Default to your tech
        "CombustionDrive"     : 1, // Default to your tech
        "ImpulseDrive"        : 1, // Default to your tech
        "HyperspaceDrive"     : 1, // Default to your tech
        "CharacterClass"      : 1, // NoClass: 0, Collector: 1, General: 2, Discoverer: 3. Default to your class
        "Galaxy"              : 1,
        "System"              : 205,
        "Position"            : 12,
        "LightFighter"        : 100,
        "HeavyFighter"        : 0,
        "Cruiser"             : 0,
        "Battleship"          : 0,
        "Battlecruiser"       : 0,
        "Bomber"              : 0,
        "Destroyer"           : 0,
        "Deathstar"           : 0,
        "SmallCargo"          : 0,
        "LargeCargo"          : 0,
        "ColonyShip"          : 0,
        "Recycler"            : 0,
        "EspionageProbe"      : 0,
        "SolarSatellite"      : 0,
        "Crawler"             : 0,
        "Reaper"              : 0,
        "Pathfinder"          : 0,
    },
    "Defender": {
        "Galaxy"                : 1,
        "System"                : 102,
        "Position"              : 11,
        "Metal"                 : 1000,
        "Crystal"               : 0,
        "Deuterium"             : 0,
        "Weapon"                : 1,
        "Shield"                : 1,
        "Armour"                : 1,
        "LightFighter"          : 50,
        "HeavyFighter"          : 0,
        "Cruiser"               : 0,
        "Battleship"            : 0,
        "Battlecruiser"         : 0,
        "Bomber"                : 0,
        "Destroyer"             : 0,
        "Deathstar"             : 0,
        "SmallCargo"            : 0,
        "LargeCargo"            : 0,
        "ColonyShip"            : 0,
        "Recycler"              : 0,
        "EspionageProbe"        : 0,
        "SolarSatellite"        : 0,
        "Crawler"               : 0,
        "Reaper"                : 0,
        "Pathfinder"            : 0,
        "RocketLauncher"        : 0,
        "LightLaser"            : 0,
        "HeavyLaser"            : 0,
        "GaussCannon"           : 0,
        "IonCannon"             : 0,
        "PlasmaTurret"          : 0,
        "SmallShieldDome"       : 0,
        "LargeShieldDome"       : 0,
        "AntiBallisticMissiles" : 0,
        "InterplanetaryMissiles": 0,
    },
    "Params": {
        "Simulations"        : 5,  // Default to 5
        "FleetToDebris"      : 70, // Default to your universe settings
        "DefenceToDebris"    : 0,  // Default to your universe settings
        "DefenceRepairFactor": 70, // Default to your universe settings
        "Plunder"            : 50, // Default to your universe settings
    }
}

res, err = Simulator(payload)
Print(res, err)
```

### VersionCompare
```go
// Compare compares this version to another version. This
// returns -1, 0, or 1 if this version is smaller, equal,
// or larger than the other version, respectively.
VersionCompare(version1, version2 string) int
```
```go
Print(VersionCompare("0.1.1", "0.9.0")) // -1
Print(VersionCompare("0.1.1", "0.1.1")) //  0
Print(VersionCompare("0.1.1", "0.0.1")) //  1

if VersionCompare(__VERSION__, "0.91.8") == -1 {
    Print("You need version 0.91.8 or higher")
}
```

### Dotify
```go
// Pretty print a large number using dot as thousand separator
Dotify(in int64) string
```
```go
Print(Dotify(1000000)) // 1.000.000
```

### ShortDur
```go
// Pretty print a duration
ShortDur(v interface{}) string
```
```go
Print(ShortDur(121)) // 2m1s
Print(ShortDur(10000)) // 2h46m40s
```

### ShortShipsInfos
```go
// Return a short inline description of the ships. eg: "LightFighter: 10, SmallCargo: 3"
ShortShipsInfos(ships ShipsInfos) string
```

### Bytes2Str
```go
// Convert a []byte to string
Bytes2Str([]byte) string
```

### ID2Str
```go
// Get the string representation of an ogame ID
ID2Str(ID) string
```
```go
Print(ID2Str(204))       // LightFighter
Print(ID2Str(METALMINE)) // MetalMine
```

### Atoi
```go
// Convert a string to an integer
Atoi(string) int64
```
```go
Print(1 + Atoi("2")) // 3
```

### Itoa
```go
// Convert a integer to a string
Itoa(int64) string
```
```go
Print(Itoa(123) + "4") // 1234
```

### StartScript
```go
// Start a script. This call is async, it doesn't wait for the script to terminate.
StartScript(name string) error
```
```go
StartScript("another.ank")
Print("another.ank started")
```

### StopScript
```go
// Stops a script
StopScript(name string) error
```
```go
StopScript("another.ank")
Print("another.ank stopped")
```

### PauseScript
```go
// Pause a script
PauseScript(name string) error
```

### ResumeScript
```go
// Resume a paused script
ResumeScript(name string) error
```

### IsPausedScript
```go
// Either or not the script is paused
IsPausedScript(name string) error
```

### IsScriptRunning
```go
// Returns true if 'script' is running, otherwise false
IsScriptRunning(name string) bool
```
```go
Print(IsScriptRunning("another.ank"))
```

### GetScripts
```go
// Returns the names of all scripts for this bot
GetScripts() []string
```
```go
Print(GetScripts())
```

### GetRunningScripts
```go
// Returns the names of all currently running scripts for this bot
GetRunningScripts() []string
```
```go
Print(GetRunningScripts())
```

### StartBrain
```go
// Start brain
StartBrain()
```

### StopBrain
```go
// Stop brain
StopBrain()
```

### StartScanner
```go
// Start scanner
StartScanner()
```

### StopScanner
```go
// Stop scanner
StopScanner()
```

### StartHunter
```go
// Start hunter
StartHunter()
```

### StopHunter
```go
// Stop hunter
StopHunter()
```

### StartSleepMode
```go
// Turn on sleep mode
StartSleepMode()
```

### StopSleepMode
```go
// Turn off sleep mode
StopSleepMode()
```

### StartPhalanxSession
```go
// Starts a new phalanx session
StartPhalanxSession(origin ogame.MoonID, target ogame.Coordinate, phalanxDelay int64) (sessionID int64, err error)
```

```go
moon = GetCachedCelestial("M:1:2:3")
target = NewCoordinate(1, 2, 4, PLANET_TYPE)
id, err = StartPhalanxSession(moon.GetID(), target, 3)
Print(id, err)
for {
    select {
    case report = <-OnPhalanxReport:
        Print(report)
    }
}
```

### StopPhalanxSession
```go
// Stops a phalanx session
StopPhalanxSession(sessionID int64)
```

### StartFarmingBot
```go
// Starts farming bot
StartFarmingBot()
```

### StopFarmingBot
```go
// Stops farming bot
StopFarmingBot()
```

### PauseFarmingBot
```go
// Pause farming bot
PauseFarmingBot()
```

### ResumeFarmingBot
```go
// Resume farming bot
ResumeFarmingBot()
```

### IsRunningFarmingBot
```go
// Returns true in case Farming Bot is running, otherwise false
IsRunningFarmingBot() bool
```

### IsPausedFarmingBot
```go
// Returns true in case Farming Bot is paused, otherwise false
IsPausedFarmingBot() bool
```

### IsFarmSessionOngoing
```go
// Returns true if the bot is currently working on a farm session
IsFarmSessionOngoing() bool
```

### FarmingBotSessionsCount
```go
// Returns a count of active farm sessions including the one being processed
FarmingBotSessionsCount() int64
```

### AbortAllFarmingSessions
```go
// Abort all farming sessions
AbortAllFarmingSessions()
```

### StartExpeditionsBot
```go
// Starts expeditions bot
StartExpeditionsBot()
```

### StopExpeditionsBot
```go
// Stops expeditions bot
StopExpeditionsBot()
```

### IsRunningExpeditionsBot
```go
// Returns true in case Expeditions Bot is running, otherwise false
IsRunningExpeditionsBot() bool
```

### StartColonizerBot
```go
// Starts colonizer bot
StartColonizerBot()
```

### StopColonizerBot
```go
// Stops colonizer bot
StopColonizerBot()
```

### IsRunningColonizerBot
```go
// Returns true in case Colonizer Bot is running, otherwise false
IsRunningColonizerBot() bool
```

### StartDiscoveryBot
```go
// Starts discovery bot
StartDiscoveryBot()
```

### StopDiscoveryBot
```go
// Stops discovery bot
StopDiscoveryBot()
```

### IsRunningDiscoveryBot
```go
// Returns true in case Discovery Bot is running, otherwise false
IsRunningDiscoveryBot() bool
```

### StartDefenderBot
```go
// Starts defender bot
StartDefenderBot()
```

### StopDefenderBot
```go
// Stops defender bot
StopDefenderBot()
```

### IsRunningDefenderBot
```go
// Returns true in case Defender Bot is running, otherwise false
IsRunningDefenderBot() bool
```

### SetDefenderCheckInterval
```go
// Set the defender check interval (in seconds)
SetDefenderCheckInterval(min, max int64)
```

### SetDefenderCheckOrigin
```go
// Set the defender check origin
// 1 -> Random planet/moon
// 2 -> Homeworld
// 3 -> Latest active planet/moon
// 4 -> Random moon
SetDefenderCheckOrigin(originNum int64)
```

### DBGetResourceBuildings
```go
// Get resources buildings from database
DBGetResourceBuildings(CelestialID) (ResourcesBuildings, error)
```

### DBGetResourceSettings
```go
// Get resources settings from database
DBGetResourceSettings(CelestialID) (ResourceSettings, error)
```

### DBGetFacilities
```go
// Get Facilities from database
DBGetFacilities(CelestialID) (Facilities, error)
```

### DBGetShips
```go
// Get Ships from database
DBGetShips(CelestialID) (ShipsInfos, error)
```

### DBGetDefenses
```go
// Get defenses from database
DBGetDefenses(CelestialID) (DefensesInfos, error)
```

### DBGetResearches
```go
// Get Ships from database
DBGetResearches() Researches
```

### Put
```go
// Put data into storage
Put(key string, val interface{}) error
```

```go
Put("key1", 123)
Put("key2", "Hello world!")
Put("key3", true)
Put("key4", [1, 2, 3])
```

### Get
```go
// Get data from storage
Get(key string) (interface{}, error)
```

```go
val, err = Get("key1")
Print(val, err)
```

### Has
```go
// Has returns if the key exists in storage
Has(key string) bool
```

### Delete
```go
// Delete a key/value pair from storage
Delete(key string) bool
```

### ParseCoord
```go
// Parse a string into a coordinate
ParseCoord(str string) (coord Coordinate, err error)
```
```go
Print(ParseCoord("1:2:3"))     // [P:1:2:3]
Print(ParseCoord("P:1:2:3"))   // [P:1:2:3]
Print(ParseCoord("[1:2:3]"))   // [P:1:2:3]
Print(ParseCoord("[P:1:2:3]")) // [P:1:2:3]
Print(ParseCoord("M:1:2:3"))   // [M:1:2:3]
Print(ParseCoord("[M:1:2:3]")) // [M:1:2:3]
Print(ParseCoord("D:1:2:3"))   // [D:1:2:3]
Print(ParseCoord("[D:1:2:3]")) // [D:1:2:3]
```

### NowTimeString
```go
// NowTimeString returns time.Now in a time string format "XX:XX:XX"
NowTimeString() string
```
```go
Print(NowTimeString()) // 01:15:47
```

### NowInTimeRange
```go
// NowInTimeRange returns either or not the current time is in a time range
NowInTimeRange(timeStr1, timeStr2 string) bool
```
```go
Print(NowInTimeRange("01:00:00", "02:00:00")) // true
```

### DurationBetweenTimeStrings
```go
// DurationBetweenTimeStrings returns the duration between two time strings
DurationBetweenTimeStrings(timeStr1, timeStr2 string) time.Duration
```
```go
Print(DurationBetweenTimeStrings("01:00:00", "02:00:00")) // 1h0m0s
```

### MillisecondsBetweenTimeStrings
```go
// MillisecondsBetweenTimeStrings returns the duration in milliseconds between two time strings
MillisecondsBetweenTimeStrings(timeStr1, timeStr2 string) time.Duration
```
```go
Print(MillisecondsBetweenTimeStrings("01:00:00", "02:00:00")) // 3600000
```

### ParseNextDatetimeAt
```go
// ParseNextDatetimeAt given a string in this format 00:00:00 returns the next Time object at that time.
ParseNextDatetimeAt(hourMinSec string) (time.Time, error)
```
```go
parsedTime, err = ParseNextDatetimeAt("23:20:31")
Print(parsedTime, err)
```

### GetNextDatetimeAt
```go
// GetNextDatetimeAt given a hour, minute, second, returns the next Time object at that time.
GetNextDatetimeAt(hour, min, sec int64) (time.Time, error)
```
```go
nextDatetime, err = GetNextDatetimeAt(23, 20, 31)
Print(nextDatetime, err)
```

### ShipFuel
```go
ShipFuel(ID) int64
```

### ShipSpeed
```go
ShipSpeed(ID) int64
```

### ShipCargo
```go
ShipCargo(ID) int64
```

### Speed
```go
Speed(shipsInfos ShipsInfos) int64
```

```go
s1 = NewShipsInfos()
s1.Set(LARGECARGO, 10)
Print(Speed(*s1))

celestial = GetCachedCelestials()[0]
s2, _ = celestial.GetShips()
Print(Speed(s2))
```

### Cargo
```go
Cargo(shipsInfos ShipsInfos) int64
```

```go
s1 = NewShipsInfos()
s1.Set(LARGECARGO, 10)
Print(Cargo(*s1))

celestial = GetCachedCelestials()[0]
s2, _ = celestial.GetShips()
Print(Cargo(s2))
```

### CalcCargo
```go
// Calculate how many small cargo OR how many large cargo you need to transport a total amount of resources
CalcCargo(resourcesTotal int64) (largeCargoNeeded, smallCargoNeeded int64)
```
```go
resources = NewResources(10000, 20000, 30000)
lc, sc = CalcCargo(resources.Total())
Print("You need either " + lc + " large cargo OR " + sc + " small cargo")
```

### CalcFastCargo
```go
// Calculate the best combo to transport a total amount of resources according to your available ships.
CalcFastCargo(lcAvail, scAvail, resourcesTotal int64) (largeCargo, smallCargo, cargo int64)
```
```go
resources = NewResources(10000, 20000, 30000)

smallCargoAvailable = 10
largeCargoAvailable = 10
lc, sc, cargo = CalcFastCargo(largeCargoAvailable, smallCargoAvailable, resources.Total())
Print("You need to send " + lc + " large cargo and " + sc + " small cargo") // You need to send 0 large cargo and 9 small cargo

smallCargoAvailable = 8
largeCargoAvailable = 10
lc, sc, cargo = CalcFastCargo(largeCargoAvailable, smallCargoAvailable, resources.Total())
Print("You need to send " + lc + " large cargo and " + sc + " small cargo") // You need to send 1 large cargo and 4 small cargo
```

### CalcFastCargoPF
```go
// Calculate the best combo to transport a total amount of resources according to your available ships.
CalcFastCargoPF(pfAvail, lcAvail, scAvail, resourcesTotal int64) (pathfinder, largeCargo, smallCargo, cargo int64)
```

### CalcPreferredCargo
```go
// Calculate ships to transport resources according to your available ships and preferred transport.
CalcPreferredCargo(preferredID ogame.ID, epAvail, pfAvail, lcAvail, scAvail, resourcesTotal int64, roundUp bool) (ep, pf, lc, sc, cargo int64)
```

### GetPlayerCoordinates
```go
// Gets all the coordinates of a player from the local database
GetPlayerCoordinates(playerID int64) []Coordinate
```

### Base64
```go
// Base64 encode a string 
Base64(string) string
```

### Base64Decode
```go
// Base64Decode decode a base64 encoded string 
Base64(string) (string, error)
```

### GetSortedCelestials
```go
// GetSortedCelestials gets all celestials sorted by proximity to destination.
// If planets are at different distances to the destination, the order will be moon first, planet second.
// M1, P1, M2, P2, M3, P3 ...
// If planets are at the same distance to the destination, the order will be all the moons first then all planets.
// M1, M2, M3, P1, P2, P3 ...
GetSortedCelestials(destination Coordinate) []Celestial
```

### GetSortedPlanets
```go
// GetSortedPlanets ...
GetSortedPlanets(destination Coordinate) []Planet
```

### GetSortedMoons
```go
// GetSortedMoons ...
GetSortedMoons(destination Coordinate) []Moon
```

### SendMail
```go
// Send an email. Your SMTP settings must be properly configured. 
SendMail(subject, body, to string) error
```
```go
SendMail("Attack detected", "I'm under attack at x:x:x", "you@gmail.com")
```

### SendDiscord
```go
// Send a discord notification
SendDiscord(webhook, msg string) error
```
```go
SendDiscord(DISCORD_WEBHOOK, "Attack detected") // use discord webhook from bot setting
```

### SendTelegram
```go
// Send a telegram notification
SendTelegram(chatID int64, msg string) error
```
```go
SendTelegram(TELEGRAM_CHAT_ID, "Attack detected") // use chat id from bot setting
SendTelegram(1234567, "Attack detected")
```

### SendTelegramReplyMarkup
```go
// Send a telegram notification
SendTelegramReplyMarkup(chatID int64, msg string, replyMarkup any) (tgbotapi.Message, error)
```
```go
btn1 = NewInlineKeyboardButtonData("Bot0 [1:2:3]", "0")
btn2 = NewInlineKeyboardButtonData("Bot1 [1:2:4]", "1")
btn3 = NewInlineKeyboardButtonData("Bot2 [1:2:5]", "2")
btn4 = NewInlineKeyboardButtonData("Bot3 [1:2:6]", "3")
row = NewInlineKeyboardRow(btn1, btn2, btn3, btn4)
keyboard = NewInlineKeyboardMarkup(row)
sentMsg, _ = SendTelegramReplyMarkup(TELEGRAM_CHAT_ID, "Some message", keyboard)

answer = ""
for {
    m = <-OnTelegramUpdateReceivedCh
    if m.CallbackQuery.ID != "" {
        if m.CallbackQuery.Message.MessageID == sentMsg.MessageID {
            AnswerCallbackQuery(m.CallbackQuery.ID, "We received the data")
            answer = m.CallbackQuery.Data
            break
        }
    }
}

Print(answer)
```

```go
// Adding rows dynamically
keyboard = NewInlineKeyboardMarkup()
for b in AllBots() {
    btn = NewInlineKeyboardButtonData(b.GetPlayerName(), Itoa(b.GetID()))
    row = NewInlineKeyboardRow(btn)
    keyboard.Add(row)
}
sentMsg, _ = SendTelegramReplyMarkup(TELEGRAM_CHAT_ID, "Some message", keyboard)
```

### NewInlineKeyboardMarkup
```go
// NewInlineKeyboardMarkup creates a new inline keyboard.
// See SendTelegramReplyMarkup for example
NewInlineKeyboardMarkup(rows ...[]InlineKeyboardButton) InlineKeyboardMarkup
```

### NewInlineKeyboardRow
```go
// NewInlineKeyboardRow creates an inline keyboard row with buttons.
// See SendTelegramReplyMarkup for example
NewInlineKeyboardRow(buttons ...InlineKeyboardButton) []InlineKeyboardButton
```

### NewInlineKeyboardMarkup
```go
// NewInlineKeyboardMarkup creates a new inline keyboard.
// See SendTelegramReplyMarkup for example
NewInlineKeyboardMarkup(rows ...[]InlineKeyboardButton) InlineKeyboardMarkup
```

### NewKeyboardButtonRow
```go
// NewKeyboardButtonRow creates a row of keyboard buttons.
// See SendTelegramReplyMarkup for example
NewKeyboardButtonRow(buttons ...KeyboardButton) []KeyboardButton
```

### NewKeyboardButton
```go
// NewKeyboardButton creates a regular keyboard button.
// See SendTelegramReplyMarkup for example
NewKeyboardButton(text string) KeyboardButton
```

### NewReplyKeyboard
```go
// NewReplyKeyboard creates a new regular keyboard with sane defaults.
// See SendTelegramReplyMarkup for example
NewReplyKeyboard(rows ...[]KeyboardButton) ReplyKeyboardMarkup
```

### AnswerCallbackQuery
```go
// AnswerCallbackQuery sends a response to an inline query callback.
// See SendTelegramReplyMarkup for example
AnswerCallbackQuery(callbackQueryID string, text string) error
```

### NewFleet
```go
// Return a fleet builder
NewFleet() *FleetBuilder
```
```go
f = NewFleet()
f.SetOrigin("1:2:3")
f.SetDestination("1:2:16")
f.SetSpeed(HUNDRED_PERCENT)
f.SetMission(EXPEDITION)
f.AddShips(LIGHTFIGHTER, 2)
f.AddShips(SMALLCARGO, 3)
f.SetDuration(1)
f.SendNow()

f = NewFleet()
f.SetOrigin("M:1:2:3")
f.SetDestination("M:1:2:4")
f.SetSpeed(TEN_PERCENT)
f.SetMission(PARK)
f.SetAllResources()
f.SetAllShips()
fourHours = 4 * 60 * 60
f.SetRecallIn(fourHours)
f.SendNow()
```

### NewFarmSession
```go
// Return a farm session builder
NewFarmSession() *FarmSessionBuilder
```
```go
origin = GetCachedCelestials()[0]
f = NewFarmSession()
f.SetOrigin(origin)
f.SetRange(4, 1, 497)
f.SetProbes(6)
f.SetAdditionalCargo(11)
f.SetMinimumResourcesToAttack(300000)
f.SetMinimumDefensesToIgnore(0)
f.SetMinimumStorageToIgnore(0, 0, 0)
f.SetMinimumPlayerRank(0)
f.SetEspionageProbeRaids(false)
f.SetUsePathfinders(false)
f.SetFastAttacking(true)
f.SetAttackFromNearestPlanet(false)
f.SetAttackFromNearestMoon(false)
f.SetDeleteCombatReports(true)
f.SetFarmSpeed(HUNDRED_PERCENT)
f.SetPriorityRatio(1, 1, 1)
session, err = f.BuildFarmSession()
Print(session.ID, err)
```

### NewACS
```go
// Return a new ACS builder
NewACS() *ACS
```
```go
planet = GetCachedPlanets()[0]
group1 = NewShipsInfos()
group1.Set(SMALLCARGO, 1)
group2 = NewShipsInfos()
group2.Set(SMALLCARGO, 2)
a = NewACS(planet.GetID(), NewCoordinate(1, 2, 3, PLANET_TYPE))
a.AddGroup(0, HUNDRED_PERCENT, *group1)
a.AddGroup(10, HUNDRED_PERCENT, *group2)
Print(a.Execute())

planet = GetCachedPlanets()[0]
group1 = NewShipsInfos()
group1.Set(SMALLCARGO, 1)
group2 = NewShipsInfos()
group2.Set(SMALLCARGO, 2)
a = NewACS(planet.GetID(), NewCoordinate(1, 2, 3, PLANET_TYPE))
a.AddGroup(0, HUNDRED_PERCENT, *group1)
a.AddGroup(10, HUNDRED_PERCENT, *group2)
Print(a.ExecuteAsync())
```

### CreateUnion
```go
// CreateUnion creates a union
// Second parameter is an array of player names
CreateUnion(Fleet, []string) (int64, error)
```

```go
f = NewFleet().SetOrigin("M:1:2:3").SetDestination("M:1:2:4").SetMission(PARK).SetAllShips()
fleet, _ = f.SendNow()
unionID, _ = CreateUnion(fleet, [])
Print("Union ID: " + unionID)
```

### Notify
```go
// Display a desktop notification
Notify(title, message string)
```
```go
Notify("Player online", "Player 'target' was seen online")
```

### PlaySound
```go
// Play a sound
// soundID is a value between 1 and 7.
// volume must be between 0.0 and 1.0
PlaySound(soundID int64, volume float64) error
```
```go
PlaySound(1, 1.0)
```

### GetEspionageReportMessages
```go
// Gets the summary of each espionage reports
GetEspionageReportMessages() ([]EspionageReportSummary, error)
```

### GetEspionageReportFor
```go
// Gets the latest espionage report for a given coordinate
GetEspionageReportFor(Coordinate) (EspionageReport, error)
```

### GetEspionageReport
```go
// Gets a detailed espionage report
GetEspionageReport(msgID int64) (EspionageReport, error)
```

### GetCombatReportSummaryFor
```go
// Gets the latest combat report for a given coordinate
GetCombatReportSummaryFor(Coordinate) (CombatReportSummary, error)
```

### DeleteMessage
```go
// Deletes a message from the mail box
DeleteMessage(msgID int64) error
```

### DeleteAllMessagesFromTab
```go
// Deletes all messages from a tab in the mail box
// Espionage: 20, Combat Reports: 21, Expeditions: 22, Unions/Transport: 23, Other: 24
DeleteAllMessagesFromTab(tabID int64) error
```

### Build
```go
// Builds any ogame objects (building, technology, ship, defence)
Build(celestialID CelestialID, id ID, nbr int64) error
```
```go
celestial = GetCachedCelestial("1:2:3")
Build(celestial.GetID(), LIGHTFIGHTER, 5)     // Build 5 LightFighter
Build(celestial.GetID(), LIGHTLASER, 5)       // Build 5 LightLaser
Build(celestial.GetID(), METALMINE, 0)        // Build next MetalMine
Build(celestial.GetID(), ENERGYTECHNOLOGY, 0) // Build next EnergyTechnology

// This also work
celestial.Build(LIGHTFIGHTER, 5)     // Build 5 LightFighter
celestial.Build(LIGHTLASER, 5)       // Build 5 LightLaser
celestial.Build(METALMINE, 0)        // Build next MetalMine
celestial.Build(ENERGYTECHNOLOGY, 0) // Build next EnergyTechnology
```

### BuildBuilding
```go
// BuildBuilding ensure what is being built is a building
BuildBuilding(celestialID CelestialID, buildingID ID) error
```
```go
celestial = GetCachedCelestial("1:2:3")
BuildBuilding(celestial.GetID(), METALMINE) // Build next MetalMine

// This also work
celestial.BuildBuilding(METALMINE) // Build next MetalMine
```

### BuildTechnology
```go
// BuildTechnology ensure that we're trying to build a technology
BuildTechnology(celestialID CelestialID, technologyID ID) error
```
```go
celestial = GetCachedCelestial("1:2:3")
BuildTechnology(celestial.GetID(), ENERGYTECHNOLOGY) // Build next energy technology

// This also work
celestial.BuildTechnology(ENERGYTECHNOLOGY) // Build next energy technology
```

### TearDown
```go
// TearDown tears down any ogame building
TearDown(celestialID CelestialID, buildingID ID) error
```
```go
celestial = GetCachedCelestial("1:2:3")
TearDown(celestial.GetID(), METALMINE)

// This also work
celestial.TearDown(METALMINE)
```

### CancelBuilding
```go
// CancelBuilding cancel the construction of a building on a specified planet
CancelBuilding(CelestialID) error
```
```go
celestial = GetCachedCelestial("1:2:3")
CancelBuilding(celestial.GetID())

// This also work
celestial.CancelBuilding()
```

### CancelLfBuilding
```go
// CancelLfBuilding cancel the construction of a lifeform building on a specified planet
CancelLfBuilding(CelestialID) error
```
```go
celestial = GetCachedCelestial("1:2:3")
CancelLfBuilding(celestial.GetID())

// This also work
celestial.CancelLfBuilding()
```

### CancelResearch
```go
// CancelResearch cancel the research
CancelResearch(CelestialID) error
```
```go
celestial = GetCachedCelestial("1:2:3")
CancelResearch(celestial.GetID())

// This also work
celestial.CancelResearch()
```

### CancelFleet
```go
// CancelFleet cancels a fleet
CancelFleet(FleetID) error
```
```go
fleets, slots = GetFleets()
CancelFleet(fleets[0].ID)

fleets, slots = GetFleets()
for fleet in fleets {
    CancelFleet(fleet.ID)
}
```

### GetLfBonuses

```go
// GetLfBonuses get the lifeform bonuses from ogame
GetLfBonuses() (ogame.LfBonuses, error)
```

### GetCachedLfBonuses

```go
// GetCachedLfBonuses get the last lifeform bonuses known
GetCachedLfBonuses() (ogame.LfBonuses, error)
```

### TechnologyDetails
```go
// TechnologyDetails extract details from ajax window when clicking supplies/facilities/techs/lf...
TechnologyDetails(CelestialID, ID) (TechnologyDetails, error)
```

```go
celestial = GetCachedCelestial("1:2:3")
details, err = TechnologyDetails(celestial.GetID(), METALMINE)
Print(details)
```

### GetResourcesBuildings
```go
// GetResourcesBuildings gets the resources buildings levels
GetResourcesBuildings(celestialID CelestialID) (ResourcesBuildings, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
buildings, err = GetResourcesBuildings(celestial.GetID())

// This also work
buildings, err = celestial.GetResourcesBuildings()
```

### GetTechs
```go
// GetTechs will return all information with a single call to ogame
GetTechs(celestialID CelestialID) (ResourcesBuildings, Facilities, ShipsInfos, DefensesInfos, Researches, error)
```

### GetTechs2
```go
// GetTechs will return all information with a single call to ogame
GetTechs2(celestialID CelestialID) (ResourcesBuildings, Facilities, ShipsInfos, DefensesInfos, Researches, LfBuildings, LfResearches, error)
```

### GetFacilities
```go
// GetFacilities gets all facilities information of a planet
GetFacilities(CelestialID) (Facilities, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
facilities = GetFacilities(celestial.GetID())

// This also work
facilities = celestial.GetFacilities()
```

### GetLfBuildings
```go
// GetLfBuildings gets lifeform buildings information of a planet
GetLfBuildings(CelestialID) (LfBuildings, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
lfBuildings = GetLfBuildings(celestial.GetID())

// This also work
lfBuildings = celestial.GetLfBuildings()
```

### GetResearch
```go
// GetResearch gets the player researches information
GetResearch() Researches
```
```go
researches = GetResearch()
Print(researches)
```

### GetLfResearch
```go
// GetLfResearch gets the player lifeform researches information
GetLfResearch(ogame.CelestialID) (LfResearches, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
researches, _ = GetLfResearch(celestial.GetID())
Print(researches)
```

### GetShips
```go
// GetShips gets all ships units information of a planet
GetShips(CelestialID) (ShipsInfos, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
ships, _ = GetShips(celestial.GetID())
Print(ships.LightFighter)

// This also work
ships, _ = celestial.GetShips()
```

### GetDefense
```go
// GetDefense gets all the defenses units information of a planet
GetDefense(CelestialID) (DefensesInfos, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
defenses, _ = GetDefense(celestial.GetID())

// This also work
defenses, _ = celestial.GetDefense()
```

### GetProduction
```go
// GetProduction get what is in the production queue.
// (ships & defense being built)
// Returns a list of "quantifiable", the total time remaining before the queue is done, and an error if any
GetProduction(CelestialID) ([]Quantifiable, int64, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
productionLine, _, _ = GetProduction(celestial.GetID())

// This also work
productionLine, _, _ = celestial.GetProduction()
```

### ConstructionsBeingBuilt

```go
// ConstructionsBeingBuilt returns the building & research being built, and the time remaining (secs)
ConstructionsBeingBuilt(CelestialID) (buildingID ID, buildingCountdown int64, researchID ID, researchCountdown int64)
```
```go
celestial = GetCachedCelestial("1:2:3")
buildingID, buildingCountdown, researchID, researchCountdown = ConstructionsBeingBuilt(celestial.GetID())
```

### ConstructionsBeingBuiltLf

```go
// ConstructionsBeingBuiltLf returns the building & research being built, and the time remaining (secs)
ConstructionsBeingBuiltLf(CelestialID) (buildingID ID, buildingCountdown int64, researchID ID, researchCountdown int64, lfBuildingID ID, lfBuildingCountdown int64, lfResearchID ID, lfResearchCountdown int64)
```
```go
celestial = GetCachedCelestial("1:2:3")
buildingID, buildingCountdown, researchID, researchCountdown, lfBuildingID, lfBuildingCountdown, lfResearchID, lfResearchCountdown = ConstructionsBeingBuiltLf(celestial.GetID())
```

### GetResourceSettings
```go
// GetResourceSettings gets the resources settings for specified planetID
GetResourceSettings(PlanetID) (ResourceSettings, error)
```

### SetResourceSettings
```go
// SetResourceSettings set the resources settings on a planet
SetResourceSettings(PlanetID, ResourceSettings) error
```
```go
celestial = GetCachedCelestial("1:2:3")
settings = NewResourceSettings(100, 100, 80, 100, 10, 0, 0)
SetResourceSettings(celestial.GetID(), settings)
```

### GetSlots
```go
// GetSlots gets the player current and total slots information
GetSlots() Slots
```
```go
slots = GetSlots()
Print("Slots in use:", slots.InUse)
Print("Slots total:", slots.Total)
Print("Expedition slots in use:", slots.ExpInUse)
Print("Expedition slots total:", slots.ExpTotal)
```

### Distance
```go
// Distance return distance between two coordinates
Distance(origin, destination Coordinate) int64
```
```go
celestial = GetCachedCelestial("1:2:3")
coord1 = NewCoordinate(1, 2, 4, PLANET_TYPE)
dist = Distance(celestial.GetCoordinate(), coord1)
Print("Distance:", dist)
```

### FlightTime
```go
// FlightTime calculate flight time and fuel needed
FlightTime(origin, destination Coordinate, speed Speed, ships ShipsInfos, mission MissionID) (secs, fuel int64)
```
```go
celestial = GetCachedCelestial("1:2:3")
ships, _ = celestial.GetShips()
coord1 = NewCoordinate(1, 2, 4, PLANET_TYPE)
secs, fuel = FlightTime(celestial.GetCoordinate(), coord1, HUNDRED_PERCENT, ships, TRANSPORT)
Print("Time:", ShortDur(secs))
Print("Fuel:", Dotify(fuel))
```
```go
origin = NewCoordinate(1, 2, 3, PLANET_TYPE)
coord1 = NewCoordinate(1, 2, 4, PLANET_TYPE)
ships = NewShipsInfos()
ships.Set(RECYCLER, 20)
secs, fuel = FlightTime(origin, coord1, HUNDRED_PERCENT, *ships, PARK)
Print("Time:", ShortDur(secs))
Print("Fuel:", Dotify(fuel))
```

### DestroyRockets
```go
// DestroyRockets destroys anti-ballistic & inter-planetary missiles
DestroyRockets(planetID PlanetID, abm, ipm int64) error
```
```go
celestial = GetCachedCelestial("1:2:3")
DestroyRockets(celestial.GetID(), 1, 2) // Destroy 1 ABM and 2 IPM
```

### SendIPM
```go
// SendIPM sends IPM
SendIPM(PlanetID, Coordinate, int64, ID) (int64, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
target = NewCoordinate(1, 1, 1, PLANET_TYPE)
SendIPM(celestial.GetID(), target, 10, 0) // Send 10 IPM to target, (0 is random primary defense target)
SendIPM(celestial.GetID(), target, 10, LIGHTLASER) // Send 10 IPM to target, targetting light laser
```

### BuyOfferOfTheDay
```go
// BuyOfferOfTheDay buys the offer of the day.
BuyOfferOfTheDay() error
```
```go
Print(BuyOfferOfTheDay())
```

### IsVacationModeEnabled
```go
// IsVacationModeEnabled returns either or not the bot is in vacation mode
IsVacationModeEnabled() bool
```

### SetVacationMode
```go
// SetVacationMode puts the account in vacation mode
SetVacationMode() error
```

### GetAuction
```go
// GetAuction gets current auction information
GetAuction() (Auction, error)
```

### DoAuction
```go
// DoAuction make a bid on current auction
DoAuction(bid map[CelestialID]Resources) error
```
```go
celestial = GetCachedCelestials()[0]
bid = {
    celestial.GetID(): NewResources(2000, 0, 0)
}
Print(DoAuction(bid))
```

### Phalanx
```go
// Phalanx scan a coordinate from a moon to get fleets information
Phalanx(MoonID, Coordinate) ([]Fleet, error)
```

### UnsafePhalanx
```go
// UnsafePhalanx same as Phalanx but does not perform any input validation.
// Warning: scanning an invalid coordinate (out of phalanx range) might get your account banned automatically. (10min ban for first time)
UnsafePhalanx(MoonID, Coordinate) ([]Fleet, error)
```

### JumpGate
```go
// JumpGate sends ships through a jump gate.
JumpGate(origin, dest MoonID, ships ShipsInfos) error
```
```go
moon1 = GetCachedCelestial("M:1:2:3")
moon2 = GetCachedCelestial("M:1:2:4")
ships = NewShipsInfos()
ships.Set(RECYCLER, 20)
err = JumpGate(moon1.GetID(), moon2.GetID(), *ships)
Print(err)
```

### JumpGate2
```go
// JumpGate2 sends ships through a jump gate.
JumpGate2(origin, dest MoonID, ships ShipsInfos) (success bool, rechargeTime int64, err error)
```

### JumpGateDestinations
```go
// JumpGateDestinations returns available destinations for jump gate.
JumpGateDestinations(origin MoonID) (destinations []MoonID, rechargeTime int64, err error)
```

### GetFleetSlotsReserved
```go
// Returns current fleet slots reserved for player setting
GetFleetSlotsReserved() int64
```
```go
reserved = GetFleetSlotsReserved()
Print(reserved)
```

### SetFleetSlotsReserved
```go
// Set fleet slots reserved for player setting
SetFleetSlotsReserved(reserved int64)
```

### NewResources
```go
// Represent ogame resources
NewResources(metal, crystal, deuterium int64) Resources
```

### NewResourceSettings
```go
// Represent an ogame ResourceSettings
NewResourceSettings(metalMine, crystalMine, deuteriumSynthesizer, solarPlant, fusionReactor, solarSatellite, crawler int64) ResourceSettings
```

### NewCoordinate
```go
// Represent an ogame coordinate
NewCoordinate(galaxy, system, position int64, celestialType CelestialType) Coordinate
```
```go
myCoordinate = NewCoordinate(1, 2, 3, PLANET_TYPE)
Print(myCoordinate)
```

### NewTemperature
```go
// Represent an ogame temperature
NewTemperature(min, max int64) Temperature
```

### NewShipsInfos
```go
// Represent an ogame ShipsInfos
NewShipsInfos() *ShipsInfos
```

### SendSavedFleet
```go
SendSavedFleet(int64) (Fleet, error)
```

```go
fleet, err = SendSavedFleet(1)
```

### CoordinatesAvailableForDiscoveryFleet

```go
// origin is from which planet you want to look at the galaxy page.
// if origin is nil, the currently active planet will be used.
// origin can be anything that can be turned into a Celestial
CoordinatesAvailableForDiscoveryFleet(origin Celestial, galaxy, system int64) ([]Coordinate, error)
```

```go
coords, err = CoordinatesAvailableForDiscoveryFleet(nil, 1, 2)
```

### SendDiscoveryFleet

```go
// origin can be anything that can be turned into a Celestial
// destination can be anything that can be turned into a Coordinate
SendDiscoveryFleet(origin Celestial, destination Coordinate) error
```

```go
origin = GetCachedPlanets()[0]
destination = NewCoordinate(1, 2, 3, PLANET_TYPE)
Print(SendDiscoveryFleet(origin, destination))

// This also works
SendDiscoveryFleet("1:2:3", "4:5:6")
```

### ConvertIntoCoordinate

```go
// Turn any of the following into a Coordinate: string, Coordinate, Celestial, Planet, Moon, CelestialID, PlanetID, MoonID
// Returns an error if the input is invalid. Invalid type, or failed to parse a string into a coordinate.
ConvertIntoCoordinate(any) (Coordinate, error)
```

```go
coord, err = ConvertIntoCoordinate("1:2:3")

celestial = GetCachedPlanets()[0]
coord, err = ConvertIntoCoordinate(celestial)
coord, err = ConvertIntoCoordinate(celestial.GetID())
coord, err = ConvertIntoCoordinate(celestial.GetCoordinate())
```

### GetExpeditionsConfigs
```go
GetExpeditionsConfigs() ExpeditionsConfigs
```

```go
configs = GetExpeditionsConfigs()
celestial = GetCachedPlanets()[0]
configs.Sheets[0].Origin = celestial.GetID()
configs.Sheets[0].LightFighter = 12
SetExpeditionsConfigs(configs)
```

### SetExpeditionsConfigs
```go
SetExpeditionsConfigs(ExpeditionsConfigs)
```

### TempFile
```go
// Create a new temp file
// File can be downloaded at /temp-files/<filename>/download
TempFile(filename string) (*TempFile, error)
```
```go
f, _ = TempFile("test.txt")
f.Write("Line1\n")
f.Write("Line2\n")
f.Write("Line3\n")
f.Close()
```

### ListTempFiles
```go
// List all known temp files
ListTempFiles() []string
```

### DeleteTempFile
```go
// Delete a temp file
DeleteTempFile(filename string) error
```

### DeleteAllTempFiles
```go
// Delete all temp files
DeleteAllTempFiles()
```

### JsonDecode
```go
JsonDecode(data []byte) (out interface{}, err error)
```
```go
out, err = JsonDecode(`{"a": 1, "b": 2.1, "c": "a string", "d": true}`)
Print(out, err)
```

### Min
```go
// Min returns the smaller of x or y.
Min(x, y float64) float64
```

### Max
```go
// Max returns the larger of x or y.
Max(x, y float64) float64
```

### Ceil
```go
// Ceil returns the least integer value greater than or equal to x.
Ceil(x float64) float64
```

### Floor
```go
// Floor returns the greatest integer value less than or equal to x.
Floor(x float64) float64
```

### Round
```go
// Round returns the nearest integer, rounding half away from zero.
Round(x float64) float64
```

### Abs
```go
// Abs returns the absolute value of x.
Abs(x float64) float64
```

### Pow
```go
// Pow returns x**y, the base-x exponential of y.
Pow(x, y float64) float64
```

### Sqrt
```go
// Sqrt returns the square root of x.
Sqrt(x float64) float64
```

### Terminate
```go
// Terminate the script immediately
Terminate()
```