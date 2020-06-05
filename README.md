# Valkyrie Descent Campaign Template
This is a campaign template for creating [Descent: Journeys in the Dark (Second Edition)](https://descent2e.fandom.com) campaigns in the [Valkyrie](https://github.com/NPBruce/valkyrie/wiki) mission creation tool.

A lot of the campaign logic is based on the [Community Shadow Rune Project](https://descent-community.org/index.php/fan-projects-fan-content/the-shadow-rune-project/). A big thank you to the creators of the campaign.

If you only want to create single missions without any campaign overhead check out the [Valkyrie Descent Mission Template](https://github.com/Quantumrunner/ValkyrieDescentMissionTemplate) project.

## Table of contents
- [Valkyrie Descent Campaign Template](#valkyrie-descent-campaign-template)
  - [Table of contents](#table-of-contents)
  - [Template Content](#template-content)
  - [How to use this Template](#how-to-use-this-template)
    - [Template Basics](#template-basics)
      - [eventssettings.ini](#eventssettingsini)
        - [Fame](#fame)
        - [Gold, XP and restock](#gold-xp-and-restock)
        - [Number of quests](#number-of-quests)
          - [Quest logic](#quest-logic)
        - [Campaign phase start](#campaign-phase-start)
        - [Transition](#transition)
      - [Images](#images)
          - [Campaign Map Image](#campaign-map-image)
      - [Mission rewards](#mission-rewards)
    - [Template Advanced Logic](#template-advanced-logic)
      - [Changing travel events](#changing-travel-events)
      - [Adding more missions](#adding-more-missions)
      - [Act 1 and Act 2 quests](#act-1-and-act-2-quests)
      - [Changing shop logic](#changing-shop-logic)
  - [Translations](#translations)

## Template Content
The template contains logic for creating a Descent: Journeys in the Dark (Second Edition) campaign with 6 (or less) missions with the following content:

- A campaign start screen (for flavor text and images)
- A campaign phase whith travel and city events
- 6 pre defined mission templates (including a finale)
- An campaign end (for flavor text and images)
- English and german texts for all events

## How to use this Template

There are two different approaches to use this template:

1. If you want to concentrate on creating missions content for a campaign everything is ready to go. In this case you only need to set some start parameters (see the [Template Basics](#template-basics) section below).
2. If you want to change parts of the template (e.g. Travel events, Shop Items etc.) the [Template Advanced Logic](#template-advanced-logic) describes the parts of the template you have to edit.

### Template Basics
This section describes which settings you need to change to make your campaign ready.

#### eventssettings.ini
All important settings for campain can be set in the `eventssettings.ini` file:

##### Fame
Valkyrie uses a system called <i>fame</i> to determine which items appear in the shop menus. There are different fame levels.

Every descent item in Valkyrie has a minimum and maximum fame level. The following fame levels exist:

1. insignifent (default level)
2. impressive
3. celebrated
4. heroic
5. legendary

See the following example for details:

```
[ItemCrossbow]
name={ffg:ITEM_CROSSBOW}
image="{import}/img/Item_Crossbow"
traits=ranged one_handed bow exotic act1
minfame=impressive
maxfame=celebrated
price=175
```

This item only appears if the heroes have reached the fame level `impressive` and will not appear anymore if the have reached higher level than `celebrated`. Therefore the heroes would not to have at least 2 and maximum 12 fame to see this item in the shop.

The `[EventInitializeCampaignFame]`-event contains variables which you need to set to make better items appear in the shop menu:

`operations=$%fameimpressive,=,2 $%famecelebrated,=,12 $%fameheroic,=,22 $%famelegendary,=,32`

This example values mean that the hero need to have at least **2** fame to reach the **impressive** level, **12** fame to reach the **celebrated** level etc..

Furthermore the event contains the `$%fame` variable you which you need to set for the start fame value.

##### Gold, XP and restock
The next important event is the `[EventInitializeGoldRestockAndAct]`-event. In this event you have to set the start gold (`$%gold`) and xp (`$%xp`) for the heroes. Furthermore you can set how often the heroes are able to restock (get new random items in the shop without waiting for the next mission, `%RestockAmount`). On top of that you can set the start act (only change this if you already want the heroes to start in act 2 with stronger monster, `%CurrentAct`).

##### Number of quests
In the `[EventInitializeNumberOfQuests]`-event you can set how many missions your campaign contains (`%NumberOfQuests`). If your campaign contains less than 6 missions you can delete the missions folders you don't need (Folders `02_Quest-05_Quest`).

###### Quest logic
For details on the single quests and the finale (`01_Quest`-`06_Finale`) check the [Valkyrie Descent Mission Template](https://github.com/Quantumrunner/ValkyrieDescentMissionTemplate) project.

##### Campaign phase start
In the `[EventCampaignPhaseStartValue]` set the `%CampaignPhaseStart`-variable to 1 (use 0 to disable this) to start on the campaign map (e.g. because the heroes can go shopping first) instead of directly starting with the first mission.

##### Transition
If you want to show an event text that says that the will switch from act 1 to act 2 the variables in the `EventInitializeTransition]`-event. This event will also change the shop items so that only act 2 shop items appear.

To enable the transition set `%TransitionEnabled` to 1. Furthermore set the `%TransitionAfterQuest`-variable to 1-5 to let the text appear **after** the specified mission.

#### Images
The template contains two image which are used in the campaign start screen (`[UIbg]` in `ui.ini`) and the campaign end screen (`[UIImage]` in `\CampaignEnd\ui.ini`). You have to replace the image with a custom image.

If you don't want to use the images you have to delete the images, remove the ui objects (`[UIbg]` in `ui.ini` and `[UIImage]` in `\CampaignEnd\ui.ini` ) and remove their usage in the events that add them to the screen. To do this replace the following code:

Replace `[EventStart]` code in `events.ini` with the following code:

```
[EventStart]
display=false
buttons=1
event1=EventOutroWon EventOutroLost
add=UIBackground UIButtonEndCampaign UICredits
trigger=EventStart
```

and `[EventTitle]` code in `\CampaignEnd\events.ini` with the following code:

```
[EventTitle]
display=false
buttons=0
add=UIbg UIIntro UIStart
```

###### Campaign Map Image

Another image used in the template is the campaign map (`CP\map.jpg`). This image is directly placed in the campaign phase folder (`CP`). The reason for this is that the map image can be replaced per langauge. For this another image with the same filename needs to be placed in a subfolder with the language name (see german language example `CP\German\map.jpg`). This will automatically replace the image when switching the language.

If you don't want to use a map replace the event `[EventMap]` in `CP\events.ini` with the following code:

```
[EventMap]
display=false
buttons=1
event1=EventIcons
music=AudioWorldMap AudioWorldMap
```


#### Mission rewards
Each mission except for the finale contains rewards (e.g. Gold and XP) for the heroes. Check out the `[EventVictory]`- and `[EventDefeat]` events in the `event.ini` files in the `01_Quest`-`05_Quest` folders and change the variable set there to increase or decrease hero rewards.

### Template Advanced Logic
The following sections describe how to extend or replace the templates base logic. These steps are optional.

#### Changing travel events
Each quest currently has three travel steps. These travel steps are defined in the `CP\travel.ini` file. See the following example for details:

```
[EventTravelStep1Quest1]
operations=TravelStep1Quest1,=,1 TravelStepAmount,+,1
vartests=VarOperation:TravelStep1Quest1,==,0 VarTestsLogicalOperator:AND VarOperation:TravelStepAmount,==,0 
display=false
buttons=1
event1=EventTravelEventForest

[EventTravelStep2Quest1]
operations=TravelStep2Quest1,=,1 TravelStepAmount,+,1
vartests=VarOperation:TravelStep2Quest1,==,0 VarTestsLogicalOperator:AND VarOperation:TravelStepAmount,==,1 
display=false
buttons=1
event1=EventTravelEventWater

[EventTravelStep3Quest1]
operations=TravelStep3Quest1,=,1 TravelStepAmount,+,1
vartests=VarOperation:TravelStep3Quest1,==,0 VarTestsLogicalOperator:AND VarOperation:TravelStepAmount,==,2 
display=false
buttons=1
event1=EventTravelEventWater
```

The quests use different travel event types which deal with different areas (e.g. water, forest...). The following travel event types can be used:

- EventTravelEventRoad
- EventTravelEventPlains
- EventTravelEventForest
- EventTravelEventWater
- EventTravelEventMountains

You can change the type of travel event that a quest uses be changing the `event1=EventTravelEventForest` command to a different travel type. See the following example for details:

```
[EventTravelStep1Quest1]
operations=TravelStep1Quest1,=,1 TravelStepAmount,+,1
vartests=VarOperation:TravelStep1Quest1,==,0 VarTestsLogicalOperator:AND VarOperation:TravelStepAmount,==,0 
display=false
buttons=1
event1=EventTravelEventWater
```

#### Adding more missions
If you plan to add more than 6 missions to your campaign you need to change multiple events in the `CP\events.ini` and the `CP\travel.ini` file.

The following events would be needed to add an additional 7th quest (which will be called quest 6 because the finale would be quest 7 in this example):
```
[EventQuest6]
vartests=VarOperation:%QuestAmountPlayed,==,%AmountQuest6 
display=false
buttons=1
event1=EventAddAct1Quests

[EventQuestFinale]
vartests=VarOperation:%QuestAmountPlayed,==,%AmountQuestFinale 
display=false
buttons=1
event1=EventAddFinaleQuest

[EventAddAct1Quests]
display=false
buttons=1
event1=EventAddQuest1 EventAddQuest2 EventAddQuest3 EventAddQuest4 EventAddQuest5 EventAddQuest6

[EventAddQuest6]
operations=PlacedQuest6,=,1
vartests=VarOperation:%PlayedQuest6,==,0 VarTestsLogicalOperator:AND VarOperation:PlacedQuest6,==,0 VarTestsLogicalOperator:AND VarOperation:%QuestAmountPlayed,==,%AmountQuest6 
display=false
buttons=1
event1=
add=UIQuest6

[EventStartQuest6]
vartests=VarOperation:TravelStepAmount,>=,3 
display=false
buttons=1
event1=/06_Quest/quest.ini
audio=AudioQuestBegin

[EventTravelQuest6]
operations=TravelStepAmount,=,0 QuestSelectedQuest6,=,1
display=false
buttons=1
event1=EventTravelEventLoop
audio=AudioButton1
```

Furthermore you would need to add travel events in the `CP\travel.ini`:

```
[EventTravelEventLoopQuest6]
vartests=VarOperation:QuestSelectedQuest6,==,1 VarTestsLogicalOperator:AND VarOperation:AdditionalTravelEvents,==,0 
display=false
buttons=1
event1=EventTravelStep1Quest6 EventTravelStep2Quest6 EventTravelStep3Quest6 EventStartQuest6

[EventTravelStep1Quest6]
operations=TravelStep1Quest6,=,1 TravelStepAmount,+,1
vartests=VarOperation:TravelStep1Quest6,==,0 VarTestsLogicalOperator:AND VarOperation:TravelStepAmount,==,0 
display=false
buttons=1
event1=EventTravelEventRoad

[EventTravelStep2Quest6]
operations=TravelStep2Quest6,=,1 TravelStepAmount,+,1
vartests=VarOperation:TravelStep2Quest6,==,0 VarTestsLogicalOperator:AND VarOperation:TravelStepAmount,==,1 
display=false
buttons=1
event1=EventTravelEventRoad

[EventTravelStep3Quest6]
operations=TravelStep3Quest6,=,1 TravelStepAmount,+,1
vartests=VarOperation:TravelStep3Quest6,==,0 VarTestsLogicalOperator:AND VarOperation:TravelStepAmount,==,2 
display=false
buttons=1
event1=EventTravelEventWater
```

#### Act 1 and Act 2 quests
The template currently makes no differences between act 1 and act 2 quests. If you plan do create custom logic for the different acts you should use the two events prepared for this:

````
[EventAddInterludeQuest]
display=false
buttons=1
event1=

[EventAddAct2Quests]
display=false
buttons=1
event1=
````

Furthermore you need to call these events from the `[EventQuest1]-[EventQuest5]`-events (or any additional events).

#### Changing shop logic
The shop is called from the following events in the `CP\events.ini` file:

- `[EventCity]`
- `[EventCityAct2]`
- `[EventCityEL]`
- `[EventCityELAct2]`

If you want to add more or less items to the Shop you need to change the items added in these events:

## Translations
Checkout the [Translation](https://github.com/NPBruce/valkyrie/wiki/Translations#scenariosquests) sections of the valkyrie wiki for details how to translate the texts used in this template.

Any further translations of the template would be very much appreciated (please open a ticket in the [issues](https://github.com/Quantumrunner/ValkyrieDescentCampaignTemplate/issues) section or create a pull request).
