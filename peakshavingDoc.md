#Documentation for the peakshaving code. And what do I want to change with it

## Introduction
This file is designed to act as the charging/discharging manager for the entire string. A quick description of how it works: It tracks the rate at which batteries are charging at. It records exactly how many amphours are pushed into the system and how many are drawn from the system. It also maintains rest times, charge/discharge threshholds. This is done by interacting with a database layer, which maintains communications with the inverters

## Lines 4 - 36
This is all the variables that we need right now. I will put this into a single python dictionary and use that to save and update data. Because of the dictonary's close relation to the JSON format, it is ideal

## Lines 39 - 95
This whole bunch of functions are designed to send commands to the database. Where they are read by the serverside code. There really isn't much that can be done here. The best that I can do here is abstract the connection to the database. It was too much hassle at the moment of writing

All of these functions simply send a command, the handling of these commands is done by the server side code. 

All of these functions, save for ConnectToDB(), all do the same thing, by manipulating a dictionary, and pushing it off to the database. This should be consolidated and made more solid to allow for better editing in the future

### Function ConnectToDB()
Function that makes the connection to Mongo on the localhost, selects the set database for the current application, and then it returns the database object so that it could be manipulated further. 

This takes NO parameters, but eventually it should, to allow connection to remote hosts.

### Function sendStartCharge()
It creates a new database object, selects the inverterCommands collection, and finds the one record there that we are using to communicate with the inverters. It edits that record to tell the inverters what to do. 

There are no parameters to this function

### Function sendStandbyCommand()
Manipulates the sent out dictionary to inform the inverters to go into a rest state. It turns off charging and selling indiscriminantly 

There are no parameters to this function

### Function sendStartSell()
Manipulates the sent out dictionary to inform the inverters to start selling their load. 

There are no parameters to this function

## Lines 98 - 137

### Function GetValues
This function populates a dictionary in the script that will be used to hold all the values that are used by the charging algorithem. It's updated every cycle that the code loops. This is passed around and updated accordingly. 

What needs to be done is saving of this document such that on crash, it will be presisted. In addition to that, inclusion of checks to the data would be beneficial

The paramater for this function is the dictionary that we are going to be updating. 

## Main Loop
 
### Quick Synopsis 
Everything within the main loop is maintained within a forloop which counts to 25. The database is logged with data after the 25th cycle. A "step" record within the database is recorded at the end of every cycle. But this is an access point for realtime data. 

### Lines 144 - 163
We take in a dictionary that will maintain the values for this program. We populate that dictionary through the getValues function. Within that dictiornay we look to see that there were no voltage limits hit. If any limit was hit, a boolean value is set and then we move on. 

We are assuming that only one limit can be hit at one time.

### Lines 166 - 188
If we have hit a minimum limit, than we are assuming that there is no more power left within the battery. If we are charging and the maximum voltage was not hit, then we send a charge command again. Otherwise we put the system into rest. 

If we hit a maximum limit, we will put the system into rest. Determination of discharging the string is later decided. 

If we didn't hit ANY limits, what we do is we simply maintain the state of the previous run. 

The issue with how this is currently handled is that 1. I am constantly updating the database that stands between the inverters. There is a handle for this in the serverside code. But it's a timesensetive operation, so it must be stripped of ALL extra fat. 

#### How I plan to change this
Right now, I have a psudo state system, where we are running a boolean of what the system is doing. Is it charging, is it in rest, or is it discharging/selling. The events that will trigger transitions between these states are time as well as voltage limits. There could be more added to this later on. 

### Calculations of data into information [Lines 196 - 298]
The following lines are 

#### Lines 196 - 221

This is where the calculations for the coulombic counting is done. These are Michael's functions. They might be encapsulated into a function, but they, themselves will probably not be changed. 

#### 231 - 252
These two if statements determin whether we should transition over to rest smoothly, and if we do transition, it sets values accordingly. This includes calculating for the culombic efficiency as well as state of charge, and other values.

#### 256 - 264
There are corrections of values that need to be made. This is handled at this part of the code. This is critical to do because if we don' d these corrections, what you lose is accuracce of the data that you're collecting.

#### 266 - 298
Transitions between my psudo states are handled here. What we end up doing is we set the booleans for whether we are charging, discharging, or resting here. this is the only place where these values are edited. 

#### What needs to be changed with this model
Right now there is no precistance of data. This is a dangerous thing because should anything crash, we are going to be stating ALL over again, using only the voltage as a marker for where we currently are. This is not a good way to approch this issue, but it will be approched differently on the next version of this code. 






