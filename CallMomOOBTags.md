# CallMom AIML Tags #

The CallMom project extends AIML by including a new set of tags to
control device actions.  You can create your own custom AIML bot for CallMom with our OOB tag set.  "OOB" means "Out of Band", an engineering term for a conversation on a separate, hidden channel.  For example if you are having a phone call with someone, and during the call send them a text message, the text message is "out of band" from the voice call.  In our case this refers to commands that the bot sends to the phone as part of a reply, but these commands are hidden from the end-user.

# OOB Tag Descriptons #


## 1. OOB Tag ##

`<oob>`

The `<oob>` tag wraps all the OOB tags described below.  To include an OOB response in a `<template`, surround it with `<oob>...</oob>`.

### OOB Syntax ###

`<oob>(.*)</oob>`

### OOB Example AIML ###

```
<category>
<pattern>BATTERY LEVEL</pattern>
<template>
<oob><battery></battery></oob>
Checking...
</template>
</category>

<category>
<pattern>SENDTEXTMESSAGE</pattern>
<template>
<oob><sms><recipient><get name="numbertodial"/></recipient> <message><get name="textmessage"/></message></sms></oob>
Now sending "<get name="textmessage"/>" to <get name="textcontactpers"/>.
</template>
</category>

<category>
<pattern>DO YOU HAVE NEWS *</pattern>
<template><oob><botecho>news <star/></botecho></oob></template>
</category>

```


## 2. Dial a number ##

`<dial>`

The `<dial>` command activates the phone dialer with a given number.

### Dial Syntax ###

`<dial>(.*)</dial>`

### Dial Example AIML ###

```
<category>
<pattern>CALL 911</pattern>
<template>Responding to 911 with your location.
<oob><dial>911</dial></oob>
</template>
</category>

<category>
<pattern>DIAL *</pattern>
<template>Calling <star/>.
<oob><dial><star/></dial></oob>
</template>
</category>

<category>
<pattern>WE HAVE A FLAT TIRE</pattern>
<template><srai>FLAT TIRE</srai></template>
</category>

<category>
<pattern>FLAT TIRE</pattern>
<template>
<oob><dial>1-800-222-4357</dial></oob>
I'm calling Triple A.
</template>
</category>
```


### Example Usage of Dial ###

**Human:** Call 911<br />
**Robot:** Responding to 911 from your location.<br />
**Human:** Dial 5551212<br />
**Robot:** Calling 5551212.<br />

## 3. Send an SMS Message ##

`<sms>`

The `<sms>` command launches the text messaging application and prepares an SMS with to a given recipient with the specified message.

### SMS Syntax ###

`<sms><recipient>(.*)</recipient><message>(.*)</message></sms>`

### SMS Example AIML ###

```
<category>
<pattern>SENDTEXTMESSAGE</pattern>
<template>
<oob><sms><recipient><get name="numbertodial"/></recipient> <message><get name="textmessage"/></message></sms></oob>
Now sending "<get name="textmessage"/>" to <get name="textcontactpers"/>.
</template>
</category>
```


### Example Usage of SMS ###

**Human:** Send a text to Bob saying I am on my way.<br />.
**Robot:** Now sending "I am on my way" to Bob.

## 4. Web Search ##

`<search>`

The `<search>` command launches the browser and requests a Google search with the given search terms.

### Search Syntax ###

`<search>(.*)</search>`

### Search Example AIML ###

```
<category>
<pattern>SEARCH THE WEB FOR *</pattern>
<template><srai>XSEARCH <star/></srai></template>
</category>

category>
<pattern>XSEARCH *</pattern>
<template>
<srai>RANDOM SEARCH PHRASE</srai>
<oob><search><star/></search></oob>
</template>
</category>
```


### Example Usage of Search ###

**Human:** Search the web for Cake Recipes.<br />
**Robot:** Searching...Searching...Please stand by.

## 5. Open a URL ##

`<url>`

The `<url>` command launches the browser with a specific HTTP request.

### URL Syntax ###

`<url>(.*)</url>`

### URL Example AIML ###

```
<category>
<pattern>WIKIPEDIA</pattern>
<template><oob><url>http://www.wikipedia.org</url></oob> Opening Wikipedia.</template>
</category>

<category>
<pattern>SHOW ME *</pattern>
<template>
<srai>pictureblab</srai>
<oob><url>http://<star/>.jpg.to</url></oob>
</template>
</category>
```


### Example Usage of URL ###

**Human:** She me a picture of a Unicorn.<br />
**Robot:** I've displayed a picture for you.

## 6. Read text data from a URL ##

`<urlget>`

The `<urlget>` command launches sends an HTTP request to a web service and reads back text data.  The app sends the text data to the bot as an input with the prefix OOB URLDATA.  The bot may then process the result and create a reply.

CallMom includes some post-processing to try to translate XML results into AIML-friendly natural language.

**Note:** The `<urlget>` command is somewhat deprecated by the `<botecho>` command described below.

### Urlget Syntax ###

`<urlget>(.*)</urlget>`

### Urlget Example AIML ###

```
<category>
<pattern>LOOK UP THE VALUE OF PI</pattern>
<template>
<oob><url>http://api.wolframalpha.com/v2/query?input=pi&amp;appid=YourAppIdGoesHere</url></oob>
Ok I will ask Wolfram Alpha.
</template>
</category>

<category>
<pattern>XWOLFRAMALPHA *</pattern>
<template>
<think>
<set name="findout"><star/></set>
<set name="webservice">wolframalpha</set>
</think>
<oob><geturl>http://api.wolframalpha.com/v2/query?input=<star/>&amp;appid=YourAppIdGoesHere</geturl></oob>
<srai>RANDOM MEDIUM SEARCH PHRASE</srai>
</template>
</category>

<category>
<pattern>OOB URLDATA *</pattern>
<template><star/></template>
</category>
```


### Example Usage of Urlget ###

**Human:** Look up the value of pi.<br />
**Robot:** 3.1415926535897932384626433832795028841971693993751058...


## 7. Read response from Web Services ##

`<botecho>`

The `<botecho>` command may be used to access web services supported by the BotEcho server. BotEcho
is a service that connects to multiple information sources to retrieve responses to
natural language queries.   !The BotEcho service hides all the details of parsing various
web service formats and returns a clean natural language response to every query.

If BotEcho fails to find a response, the CallMom app will return a string "OOB XSEARCH" to the bot.
You may write an AIML category with `<pattern>OOB XSEARCH</pattern>` to make the bot
respond intelligently when BotEcho finds no response.

The list of Services supported by BotEcho includes:

  * **News** - returns the latest news via yahoo or google.
Example:  `<botecho>News about Obama</botecho>`

  * **Ping** - returns pong if service is available
Example:  `<botecho>Ping</botecho>`

  * **TrueKnowledge** - queries the trueknowledge service.
Example:  `<botecho>When was Obama born?</botecho>`

  * **Weather** - queries google to get the latest weather information.
Example:  `<botecho>What is the weather like in Berlin?</botecho>`

  * **WikiAnswers** - queries Answers.com.
Example:  `<botecho>Who is president of America?</botecho>`

  * **Wikipedia** - returns relevant information from wikipedia.
Example:  `<botecho>Define Algorithm</botecho>`

  * **WolframAlpha** - queries Wolfram Alpha.
Example:  `<botecho>What is the distance to the moon?</botecho>`

  * **Translation** - queries translation service.
Example: `<botecho>Translate Where is the hotel to Mandarin</botecho>`



### BotEcho Syntax ###

`<botecho>(.*)</botecho>`

### BotEcho Example AIML ###

```
<category>
<pattern>DO YOU HAVE ANY NEWS *</pattern>
<template><oob><botecho>news <star/></botecho></oob></template>
</category>

<category>
<pattern>TRANSLATE *</pattern>
<template><oob><botecho>translate <star/></botecho></oob></template>
</category>

<category><pattern>XFIND *</pattern>
<template>
<think><set name="topic"><set name="findout"><star/></set></set></think>
<oob><botecho><input/></botecho></oob>
</template>
</category>


```

### Example Usage of BotEcho ###

**Human:** How much is a gallon of gas?<br />
**Robot:** $3.95 per gallon in Oregon 4 quarts...(Answers.com)<br />
**Human:** Translate where is the hotel to Mandarin<br />
**Robot:** (response in Mandarin)<br />
**Human:** What is the population of France?<br />
**Robot:** 65,350,000 in January 2012...(Answers.com)<br />
**Human:** What is the longest river in the world?<br />
**Robot:** Nile River, the natural waterway in Egypt...TrueKnowlege.com


## 8. Find a location on map ##

`<map>`

The `<map>` command opens the map application and displays a location.

### Map Syntax ###

`<map>(.*)</map>`

### Map Example AIML ###

```
<category>
<pattern>WHERE IS THE AIRPORT</pattern>
<template><oob><map>airport</map></oob>
It is where the planes land.<think><set name="it"><set name="topic">Airport</set></set></think> </template>
</category>

<category>
<pattern>WHERE IS THE *</pattern>
<template><srai>GOOGLEMAP <star/></srai></template>
</category>

<category>
<pattern>I AM HUNGRY</pattern>
<template>I'm looking for a restaurant near you. <srai>GOOGLEMAP Restaurant</srai>
</template>
</category>

<category>
<pattern>GOOGLEMAP *</pattern>
<template>
<random>
<li>I'm looking on the map.</li>
<li>I've found several near you.</li>
<li>Let me try to find it for you.</li>
<li>I think I can find it.</li>
<li>Is this what you are looking for?</li>
<li>I think I know where <person/> is.</li>
</random>
<oob><map><star/></map></oob></template>
</category>
```


### Example Usage of Map ###

**Human:** Where is the nearest Starbucks?<br />
**Robot:** I've found several near you.<br />
**Human:** I am hungry.<br />
**Robot:** I'm looking for a restaurant near you.<br />


## 9. Get directions ##


`<directions>`


The `<directions>` command opens the map application and finds driving directions to a specified location.  Optionally, you may specify both a start location and a destination.

### Directions Syntax ###

`<directions><to>(.*)</to></directions>`

`<directions><from>(.*)</from><to>(.*)</to></directions>`

### Directions Example AIML ###

```
<category>
<pattern>SHOW ME HOW TO GET FROM *</pattern>
<template>
<srai>directions from <star/></srai>
</template>
</category>

<category>
<pattern>DIRECTIONS FROM * TO *</pattern>
<template>
Here are the driving directions.
<oob><directions><from><star/></from><to><star index="2"/></to></directions></oob>
</template>
</category>

<category>
<pattern>DIRECTIONS TO *</pattern>
<template>
Here are the driving directions.
<oob><directions><to><star/></to></directions></oob>
</template>
</category>
```


### Example Usage of Directions ###

**Human:** Directions to Boston?<br />
**Robot:** Here are the driving directions.<br />
**Human:** Show me how to get to Logan Airport.<br />
**Robot:** Here are the driving directions.<br />


## 10. Launch an app ##


`<launch>`


The `<launch>` command opens an app with a specified package name.

### Launch Syntax ###

`<launch>(.*)</launch>`

### Launch Example AIML ###

```
<category>
<pattern>TAKE A PICTURE</pattern>
<template><srai>CAMERA</srai></template>
</category>

<category>
<pattern>CAMERA</pattern>
<template><oob><launch>com.android.camera</launch></oob>
Activating device camera.
</template>
</category>

<category>
<pattern>READ MAIL</pattern>
<template>
<srai>GMAIL</srai>
</template>
</category>


<category>
<pattern>GMAIL</pattern>
<template>
<random>
<li>Launching </li>
<li>Opening </li>
<li>Reading </li>
</random>
Gmail.
<oob><launch>com.google.android.gm</launch></oob></template>
</category>
```


### Example Usage of Launch ###

**Human:** Take a picture.<br />
**Robot:** Activating device camera.<br />
**Human:** Read mail.<br />
**Robot:** Opening Gmail.<br />


## 11. Send an Email ##


`<email>`


The `<email>` command laucnhes the email app.  You may specify the fields to, cc, subject and message.

### Email Syntax ###

```
<email><to>(.*)</to><cc>(.*)</cc><subject>(.*)</subject><body>(.*)</body></email>

<email><to>(.*)</to><subject>(.*)</subject><body>(.*)</body></email>

<email><to>(.*)</to><body>(.*)</body></email>

```

### Email Example AIML ###

```
<category>
<pattern>MY EMAIL IS *</pattern>
<template>
<think><set name="email"><star/></set></think>
I'll remember your email <star/>.
</template>
</category>


<category>
<pattern>SEND ME A TRANSCRIPT</pattern>
<template>
<srai>TRANSCRIPT</srai>
</template>
</category>


<category>
<pattern>TRANSCRIPT</pattern>
<template>
Sending transcript to <get name="email"/>.
<oob><email><to><get name="email"/></to><subject>Transcript of <bot name="name"/> with <get name="name"/> on <date format="%B %d, %Y at %H:%M:%S %Z"/></subject><body><srai>DIALOG HISTORY</srai></body></email></oob></template>
</category>
```


### Example Usage of Email ###

**Human:** My email is joe.jones@example.com.<br />
**Robot:** I'll remember your email.<br />
**Human:** Send me a trascript.<br />
**Robot:** Sending transcript to joe.jones@example.com.<br />


## 12. Select a Contact ##


`<contact>`


The `<contact>` command launches the Contact Picker.  When the user selects a contact, the
CallMom app tells the bot to learn the contact's phone numbers.   Specifically, the bot learns
the contact's Home, Work, Mobile and Custom numbers (if applicable).

### Contact Syntax ###

```
<contact></contact>
```

### Contact Example AIML ###

```
<category>
<pattern>CONTACT</pattern>
<template>Select a contact.
<oob><contact></contact></oob>
</template>
</category>
```


### Example Usage of Contact ###

**Human:** Contact.<br />
**Robot:** Select a contact.<br />
**Human:** (selects Joe Jones)<br />
**Robot:** I will remember Joe's mobile number is 212-555-1212.<br />



## 13. Battery Level ##


`<battery>`


The `<battery>` command requests the CallMom app to get the device's battery level.  The app
responds to the bot with a message in a format like "OOB BATTERY LEVEL IS 50 PERCENT", telling the bot that
the battery level is 50 percent.  The bot may respond by reading back the level.

### Battery Syntax ###

```
<contact></contact>
```

### Battery Example AIML ###

```
<category>
<pattern>HOW ARE YOU</pattern>
<template><srai>BATTERY LEVEL</srai></template>
</category>

<category>
<pattern>BATTERY LEVEL</pattern>
<template>
<oob><battery></battery></oob>
Checking...
</template>
</category>

<category>
<pattern>OOB BATTERY LEVEL IS *</pattern>
<template>Battery level is <set name="batterylevel"><star/></set> percent.
</template>
</category>
```


### Example Usage of Battery ###

**Human:** How are you?<br />
**Robot:** Checking...Battery level is 75 percent.<br />


## 14. Version ##


`<version>`


The `<version>` command requests the CallMom app to retrieve the app version number.
The app responds to the bot with a message in a format like "OOB CALLMOM VERSION IS 1.2.1.3", telling the bot that
the version is number 1.2.1.3.  The bot may respond by reading back the version number.
Additionally, the app displays a Toast message showing the version information.

### Version Syntax ###

```
<version></version>
```

### Version Example AIML ###

```
<category>
<pattern>WHAT VERSION ARE YOU</pattern>
<template><srai>VERSION</srai></template>
</category>

<category>
<pattern>VERSION</pattern>
<template>
<oob><version></version></oob>
Checking app version...
</template>
</category>

<category>
<pattern>OOB CALLMOM VERSION IS *</pattern>
<template>This is version <set name="version"><star/></set>.
</template>
</category>
```


### Example Usage of Version ###

**Human:** What version are you?<br />
**Robot:** Checking app version...This is version 1.2.1.3.<br />


## 15. Set Bot ##


`<setbot>`


The `<setbot>` command tells the app to switch to a different Pandorabot.  The bot is specified by a hostname **host** and a bot id **botid**.

### Set Bot Syntax ###

```
<setbot><host>(.*)</host><botid>(.*)</botid></setbot>
```

### Set Bot Example AIML ###

```
<category>
<pattern>SWITCH ME OVER TO CAPTAIN KIRK</pattern>
<template>Goodbye from <bot name="name"/>...
<oob><setbot><host>callmom.pandorabots.com</host><botid>d74317427e345ab2</botid></setbot></oob>
</template>
</category>
```


### Example Usage of Set Bot ###

**Human:** Who are you?<br />
**Robot:** I am called A.L.I.C.E.<br />
**Human:** Switch me over to Captain Kirk.<br />
**Robot:** Goodbye from ALICE.<br />
**Human:** What is your name?<br />
**Robot:** James T. Kirk


## 16. Schedule ##


`<schedule>`


The `<schedule>` command requests the CallMom app to add an event to the calendar.  The default behavior is to add the event to all your selected calendars.  If you wish to add events only to certain calendars, go to the Calendar Settings on your device and choose the calendars you want.

The event is specified by:

  * Event title
  * Event description
  * Event location
  * Event year
  * Event month
  * Event day
  * Event hour
  * Event minute
  * Event duration (in minutes)

### Schedule Syntax ###

```
<schedule>
<title>(.*)</title>
<description>(.*)</description><location>(.*)</location>
<year>(.*)</year><month>(.*)</month><day>(.*)</day>
<hour>(.*)</hour><minute>(.*)</minute>
<duration>(.*)</duration>
</schedule>
```

### Schedule Example AIML ###

```
<category>
<pattern>REMIND ME TO *</pattern>
<template><srai>setupcalendarentry</srai></template>
</category>

<category>
<pattern>SETUPCALENDARENTRY</pattern>
<template><srai><input index="1"/> ENDCAL</srai></template>
</category>

<category>
<pattern>REMIND ME TO * TOMORROW ENDCAL</pattern>
<template>
<think><set name="eventname"><star/></set><set name="checkdate"><date format="%m %d"/></set><srai>XTOMORROW</srai><set name="newhour">9</set></think>
Sure thing. I'll add it to your calendar now. <srai>ADDNEWCALENDAREVENT</srai></template>
</category>
<category>
<pattern>ADDNEWCALENDAREVENT</pattern>

<template>
<oob><schedule><title><get name="eventname"/></title><description><get name="eventname"/></description><location></location><year><date format="%Y"/></year><month><get name="newmonth"/></month><day><get name="newdate"/></day><hour><get name="newhour"/></hour><minute>00</minute><duration>30</duration></schedule></oob><oob><launch>com.android.calendar</launch></oob>
</template>
</category>
```


### Example Usage of Schedule ###

**Human:** Remind me to go to the post office tomorrow<br />
**Robot:** Sure thing. I'll add it to your calendar now.

## 17. Alarm ##


`<alarm>`


The `<alarm>` command requests the CallMom app to set an alarm.

The alarm event is specified by:

  * Alarm message
  * Alarm year
  * Alarm month
  * Alarm day
  * Alarm hour
  * Alarm minute
  * Alarm duration (in minutes)

Note: The Android Alarm Clock supports only `<message>`, `<hour>` (from 0 to 23) and `<minute>` (from 0 to 59).  The OOB specification includes the remaining fields for future supported devices.

### Alarm Syntax ###

```
<alarm>
<message>(.*)</message>
<year>(.*)</year><month>(.*)</month><day>(.*)</day>
<hour>(.*)</hour><minute>(.*)</minute>
<duration>(.*)</duration>
</alarm>
```

### Alarm Example AIML ###

```
<category>
<pattern>SET AN ALARM FOR * AM</pattern>
<template>I'll set the alarm for you.
<oob><alarm><message>Alarm</message><hour><star/></hour><minute>00</minute></alarm></oob></template>
</category>

```


### Example Usage of Alarm ###

**Human:** Set an alarm for 4 AM<br />
**Robot:** I'll set the alarm for you.

## 18. Dialog ##


`<dialog>`


The `<dialog>` tag causes the app to display a pop-up dialog with a fixed set of
input choices.   The `<dialog>` tag may be useful when the botmaster wishes to restrict the user's possible responses to a few items, such as "Yes or No" or a list of names.

The Dialog is specified by the:

  * Dialog title
  * Dialog list

The `<title>` specifies the title to be displayed on the pop-up.  The `<list>` should contain a list of items indicated by the `<item>...</item>` tag.  Whichever of the list items the user selects, the app sends to the bot.  For example if the list contains "Yes", and the user selects "Yes", it would be the same as if the user had said "Yes" to the bot.

In Android, the dialog is cancelable with the Back button.

### Dialog Syntax ###

```
<dialog>
<title>(.*)</title>
<list><item>(.*)</item>...<item>(.*)</item></list>
</dialog>
```

### Dialog Example AIML ###

```
<category><pattern>REPORT A PROBLEM</pattern>
<template>
<oob>
<dialog>
<title>Would you like to send a bug report?/title> 
<list><item>Yes</item><item>No</item></list>
</dialog>
</oob>
To help the developers understand your problem, you can choose to 
send us a transcript of your recent conversation with CallMom.  
You can add your own comments to the email message too.  
Would you like to send a report?</template>
</category>

<category><pattern>YES</pattern><that>WOULD YOU LIKE TO SEND A REPORT</that>
<template><srai>SEND BUG REPORT</srai></template>
</category>

<category><pattern>SEND BUG REPORT</pattern>
<template>Sending transcript to callmom-info@pandorabots.com.
<oob>
<email>
<to>callmom-info@pandorabots.com</to>
<subject>Transcript of <bot name="name"/> with <get name="name"/> on <date/></subject>
<body><srai>DIALOG HISTORY</srai></body>
</email>
</oob>
</template>
</category>

```


### Example Usage of Dialog ###

**Human:** Report a problem. <br />
**Robot:** To help the developers understand your problem, you can
choose to send us a transcript of your recent conversation with CallMom.  You can add your own comments to the email message too.  Would you like to send a report? _The app pops up a dialog with the choices Yes and No._ <br />
**Human:** _Selects Yes_ <br />
**Robot:** Sending transcript to callmom-info@pandorabots.com. _Launches Email app with transcript as message_ <br />