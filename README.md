# Populace-API
API Documentation for the upcoming Populace plugin

## Permissions
Permissions will dynamically control the commands that users have access to, as well as alter the in-game GUIs to suite your choices.

#### populace.commands.allow
Grants access to **/allow**, for adding friends to plots.

#### populace.commands.claim
Grants access to **/claim**, for claiming new land

#### populace.commands.claim.radius
Grants access to **/claim [#]**, for lots of new land at once

#### populace.commands.forsale
Grants access to **/forsale**, for selling plots to residents

#### populace.commands.giveplot
Grants access to **/giveplot**, for allowing town staff to give plot ownership to residents

#### populace.commands.invite
Grants access to **/invite**, for inviting new residents to town

#### populace.commands.jail
Grants access to **/jail**, for allowing town staff to jail misbehaving residents

#### populace.commands.join
Grants access to **/join**, for joining towns

#### populace.commands.map
Grants access to **/map**, for spawning maps of their town

#### populace.commands.newtown
Grants access to **/newtown**, for creating towns

#### populace.commands.notforsale
Grants access to **/notforsale**, for taking plots off the market

#### populace.commands.plot
Grants access to **/plot**, for changing plot settings

#### populace.save
Grants access to **/populace save**, for saving Populace Core's data

#### populace.commands.resident
Grants access to **/resident**, for viewing resident info

#### populace.commands.resident.viewplots
Grants access to seeing the plots that residents own when viewing their profile

#### populace.commands.setjail
Grants access to **/setjail**, for allowing town mayors to set their town's jail

#### populace.commands.town
Grants access to **/town**, for allowing viewing town details and changing settings

#### populace.commands.towns
Grants access to **/towns**, for showing all towns on the server

#### populace.commands.unclaim
Grants access to **/unclaim**, for unclaiming land

#### populace.commands.unjail
Grants access to **/unjail**, for allowing town staff to free jailed residents

#### populace.commands.visit
Grants access to **/visit**, for visiting town spawn

#### populace.commands.visit.other
Grants access to **/visit [Town]**, for visiting other towns

#### populace.commands.visualize
Grants access to **/visualize**, for requesting a visualiztion of a chunk whenever

#### populace.plottypes.residential
Grants access to making Residential plots.

#### populace.plottypes.merchant
Grants access to making Merchant plots. (If PopulaceMarket is installed.)

#### populace.plottypes.battle
Grants access to making Battle plots.

#### populace.plottypes.outpost
Grants access to making Outpost plots. (If a town has reached the City level.)

#### populace.bypass
Bypasses all land and town permission checks

#### populace.teleportation.nowarmup
There is no warmup time when teleporting

#### populace.teleportation.nocooldown
There is no cooldown time until the next teleport

## Message Formatting
To keep messages consistent, please use the **Msg** static class to prefix your messages you send to the player.

* **Msg.OK** A lime green message
* **Msg.INFO** An aqua message
* **Msg.WARN** A gold message
* **Msg.ERR** A red message

## Definitions

* **Resident** A player within Populace
* **Plot** A chunk of claimed land
* **Town** A town within populace

## Working with Residents
All resident data can be acquired from the **ResidentManager**.

**By Name**
```java 
Resident resident = ResidentManager.getResident("PlayerName"); 
```
**By UUID**
```java 
Resident resident = ResidentManager.getResident(UUID.randomUUID()); 
```
**By Online Player**
```java 
Resident resident = ResidentManager.getResident(player); 
```

All will return null if no data matches the provided query.

## Working with Plots
Populace stores locations of Plots using **PlotChunk**s for performance reasons. A Plot Chunk contains a chunk's world, X coordinate, and Z coordinate.

All plot data can be acquired from the **PlotManager**.

**By PlotChunk**
```java 
World world = Bukkit.getWorld("world");
Plot plot = PlotManager.getPlot(new PlotChunk(world, 0, 0));
```

**By Chunk**
```java 
World world = Bukkit.getWorld("world");
Chunk chunk = world.getChunkAt(0, 0);
Plot plot = PlotManager.getPlot(chunk);
```

**By Raw Coordinates**
```java 
World world = Bukkit.getWorld("world");
Plot plot = PlotManager.getPlot(world, 0, 0);
```

All will return null if the location hasn't been claimed.

#### Getting a Town by Location
```java 
Player player = // my player
Location location = player.getLocation();
Chunk chunk = location.getChunk();
Plot plot = PlotManager.getPlot(chunk);
if (plot != null) { // Make sure this land is claimed
  Town town = plot.getTown();
  // More code
}
else{
  // It's wilderness
}
```

## Events
Events are fired by bukkit when certain actions are performed within Populace. Important Populace then you can listen for any of these events like normal bukkit events.

### PopulaceNewDayEvent

Fired when a new day begins in Populace. This is when resident taxes and daily upkeep from towns is collected. Residents that can't pay their taxes are kicked from the town. Towns that can't pay their upkeep are destroyed.

### ResidentJoinTownEvent
**Cancellable** Yes, prevents the resident from joining the town. Sending feedback to the resident on why their join was cancelled recommended.

**Available Data**
* Town the resident would be joining (getTown())
* The resident who would be joining the town (getResident())
* The resident who sent the invite (null if no invite was sent, for public joins) (getInviteSource())

Fired when a resident joins a town by invitation or if the town is public.

### ResidentKickedFromTownEvent

**Available Data**
* The town the resident is being kicked from (getTown())
* The resident who is being kicked from town (getResident())
* The resident who is kicking the resident (null if kicked by the server) (getKicker())

Fired if a resident is kicked from town for any reason

### ResidentLeaveTownEvent
**Cancellable** Yes, prevents the user from leaving town

**Available Data**
* The town the resident would be leaving (getTown())
* The resident who is leaving town (getResident())
* The KickEvent that is causing this user to leave (getKickEvent()) (null if the user isn't kicked, and leaving on their own accord)

Fired when a resident is removed from a town for any reason

### ResidentPlotEnterEvent
**Cancellable** Yes, prevents the user from entering the plot. This will also send the visualizer and the "you can't enter this area" message, as if the user didn't have permission to enter the plot.

**Available Data**
* The plot the resident is attempting to enter (getPlot())
* The resident who is attempting the entry (getResident())

Fired when a resident attempts to walk into claimed land

### ResidentPlotLeaveEvent

**Available Data**
* The plot the resident is leaving (getPlot())
* The resident who is leaving (getResident())

Fired whn a resident walks into wilderness, or into a different plot

### TownBonusBlocksChangedEvent
**Cancellable** Yes, prevents the transaction from happening

**Available Data**
* The town making the bonus block transaction (getTown())
* The resident who is making the transaction on behalf of the town (getResident())
* The transaction type (BUY or SELL) (getAction())

Fired whenever the mayor attempts to buy or sell bonus land for their town

### TownBroadcastEvent
**Cancellable** Yes, the broadcast will not be sent.

**Available Data**
* The town that is being broadcast to (getTown())
* The town rank that will see the broadcast (getRank())
* The message that would be broadcasted (getMessage())

**Modifable Data**
* The message being broadcasted (setMessage(String newmsg))
* The rank who will see the broadcast (setRank(TownRank newrank))

Fired whenever a broadcast is sent to a town

### TownClaimLandEvent
**Cancellable** Yes, prevents the land from being claimed.

**Available Data**
* The town purchasing the land (getTown())
* The resident who is purchasing the land on behalf of the town (getResident())
* The plot that would be purchased for the town (getPlot())

Fired whenever a town claim land (this event is not fired for a player /claim'ing a plot already within town)

### TownCreationEvent

**Available Data**
* The town being created (getTown())
* The mayor of the town (getMayor())

Fired whenever a new town is created

### TownDestructionEvent

**Available Data**
* The town being destroyed (getTown())
* Whether or not the destruction was forced by the server or requested by the mayor (isForced())

Fired whenever a town is destroyed

### TownSpawnSetEvent
**Cancellable** Yes, prevents the spawn from being set to the new location

**Available Data**
* The town setting the spawn (getTown())
* The resident setting the spawn on behalf of the town (getResident())
* The location of the new spawn point (getLocation())

This event is fired when the town spawn is updated. **Important:** This event is NOT fired when the spawn is set automatically by Populace. (ie, on first land claim)

### TownUnclaimLandEvent
**Cancellable** Yes, prevents the land from being unclaimed. (Unless it's during town destruction, in which case the cancelled state is ignored.)

**Available Data**
* The town unclaiming the land (getTown())
* The resident who is unclaiming the land on behalf of the town (getResident())
* The plot that would be unclaimed (getPlot())

Fired whenever a town attempts to unclaim land (making it wilderness) **Important:** This event is fired for every plot in a town when the town is destroyed, HOWEVER: setting it to cancelled during this will be ignored. Town destruction can't be cancelled.
