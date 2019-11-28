# Scripting Manual

# Intro

NJA Scripts are running in a supervised environment allowing to :

- have access to NJA API: NJA internal data structures and low-level functions
- stop/start/create scripts at any moment
- schedule tasks

For scripting <a href="https://github.com/mattn/anko">anko</a> Programming Language is used.

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

You can create a script with the exact name *!global.ank* any variable or function inside will be available from any of your scripts

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

## How to debug errors

### syntax error

If you receive a message that looks like:  
```go
22:20:11 ERROR [[9:24] syntax error]
```
It means that you have a syntax error at line **9**, character **24**.  

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

// Speed
TEN_PERCENT     = 1
TWENTY_PERCENT  = 2
THIRTY_PERCENT  = 3
FOURTY_PERCENT  = 4
FIFTY_PERCENT   = 5
SIXTY_PERCENT   = 6
SEVENTY_PERCENT = 7
EIGHTY_PERCENT  = 8
NINETY_PERCENT  = 9
HUNDRED_PERCENT = 10

// Celestial types
PLANET_TYPE = 1
DEBRIS_TYPE = 2
MOON_TYPE   = 3
```
```go
TELEGRAM_CHAT_ID = (alias to telegram chat id bot setting)
```
```go
OGAME_SERVER = (ogame server information)
```

## Internal Types

### ID
```go
// ID represent an ogame id
type ID int

// Methods
IsSet() bool
Int() int
String() string
IsFacility() bool
IsResourceBuilding() bool
IsBuilding() bool
IsTech() bool
IsDefense() bool
IsShip() bool
```

### Celestial
```go
// Celestial ...
type Celestial interface {
    GetID() CelestialID
    GetType() CelestialType
    GetName() string
    GetCoordinate() Coordinate
    GetFields() Fields
    GetResources() (Resources, error)
    GetResourcesDetails() (ResourcesDetails, error)
    GetFacilities() (Facilities, error)
    SendFleet([]Quantifiable, Speed, Coordinate, MissionID, Resources, int) (Fleet, error)
    EnsureFleet([]Quantifiable, Speed, Coordinate, MissionID, Resources, int) (Fleet, error)
    GetDefense() (DefensesInfos, error)
    GetShips() (ShipsInfos, error)
    BuildDefense(defenseID ID, nbr int) error
    ConstructionsBeingBuilt() (ID, int, ID, int)
    GetProduction() ([]Quantifiable, error)
    GetResourcesBuildings() (ResourcesBuildings, error)
    Build(id ID, nbr int) error
    BuildBuilding(buildingID ID) error
    BuildTechnology(technologyID ID) error
    CancelResearch() error
    CancelBuilding() error
}
```

### Planet

```go
// Planet ogame planet object
type Planet struct {
	Img         string
	ID          PlanetID
	Name        string
	Diameter    int
	Coordinate  Coordinate
	Fields      Fields
	Temperature Temperature
	Moon        *Moon
}

// Methods
// all methods of Celestial interface
BuildCancelable(id ID) error
GetResourceSettings() (ResourceSettings, error)
BuildShips(shipID ID, nbr int) error
GetResourcesProductions() (Resources, error)
FlightTime(destination Coordinate, speed Speed, ships ShipsInfos) (secs, fuel int)
SendIPM(planetID PlanetID, coord Coordinate, nbr int, priority ID) (int, error)
```

### Moon

```go
// Moon ogame moon object
type Moon struct {
	ID         MoonID
	Img        string
	Name       string
	Diameter   int
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
type CelestialType int
```
```go
PLANET_TYPE // 1
DEBRIS_TYPE // 2
MOON_TYPE   // 3
```

### EspionageReportType
```go
type EspionageReportType int
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
	Galaxy   int
	System   int
	Position int
	Type     CelestialType
}

// Methods
Equal(v Coordinate) bool
String() string
IsPlanet() bool
IsMoon() bool
IsDebris() bool
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
	Min int
	Max int
}

// Methods
Mean() int
```

### Resources
```go
type Resources struct {
	Metal      int
	Crystal    int
	Deuterium  int
	Energy     int
	Darkmatter int
}

// Methods
String() string
Total() int
Value() int
Sub(v Resources) Resources
Add(v Resources) Resources
Mul(scalar int) Resources
Div(price Resources) int
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
		Available         int
		StorageCapacity   int
		CurrentProduction int
	}
	Crystal struct {
		Available         int
		StorageCapacity   int
		CurrentProduction int
	}
	Deuterium struct {
		Available         int
		StorageCapacity   int
		CurrentProduction int
	}
	Energy struct {
		Available         int
		CurrentProduction int
		Consumption       int
	}
	Darkmatter struct {
		Available int
		Purchased int
		Found     int
	}
}

// Methods
Available() Resources
```

### Fleet

```go
// Fleet represent a player fleet information
type Fleet struct {
	Mission      MissionID
	ReturnFlight bool
	ID           FleetID
	Resources    Resources
	Origin       Coordinate
	Destination  Coordinate
	Ships        ShipsInfos
	ArriveIn     int
	BackIn       int
}
```

### ShipsInfos
```go
// ShipsInfos represent a planet ships information
type ShipsInfos struct {
	LightFighter   int
	HeavyFighter   int
	Cruiser        int
	Battleship     int
	Battlecruiser  int
	Bomber         int
	Destroyer      int
	Deathstar      int
	SmallCargo     int
	LargeCargo     int
	ColonyShip     int
	Recycler       int
	EspionageProbe int
	SolarSatellite int
	Crawler        int
	Reaper         int
	Pathfinder     int
}

// Methods
Cargo(techs Researches, bool probeRaids) int, bool // probeRaids determines if spys in your uni have cargo or not
Has(v ShipsInfos) bool
FleetValue() int
FleetCost() Resources
ByID(id ID) int
String() string
ToPtr() *ShipsInfos
Add(v ShipsInfos)
Set(id ID, val int)
```

### DefensesInfos
```go
// DefensesInfos represent a planet defenses information
type DefensesInfos struct {
	RocketLauncher         int
	LightLaser             int
	HeavyLaser             int
	GaussCannon            int
	IonCannon              int
	PlasmaTurret           int
	SmallShieldDome        int
	LargeShieldDome        int
	AntiBallisticMissiles  int
	InterplanetaryMissiles int
}

// Methods
String() string
ByID(id ID) int
Set(id ID, val int)
```

### AttackEvent
```go
// AttackEvent all information available about an enemy attack
type AttackEvent struct {
	MissionType  MissionID
	Origin       Coordinate
	Destination  Coordinate
	ArrivalTime  time.Time
	ArriveIn     int
	AttackerName string
	AttackerID   int
	Missiles     int
	Ships        *ShipsInfos
}

// Methods
String() string
```

### SystemInfos
```go
// SystemInfos planets information for a specific system
type SystemInfos struct {
	galaxy  int
	system  int
	planets [15]*PlanetInfos
}

// Methods
Galaxy() int
System() int
Position(idx int) *PlanetInfos
```


### PlanetInfos
```go
// PlanetInfos public information of a planet in the galaxy page
type PlanetInfos struct {
	ID              int
	Activity        int
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
		Metal           int
		Crystal         int
		RecyclersNeeded int
	}
	Moon   *MoonInfos
	Player struct {
		ID         int
		Name       string
		Rank       int
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
	ID     int
	Name   string
	Rank   int
	Member int
}
```

### MoonInfos
```go
// MoonInfos public information of a moon in the galaxy page
type MoonInfos struct {
	ID       int
	Diameter int
	Activity int
}
```

### Facilities
```go
// Facilities represent a planet facilities information
type Facilities struct {
	RoboticsFactory int
	Shipyard        int
	ResearchLab     int
	AllianceDepot   int
	MissileSilo     int
	NaniteFactory   int
	Terraformer     int
	SpaceDock       int
	LunarBase       int
	SensorPhalanx   int
	JumpGate        int
}

// Methods
ByID(id ID) int
String() string
```

### Researches
```go
// Researches represent player's researches
type Researches struct {
	EnergyTechnology             int
	LaserTechnology              int
	IonTechnology                int
	HyperspaceTechnology         int
	PlasmaTechnology             int
	CombustionDrive              int
	ImpulseDrive                 int
	HyperspaceDrive              int
	EspionageTechnology          int
	ComputerTechnology           int
	Astrophysics                 int
	IntergalacticResearchNetwork int
	GravitonTechnology           int
	WeaponsTechnology            int
	ShieldingTechnology          int
	ArmourTechnology             int
}

// Methods
ByID(id ID) int
String() string
```

### ResourcesBuildings
```go
// ResourcesBuildings represent a planet resource buildings
type ResourcesBuildings struct {
	MetalMine            int
	CrystalMine          int
	DeuteriumSynthesizer int
	SolarPlant           int
	FusionReactor        int
	SolarSatellite       int
	MetalStorage         int
	CrystalStorage       int
	DeuteriumTank        int
}

// Methods
ByID(id ID) int
String() string
```

### EspionageReportSummary
```go
// EspionageReportSummary summary of espionage report
type EspionageReportSummary struct {
	ID     int
	Type   EspionageReportType
	From   string
	Target Coordinate
}
```

### CombatReportSummary
```go
// CombatReportSummary summary of combat report
type CombatReportSummary struct {
	ID           int
	Origin       *Coordinate
	Destination  Coordinate
	AttackerName string
	DefenderName string
	Loot         int
	Metal        int
	Crystal      int
	Deuterium    int
	DebrisField  int
	CreatedAt    time.Time
}
```

### EspionageReport
```go
// EspionageReport detailed espionage report
type EspionageReport struct {
	Resources
	ID                           int
	Username                     string
	LastActivity                 int
	CounterEspionage             int
	APIKey                       string
	HasFleet                     bool
	HasDefenses                  bool
	HasBuildings                 bool
	HasResearches                bool
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
```

### Server
```go
// OGame server information
type Server struct {
	Language      string
	Number        int
	Name          string
	PlayerCount   int
	PlayersOnline int
	Opened        string
	StartDate     string
	EndDate       *string
	ServerClosed  int
	Prefered      int
	SignupClosed  int
	Settings      struct {
		AKS                      int
		FleetSpeed               int
		WreckField               int
		ServerLabel              string
		EconomySpeed             int
		PlanetFields             int
		UniverseSize             int // Nb of galaxies
		ServerCategory           string
		EspionageProbeRaids      int
		PremiumValidationGift    int
		DebrisFieldFactorShips   int
		DebrisFieldFactorDefence int
	}
}
```

### ResourceSettings
```go
// ResourceSettings represent a planet resource settings
type ResourceSettings struct {
	MetalMine            int
	CrystalMine          int
	DeuteriumSynthesizer int
	SolarPlant           int
	FusionReactor        int
	SolarSatellite       int
    Crawler              int
}

// Methods
String() string
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
SetAllResources() *FleetBuilder
SetMission(mission MissionID) *FleetBuilder
SetDuration(expeditiontime int) *FleetBuilder
SetUnionID(unionID int) *FleetBuilder
AddShips(id ID, nbr int) *FleetBuilder
SetShips(ships ShipsInfos) *FleetBuilder
SetAllShips() *FleetBuilder
SetRecallIn(secs int) *FleetBuilder
FlightTime() (secs, fuel int)
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
SetRange(galaxy, systemFrom, systemTo int) *FarmSessionBuilder
SetProbes(nbProbes int) *FarmSessionBuilder
SetAdditionalCargo(pctCargo int) *FarmSessionBuilder
SetMinimumResourcesToAttack(minimumResourcesToAttack int) *FarmSessionBuilder
SetMinimumDefensesToIgnore(minimumDefensesToIgnore int) *FarmSessionBuilder
SetMinimumStorageToIgnore(metal, crystal, deuterium int) *FarmSessionBuilder
SetMinimumPlayerRank(rank int) *FarmSessionBuilder
SetEspionageProbeRaids(enabled bool) *FarmSessionBuilder
SetFastAttacking(enabled bool) *FarmSessionBuilder
SetDeleteCombatReports(enabled bool) *FarmSessionBuilder
SetAttackFromNearestPlanet(enabled bool) *FarmSessionBuilder
SetAttackFromNearestMoon(enabled bool) *FarmSessionBuilder
SetFarmSpeed(speed ogame.Speed) *FarmSessionBuilder
SetPriorityRatio(metal, crystal, deuterium float64) *FarmSessionBuilder
BuildFarmSession() (*FarmSession, error)
```

### ACSFlightTime
```go
type ACSFlightTime struct {
	ArriveIn      int
	HumanArriveIn string
	ArriveAt      time.Time
	HumanArriveAt string
	Fuel          int
	Groups        []ACSFlightTimeGroup
}
```

### ACS
```go
type ACS struct {
}

// Methods
GetFlightTimes() ACSFlightTime
AddGroup(delaySecs int, speed Speed, ships ShipsInfos) *ACS
Execute()
ExecuteAsync()
GetUnionID() int
GetFleetIDs() []FleetID
```

### ChatMsg
```go
type ChatMsg struct {
	SenderID      int
	SenderName    string
	AssociationID int
	Text          string
	ID            int
	Date          int
}
```


### TelegramMessage
```go
type TelegramMessage struct {
	MessageID int
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
	Date int64
	Text string
}
```

## Channels (events)

### OnAttackCh
Triggered when a new attack is detected

### OnAttackDoneCh
Triggered once an attack is done

### OnAttackCancelledCh
Triggered when an attack is cancelled 

### OnQuitCh
Triggered when `Exit` is called
```go
ExecIn(2000, func() {
    Exit()
})
<-OnQuitCh
Print("Exiting")
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
OnChatMessageReceivedCh chan ogame.ChatMsg
```
```go
for {
    m = <-OnChatMessageReceivedCh
    Print("From: " + m.SenderName + ", msg: " + m.Text)
}
```

## Global API

### Sleep
```go
// Sleeps a defined number of milliseconds
Sleep(milliseconds int)
```
```go
Print("Before")
Sleep(5 * 60 * 1000) // Execution of the script will stop here for 5min
Print("After")
```

### Random
```go
// Random generates a number between min and max inclusively
Random(min, max int)
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

### AddItemToQueue
```go
// Add something into a planet/moon build queue
// The "nbr" parameter is only useful for ships and defenses.
AddItemToQueue(celestialID CelestialID, id ID, nbr int) error
```

```go
planet = GetCachedPlanets()[0]
err = AddItemToQueue(planet.GetID(), SOLARPLANT, 0)
Print(err)
err = AddItemToQueue(planet.GetID(), BOMBER, 1)
Print(err)
err = AddItemToQueue(planet.GetID(), ROCKETLAUNCHER, 2)
Print(err)
err = AddItemToQueue(planet.GetID(), BOMBER, 0)
Print(err)
```

### GetPrice
```go
// Returns the price of 'entity'. 'nbr' is either a building/research level or a defense/ship quantity.
GetPrice(entityID ogame.ID, nbr int) (ogame.Resources)
```
```go
price = GetPrice(LIGHTFIGHTER, 5)
Print("The price of 5 LF is ", price) // [15,000|5,000|0]

price = GetPrice(METALMINE, 3)
Print("The price of metal mine level 3 is ", price) // [135|33|0]
```

### SolarSatelliteProduction
```go
// Returns the energy production of solar satellite.
SolarSatelliteProduction(temp Temperature, nbr int) (energy int)
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
IgnorePlanet(int) error
```
```go
IgnorePlanet(1234567)
```

### IgnorePlayer
```go
// Adds a player to the avoid list
IgnorePlayer(int) error
```
```go
IgnorePlayer(1234567)
```

### IgnoreAlliance
```go
// Adds an alliance to the avoid list
IgnoreAlliance(int) error
```
```go
IgnoreAlliance(1234567)
```

### IsPlanetIgnored
```go
// True if planet id is in ignored list
IsPlanetIgnored(int) bool
```
```go
if IsPlanetIgnored(1234567) {
    Print("Planet "+1234567+" is in the list")
}
```

### IsPlayerIgnored
```go
// True if player id is in ignored list
IsPlayerIgnored(int) bool
```
```go
if IsPlayerIgnored(1234567) {
    Print("Player "+1234567+" is in the list")
}
```

### IsAllianceIgnored
```go
// True if alliance id is in ignored list
IsAllianceIgnored(int) bool
```
```go
if IsAllianceIgnored(1234567) {
    Print("Alliance "+1234567+" is in the list")
}
```

### ExecIn
```go
// Schedules an execution after 'execIn' milliseconds. Execution will be asynchronous!
ExecIn(milliseconds int, clb func()) context.CancelFunc
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

### IntervalExec
```go
// Schedules an execution on regular intervals
IntervalExec(milliseconds int, clb func()) context.CancelFunc
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
entryID = CronExec("0 43 15 * * *", callback)
Sleep(1000)
RemoveCron(entryID) // Remove scheduled cronjob 
```

### Clock
```go
// Return the current time
Clock() (hour, min, sec int)
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
Date() (year, month, day int)
```
```go
year, month, day = Date()
Print("Today's date is "+year+"-"+month+"-"+day)
```

### Weekday
```go
// Return the week day (Sunday = 0, Monday = 1... Saturday = 6)
Weekday() int
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

### GetHomeWorld
```go
GetHomeWorld() ogame.Celestial
```
```go
homeworld = GetHomeWorld()
Print(homeworld.GetName(), homeworld.GetID())
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
IsNJAEnabled() bool
```
```go
if IsNJAEnabled() {
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

### IsUnderAttack
```go
// Returns true if the user is under attack, false otherwise
IsUnderAttack() bool
```
```go
isUnderAttack = IsUnderAttack()
Print(isUnderAttack)
```

### SendMessage
```go
// Sends a message to playerID
SendMessage(playerID int, message string) error
```
```go
SendMessage(1234567, "Sup!")
```

### SendMessageAlliance
```go
// Sends a message to associationID
SendMessageAlliance(associationID int, message string) error
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

### Abandon
```go
// Abandon a planet
Abandon(interface{}) error
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

### GalaxyInfos
```go
// GalaxyInfos get information of all planets and moons of a solar system
GalaxyInfos(galaxy, system int) (SystemInfos, error)
```
```go
systemInfo, _ = GalaxyInfos(4, 116)
```

### Dotify
```go
// Pretty print a large number using dot as thousand separator
Dotify(in int) string
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

### ID2Str
```go
// Get the string representation of an ogame ID
ID2Str(id ogame.ID) string
```
```go
Print(ID2Str(204))       // LightFighter
Print(ID2Str(METALMINE)) // MetalMine
```

### Atoi
```go
// Convert a string to an integer
Atoi(string) int
```
```go
Print(1 + Atoi("2")) // 3
```

### Itoa
```go
// Convert a integer to a string
Itoa(int) string
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

### IsScriptRunning
```go
// Returns true if 'script' is running, otherwise false
IsScriptRunning(name string) bool
```
```go
Print(IsScriptRunning("another.ank"))
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
FarmingBotSessionsCount() int
```

### AbortAllFarmingSessions
```go
// Abort all farming sessions
AbortAllFarmingSessions()
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
// Set the defender check interval (in minutes)
SetDefenderCheckInterval(min, max int)
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

### Get
```go
// Get data from storage
Get(key string) (interface{}, error)
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
ParseCoord(str string) (coord ogame.Coordinate, err error)
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

### CalcCargo
```go
// Calculate how many small cargo OR how many large cargo you need to transport a total amount of resources
CalcCargo(resourcesTotal int) (largeCargoNeeded, smallCargoNeeded int)
```
```go
resources = NewResources(10000, 20000, 30000)
lc, sc = CalcCargo(resources.Total())
Print("You need either " + lc + " large cargo OR " + sc + " small cargo")
```

### CalcFastCargo
```go
// Calculate the best combo to transport a total amount of resources according to your available ships.
CalcFastCargo(lcAvail, scAvail, resourcesTotal int) (largeCargo, smallCargo, cargo int)
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

### GetPlayerCoordinates
```go
// Gets all the coordinates of a player from the local database
GetPlayerCoordinates(playerID int) []ogame.Coordinate
```

### Base64
```go
// Base64 encode a string 
Base64(string) string
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

### SendTelegram
```go
// Send a telegram notification
SendTelegram(chatID int64, msg string) error
```
```go
SendTelegram(1234567, "Attack detected")
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
PlaySound(soundID int, volume float64) error
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
GetEspionageReport(msgID int) (EspionageReport, error)
```

### GetCombatReportSummaryFor
```go
// Gets the latest combat report for a given coordinate
GetCombatReportSummaryFor(Coordinate) (CombatReportSummary, error)
```

### DeleteMessage
```go
// Deletes a message from the mail box
DeleteMessage(msgID int) error
```

### Build
```go
// Builds any ogame objects (building, technology, ship, defence)
Build(celestialID CelestialID, id ID, nbr int) error
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
BuildTechnology(celestial.GetID(), ENERGYTECHNOLOGY) // Build next MetalMine

// This also work
celestial.BuildTechnology(ENERGYTECHNOLOGY) // Build next MetalMine
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

### GetResourcesBuildings
```go
// GetResourcesBuildings gets the resources buildings levels
GetResourcesBuildings(celestialID CelestialID) (ResourcesBuildings, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
buildings = GetResourcesBuildings(celestial.GetID())

// This also work
buildings = celestial.GetResourcesBuildings()
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

### GetResearch
```go
// GetResearch gets the player researches information
GetResearch() Researches
```
```go
researches = GetResearch()
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
GetProduction(CelestialID) ([]Quantifiable, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
productionLine = GetProduction(celestial.GetID())

// This also work
productionLine = celestial.GetProduction()
```

### ConstructionsBeingBuilt

```go
// ConstructionsBeingBuilt returns the building & research being built, and the time remaining (secs)
ConstructionsBeingBuilt(CelestialID) (buildingID ID, buildingCountdown int, researchID ID, researchCountdown int)
```
```go
celestial = GetCachedCelestial("1:2:3")
buildingID, buildingCountdown, researchID, researchCountdown = ConstructionsBeingBuilt(celestial.GetID())

// This also work
buildingID, buildingCountdown, researchID, researchCountdown = celestial.ConstructionsBeingBuilt()
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
settings = NewResourceSettings(100, 100, 80, 100, 10, 0)
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
Distance(origin, destination Coordinate) int
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
FlightTime(origin, destination Coordinate, speed Speed, ships ShipsInfos) (secs, fuel int)
```
```go
celestial = GetCachedCelestial("1:2:3")
ships, _ = celestial.GetShips()
coord1 = NewCoordinate(1, 2, 4, PLANET_TYPE)
secs, fuel = FlightTime(celestial.GetCoordinate(), coord1, HUNDRED_PERCENT, ships)
Print("Time:", ShortDur(secs))
Print("Fuel:", Dotify(fuel))
```
```go
origin = NewCoordinate(1, 2, 3, PLANET_TYPE)
coord1 = NewCoordinate(1, 2, 4, PLANET_TYPE)
ships = NewShipsInfos()
ships.Set(RECYCLER, 20)
secs, fuel = FlightTime(origin, coord1, HUNDRED_PERCENT, *ships)
Print("Time:", ShortDur(secs))
Print("Fuel:", Dotify(fuel))
```

### SendIPM
```go
// SendIPM sends IPM
SendIPM(PlanetID, Coordinate, int, ID) (int, error)
```
```go
celestial = GetCachedCelestial("1:2:3")
target = NewCoordinate(1, 1, 1, PLANET_TYPE)
SendIPM(celestial.GetID(), target, 10, 0) // Send 10 IPM to target, (0 is random primary target)
SendIPM(celestial.GetID(), target, 10, LIGHTLASER) // Send 10 IPM to target targetting light laser
```

### BuyOfferOfTheDay
```go
// BuyOfferOfTheDay buys the offer of the day.
BuyOfferOfTheDay() error
```
```go
Print(BuyOfferOfTheDay())
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

### GetFleetSlotsReserved
```go
// Returns current fleet slots reserved for player setting
GetFleetSlotsReserved() int
```
```go
reserved = GetFleetSlotsReserved()
Print(reserved)
```

### SetFleetSlotsReserved
```go
// Set fleet slots reserved for player setting
SetFleetSlotsReserved(reserved int)
```

### NewResources
```go
// Represent ogame resources
NewResources(metal, crystal, deuterium int) Resources
```

### NewResourceSettings
```go
// Represent an ogame ResourceSettings
NewResourceSettings(metalMine, crystalMine, deuteriumSynthesizer, solarPlant, fusionReactor, solarSatellite int) ResourceSettings
```

### NewCoordinate
```go
// Represent an ogame coordinate
NewCoordinate(galaxy, system, position int, celestialType CelestialType) Coordinate
```
```go
myCoordinate = NewCoordinate(1, 2, 3, PLANET_TYPE)
Print(myCoordinate)
```

### NewTemperature
```go
// Represent an ogame temperature
NewTemperature(min, max int) Temperature
```

### NewShipsInfos
```go
// Represent an ogame ShipsInfos
NewShipsInfos() *ShipsInfos
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
