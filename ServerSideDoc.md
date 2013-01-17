# Serverside Python Code Documentation

## Introductio

This applet serves the purpose of being the interface layer to the XW Inverters.There is ABSOLUTELY no other way to communicate to the Inverters other than through the use of this program

It depends on PyMongo, to interface with the mongo Database, as well as the library I wrote out to handle all the requests to the inverters

## Lines 10 - 14

Initialize the MongoDB connection, it is local at the moment. Once initialized it takes in 3 collections. 
    
    - Sensors
    - CritControl
    - Control

There might me some redundancy here and it needs to be revised and changed. The database needs significant maintinance to it right now. Refer to database maintinance in the file dedicated for that

## Lines 18 - 24

These lines are initializing the the inverter object. I am making only one inverter object for simpliciy, and I change the address of that one object. Not the best way to do it, but its quick and easy.

## Lines 26 - 46

This is an if tree which will determine the current state of what is happening. The best way to approch this is to enumerate it. But I do not thing that in python enumeration gives me much benefit. I will check it out though


## Lines 48 - 250 

The functions that I use are defined in this portuin if the code. Each function is going to get descibed here. This section will take multiple passes to go through to make sure that I get al of the interactions down right. 

### General note for all the functions 

Unfortunately this function, as with other functions of this caliber, I spend a lot of the IO time to make sure that it actually made the switch. This is a pain in the ass and it needs to be optimized. This applies to ALL the directive unctions that tell the inverters to do something. 

I suggest that ALL of these functions be rewritten while maintaing their interface. This will greatly optimize the 

### StopCharge()
As th name impies, this function will stop the charging process of the inverters. It runs through all the slave addresses, changing the address of the one inverter object, and then telling it to shut up. 

There are no parameters to pass for the useage of this function

### StartCharge()
Puts the inverters into charge mode. Please refer to general note for functions as to what needs to be done here. This is no exception

There are no parameters to pass for the usageof this function

### StartSell()

Puts the inverters into sell mode. Please refer to the general note for functions. 

There are no parameters being passed to this function

### StopSell()

Stops the sell process of the inverters. It then puts them into standby mode. Refer to the general note as to what needs to be changed with this function. 

There are no parameters being passed to this function

### Rest()

Calls functions "stopCharge()" and "stopSell()". All in all, it is a double pain in the ass because it literally doubles the pain in the ass that is created by the other two functions. REALLY need to optimize the way that is works

### standby()

Puts all of the inverters in to standby mode. This is basically a jill all swtich because when they are in standby they are not going to do or respond to anything besides being put back into operating mode. This definetely needs to be looked at carefully because of how critical it is to the safety of the batteries

There are no parameters being passed to this function

### operating()

This is the only function that will take the inverters out of standby. If you do not call this function before doing something else, the inverters are going to flatout ignore you. Which is not very nice, but its how it works

There are no parameters being passed to this function


### runCommand(controlCode)

This is a enumerated(soon to be) function that takes a numerical code, and uses it to set he inverter into a specific setting. This value is taken from the database, and then passed to this function. From there, this function calls one of the previously mentioned functions. 

There is only one parameter being passed, intereger value taken from the database

## Main Loop 

In the main loop, we zero out our averaged values, and then we go through the array of inverter addresses. We change the address of the inverter object, poll it for it's information, and then we collect it inside a dictionary. We then move on to the next address, and repeat this process. This updates a single register in the database (That might change due to interface problems) 

Between each polling of data, the code checks back onto the database and checks to see if there are any critical actions that need to be taken, as determined manually by the user, or the peakshaving code. This is an attempt to cut down the I/O time of the inverter communications down to a thirdof theoriginal time that it as at. this model works well! And thus it will prbably stay like this until something better is found


