# EWIJSON and Script Guide


## Preamble
This guide focuses on Zynj's AI Dungeon script EWIJSON and primarily on using it for manipulating World Info and the context. This guide is intended to motivate someone more dedicated to write an advanced guide on how to better utilize it's many useful features. This guide works as an introductory bootcamp to get you started. It is assumed that you've already read most of the AID research sheet and understand how most of AID's context works. To recap:
```
- Max. input character length: 2772
- Max. Summary length: 500
- If (Summary + WI + Remember) < 50%, then extra story aka history will fill up the empty space
- If (Story + A/N + Input + frontMemory) < 50%, the unused space will not be allocated to (Summary + WI + Remember)
- GPT3 has a hard cap of 1024 tokens for AID users, 2048 for the devs
```
EWIJSON and the wiki written by Zynj can be found at:

https://github.com/Zynj-git/AIDungeon/tree/master/AID-Script-Examples/EWIJSON/release

https://github.com/Zynj-git/AIDungeon/wiki/EWIJSON

Other useful scripts exist as well and will be mentioned. STARSTRUCK has written a userscript for Tampermonkey/Greasemoney that can be combined with any AID specific script for the purpose of modifying the UI and adding artwork from https://www.artbreeder.com/ . The script can be found at:

https://gitlab.com/STARSTRUCK/ai-dungeon-userscripts


## Installing EWIJSON
To use a script in AID you must first write or install it. Either download the 4 json files from Zynj's github or acquire a release zip through the official AID Discord channel #scripting. As of writing it is on version 1.1.9. From inside the AID UI go to the menu > my stuff > make scenario > scripts. Usually this is where you write/develop your own scripts. For a release of EWIJSON we press the upwards arrow and import the zip or the json files. After this is done and you see the code inside shared library, input modifier, context modifier and output modifier the script(s) is ready to be used only within this scenario. You must repeat this for every scenario you wish to use scripts with.


## Using EWIJSON
Reading Zynj's wiki will get you set up on the basics and how to write WI using EWIJSON. Usually this is done inside the scenario you're playing. The main purpose of EWIJSON is introducing regular expression (regexp) functionality inside AID and by default pulling any key written using EWIJSON recognized formats right next to the key in the context towards the front of the fresh context. With the use of attributes you can place your triggered WI wherever you want in the context. Any regular style WI won't be called by EWIJSON and will function identically to how it would inside vanilla AID. These functionalities make it the swiss-army-knife of scripts, allowing you to achieve any kind of text editing that can be done using JavaScript standard ECMAScript regexp. For checking your regexp syntax the author recommends using the following site:

https://regex101.com

Zynj's wiki contains many examples and ideas on how to start using the script. Some of them may be even more advanced than the methods discussed in this document. For now we will discuss two tricks rolled into one example. It is possible to use any other WI format together with EWIJSON. Thanks to EWIJSON WI being close to your inputs, it's also the best way to define `you` inside AID at the moment. Example on how to achieve this alongside synonyms for your name:
```
KEYS				|	ENTRY
yourname.character		|	yourname:[Zaltys or Neanderthal format stuff using you as the noun.]
yourname._synonyms		|	(your|full name|nicknames|titles)
REMEMBER			|	Your name is yourname. The rest of the remember continues...
```

## Im a complete noob and scared but EWIJSON interests me
First consider if you even need or want the script. Every little bit of the tool is consistently formed around it's designed purpose - to dynamically and with relatively low effort manage your WI on the fly through your input field. As long as you have good memory and have understood how regexp works you can easily manage all your WI related data structures from the input field. Its designed to be object-oriented for the purpose of being pseudo-dynamic. If you use a lot of WI and you tweak them often EWIJSON is perfect for you, if this isn't you, you might even prefer using regular old WI. While it can't automatically change the objects for now, you can change any object whenever you want and get immediately changed results from the output. 

Before we continue keep in mind that the AI reads strings in the context and matches them with the keys inside your WI definitions. Then, the entry of the matched key gets inserted in a certain place which is as far back down the context as possible in vanilla. In EWIJSON the match is by default inserted immediately above the matching key in context. This can further be manipulated with EWI attributes.

Say you have `John.character` and assign permanent traits to this group. It would do good to leave the clothing out of the intended permanent group. You might want to change his wardrobe later right? So inside your WI you define `key: John.character.worn entry: pajamas`. After John wakes up and gets ready to work he will get dressed. After this is done we update the subroup with through inputs with `/set John.character.worn business suit`. Following this example you've updated the entry for the former object to be `business suit`. If you ask the AI what John is wearing, it will be a description of some type of business suit.

`._synonyms` is combined with regexp to easily define multiple synonyms for your object. From a WI the author has defined we have `faun girl._synonyms` to define every key the AI recognizes for this root. In entry we have the regexp `(faun|satyr).*(girls?|females?|woman|lady)`. This makes every faun or satyr followed by the female nouns synonymous with `faun girl` from earlier. Then you can describe how you want this species to appear like inside your game with a key like `faun girl.species` with the entry containing your desired description.

`_synonyms` at the front is the same concept, but for objects you haven't whitelisted. The author has thus far noticed this is the best way to define sexual dimorphism, secrets or other booleans. Binaries or configurations of a singular concept that are drastically different. Species is whitelisted, but female isn't then we define key `_synonyms.draph.species.female` with entry `(draph).*(females?|woman|women|lady)|(female|woman|lady).*(draphs?)`. When the AI recognizes that the word draph is preceded or followed by the female nouns, the WI containing the information will be triggered which we have defined in `draph.species.female`.

This isn't even going into the EWI attributes. For those you have to think what you want in terms of manipulating the position or visibility of your WI inside the context. #[d] is used to match the key then place the entry in front of or after the match. #[p=x] tells the AI how many lines *down* in the context you want the matched entry. The other attributes you can research on Zynj's wiki. You'll figure out how to use them if you need them.


### The Ultra Condensed Numbered Point Edition
If this doesn't help you, I can't help you. This is the complete standard workflow for defining things in EWIJSON.
1. Decide on a WI. Character or location for starters. Decide on initial attribute for your characters. `.character` is good. We will now create everything though the input field, or `>`
2. Set your first character. Let's go with John. `/set John.character John`. Here, .character denotes the name.
3. John needs more traits. Let's go with clothes and mental first. `/set John.character.worn business suit`. He now wears a business suit defined under a new entry. We repeat for `/set John.character.mental workaholic` because John loves his job. Now we could repeat this for what his workplace is...
4. John has a full name. We want the AI to understand this. We do `_synonyms` which also means `_conditions`. We do `/set John._synonyms (John Doe|The Workaholic|John The Workaholic)`. Now the AI recognizes his full name and titles. John Doe implies => John.<object> and calls the entry for that WI.
5. Manually edit your whitelist or do `/set _whitelist. character,worn,mental`
6. We want a WI that triggers with a condition and ISN'T whitelisted. John's secret. We do the regexp `/set _synonyms.John.secret (John).*(secrets?)` that will call the entry we also create `/set John.secret John used to wet his bed until he was 8`
	1. Whenever `John` or the synonyms is mentioned in the context EWIJSON will insert everything under John.(character|worn|mental) in the history. The secret will only get inserted with the other WI entries if we mention `John's secret` or John followed by a short sequence of letters then the word `secret`.
7. We want an example of how to use EWI attributes and we want an Author's Notes for our scenario (without using the standard AN UI). We do `/set .#[p=3] [Author's Note: John the Salaryman just wants to live a quiet life. But in a fight he wouldn't lose to anyone.]` Explanation: In regexp just `.` matches everything. It's always active. The EWI function #[p=x] sets the entry x lines up in context. Here we use p=3 lines, literally copying the standard Author's Notes used in AID.


## Credits
Zynj for writing EWIJSON, updating it frequently and educating people on how to use it. STARSTRUCK for his artdungeon.user.js. Latitude for making scripting free. birb for writing this. 