# <ins>Advanced Enchantments Shame</ins>

Advanced Enchantments (or AE for short) is a paid/premium plugin for Minecraft (spigot, paper and all kinds of forks, whatever xD) and it allows you to create custom enchantments, kind of like vanilla ones. While I love the idea behind it, the default enchantments this particular plugin comes with are downright AWFUL, MISLEADING and sometimes actually just WRONG.

I understand that it might not be 100% accurate, especially for a plugin that's been around for years but the mismatches are so massive that I've actually started to create almost all of them from scratch cause none of them can be trusted, none of them really work and their descriptions are SO AWFUL that your users can only guess what might be the real meaning behind them.

Especially since this seems to be a HUGE selling point of the plugin, boasting about their "450+ Custom Enchants" in the title! If your 450+ Custom Enchants are shit as a starting point, you shouldn't make it a big selling point if you're asking me.

Here are all my issues in no particular order but all of them have annoyed me for YEARS and I had to vent about them somewhere, so here - we - go!!

* Cooldowns are shit
  * They make it almost impossible to know what the real probability of any enchantment is to trigger.
  * It also means your chances will vary depending on how fast you trigger it. It's entirely unpredictable and only makes sense for very few enchantments.
  * They make some enchantments worse at higher levels! Their chances increase but so do their cooldowns, WHY???
  * They are intransparent to both the admin configuring the plugin and the end user as it's not explained anywhere. Take Timber for example... everyone's always confused why it triggers sometimes and then not for a while, so they kind of figure out there appears to be a cooldown but they never know for certain. This is just plain bad and serves no purpose!!
  * Take the enchantment Hook for example. It only triggers if a fish has been caught but has a 12 second cooldown once you get the extra experience. Why the cooldown if it requires a successful catch to begin with?!? Why not just lower the chance instead? I think this serves no purpose at all.
  * The enchantment Ambit is another good example here. It deals damage to a radius of mobs around you and happens when you hit the mobs. The chance at level 1 is 4% with a cooldown of 8 seconds, max level (7) has a chance of 15% but a cooldown of 14 seconds. Why does the cooldown increase? If I'm being hit by a horde of zombie piglin I'm better off with a lower cooldown as it'll hit them more often. Almost DOUBLING the cooldown makes absolutely NO SENSE to me!
* Default descriptions are shit
  * They do not tell you if they apply for PvE or PvP, you only find out via trial and error
  * They are sometimes misleading. Take Epicness for example, the descriptions reads as follows: "Gives particles and sound effects." but it only has a chance to do so and only if you hit a mob or a player. The description makes it sound like it's permanent.
  * The enchantment Dodge is another perfect one here, too. Description is the following: "Chance to dodge physical enemy, chance increases when sneaking." I'm not sure if you were able to guess that by PHYSICAL ENEMY they are talking about a player and not mobs. Not sure when mobs become non-physical but I suppose AE made them now...
* Some enchantments and their combinations make absolutely NO SENSE
  * Take the enchantment Experience for example. It can be applied to pickaxes, shovels and axes but the config says that you only get experience if the broken block is an ore. Someone tell me who mines ores with a shovel or an axe!!
* "Applies-to" and "applies" in the config do not always align
  * To clarify, "applies-to" is what's shown to the user in the lore, "applies" is what is actually technically possible
  * An example could be Nether Slayer which says Weapons for "applies-to" but in actuality it only applies to swords and axes which I can guarantee you not everyone would consider WEAPONS of Minecraft. There are bows, crossbows, tridents and probably even more. Honestly I would probably not even allow this to be changed in the config but hey, I'm not the developer.
* Outdated conditions / Misalignment between descriptions and actual technical meanings (probably kind of a duplicate of "Default descriptions are shit")
  * The descriptions also often do not match what they actually do, probably just cause nobody updated them in years!
  * Another one for Nether Slayer, says it deals more damage to nether mobs, in reality it only works on blazes, zombie pigmen and ghasts but I can't even count how many mobs have been added to the nether since these were the only ones roaming the nether, it's ridiculous!
* Menues are outdated and their descriptions are sometimes entirely wrong
  * I'm too lazy to find examples for this but the descriptions of buttons and their lore are often times misleading or just wrong. Some say to right click when that's either not doing anything OR not doing what it said it would do. There is not even any explanation as to how Advanced Enchantments and the enchanter works or any of its other functions, it's yet another very, VERY intransparent feature and the outdated and wrong descriptions just add on to this.
  * It's another aspect that I have revamped entirely, basically designed an entirely new GUI which explains in easy words how the menus work and what they all do.
* Using experience instead of levels is super clunky for the end user and not remotely easily digestable, by default AE uses vanilla experience rather than levels.
  * It's only recently become a possibility to change this to levels but you'll have to make all the changes yourself if you want to go that route. I've done so but it takes a lot of time.
  * End users (and I) do not know how much 120k vanilla experience are. Yes, it shows how much you still need but honestly this is still confusing and while you might put a band-aid on this by translating it to what it's roughly in levels, it's still not 100% accurate unless you also change the experience values as well to match the levels exactly. I'm using levels from now on and I've not looked back. People obviously know when they've reached level 50 but they do not even remotely know when they might have reached something like 38,400 experience.
  * I understand the pros; if you have 500 levels and you pay experience instead of levels, you get more books as levels scale exponentially but I'll give you a counter to that: Aren't you embracing people being afk at huge experience grinders by doing so? If you use levels instead, people will have to be more active as they want to spend their levels as quickly as possible so they do not want to waste too much experience and time.
* This just happened (2024-01-18): https://tickettool.xyz/direct?url=https://cdn.discordapp.com/attachments/1018941491663605761/1197554800779337738/transcript-regex-still-wr-5508.html?ex=65bbb0d1&is=65a93bd1&hm=dd3d132235456472645bd818a89e68ef4073616e58eafd04bed12a51e6eaf80f&
  * I know it's super pedantic of me but it's just another example of lack of caring at all about anything. I don't know what to do but shake my head...
  * Lucky for them it seems that after a while these transcripts are removed. I first told the developers through GitHub that one of their RegEx examples on their website was wrong and showed them what the proper fix was. They however screwed up and gave another wrong example and I felt the need to open a ticket on their Discord server but they then told me it's just an example and nobody would ever use it. Obviously they're not entirely wrong but if anyone ever relies on this, they'll simply use wrong information.

I might be adding more in the future but for now my rant is over. I might even post this link in their Discord, maybe whenever I find some related post about it.
This isn't JUST meant to rant, it's also meant to show where the plugin should, and in my opinion MUST, improve.

As I started rewriting the descriptions, I even opened a ticket on their Discord, offering to send what changes I had made but it wasn't really of any interest or whatever. By now I have changed SO MUCH about the plugin that it's almost not recognizable anymore and sharing my current configuration would be a major overhaul and I don't think it makes sense anymore. If they ever approach me, I will be inclined to share it and let them check it out, otherwise I'll just keep using my own stuff as it seems futile to bring any sense into the plugin unless you do it yourself.

The worst thing is that the idea and even the foundation of the plugin aren't really bad but the package that you get by default is just UNUSABLE. For a plugin that I paid for, the quality is lacking BIG TIME and I wish they'd take some time to refine what they've created in the past.

~ Folas1337
