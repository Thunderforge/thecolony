# The Colony

![The Box](./Screenshots/The-Colony-Box.webp)

## First realtime 3D adventure/shooter


This code is made available under the GPL 2.0 license.
There are all three versions here:
- Colony for Macintosh
- Colony for PC
- Colony for Amiga


David W Easter was responsible for both the PC and Amiga ports.

To see The Colony in action, you can watch the demos on Youtube. 

Part 1:

[https://www.youtube.com/watch?v=i1XENlUUOhA]

Part 2:

[https://www.youtube.com/watch?v=3k3qrt76Ddk]


## Images
![The Colony](./Screenshots/the-colony-001.png)
![Promo sheet](./Screenshots/Croquet-promo.webp)
![Queen](./Screenshots/the-colony_9.png)
![Fork Lift](./Screenshots/the-colony_11.png)
![The bridge](./Screenshots/the-colony-bridge.png)

# Colony Memoirs

David A. Smith
February 12, 2005
Draft 1.0

I wrote Colony after a few failed attempts at developing other games before it. I had been interested in the idea of virtual environments for some time, and was very interested in using the computer as a vehicle to build and explore these worlds. My first attempts at this were on an Apple II using simple pictures where you could move through the world by loading a new image based upon the user's decisions. I realized pretty quickly that this approach would simply not scale very well if only due to the limitations of space on a floppy disk. In a way, I turned to 3D as a compression technology - I could fit a (relatively speaking) huge world onto a single floppy disk.

Once I decided on the technology, I began the process of "inventing" it. There were very few examples of realtime 3D available at the time, especially on a PC. Bruce Artwick’s flight simulator book. “Applied Concepts in Microcomputer Graphics” was released in 1984, while I was working on it, and it had some very useful ideas - though all applied to exterior scenes. I wanted an interior environment because it would better lend itself to an adventure style game. Also, I wanted to do something a bit different from the other games that had been done at that time.

I created my first 3D "walkthrough" in 1983 on a PC-XT where I had some blocks in a scene and could walk around them in realtime. Not much more than Sutherland’s original 3D headmount demo to be honest. Interesting that I was working for Stelious Pezaris at the time, and he was one of the hardware designers for Sutherland’s headmount. Of course, what I was attempting with the game would require a bit more thought than just moving some cubes around. 

Soon after, I had heard of the Macintosh. I had seen the Lisa and by this time I was working with Richard Greenblatt at Lisp Machines Inc (actually a startup hosted by LMI), so I was very familiar with bit-mapped displays and windowing environments, but I was excited at the prospect of these technologies being made available in a “personal” computer. I got a loan from my parents and put $500 down to reserve the first machine that came in the nearby computer store – though I didn’t even know what it was called yet. What neither the store nor I knew at the time that there would be an allocation of a single machine to each store when the Mac actually shipped. They did uphold their promise of giving me the first machine in the store, though, and I received my Mac on February 11, 1984. It was an easy date to remember, as it was my birthday.

I took it to LMI to show off, and I could tell it made a number of people very uncomfortable. LMI was currently in a bitter struggle with Symbolics for the small lisp machine market, and with Sun (and Apollo at the time) demonstrating similar capabilities with their workstations with the additional capabilities they had to run general purpose applications (no-lisp based) I am sure the Macintosh made them feel pretty squeezed from both above and now below.

I was absolutely amazed by MacPaint. It was an easy and fun introduction to using the Mac, and at the same time set a standard for how one should write applications for the machine. At the same time, I figured out that there were no tools to program the Mac that would actually run on the Mac. You had to buy a Lisa to be able to develop Mac applications, and at the time, this was well beyond my budget. I could barely afford the Mac as it was. A bit later, Microsoft ported their Basic interpreter over, but this was not an acceptable language for building the kind of game I wanted to build. I thought about using Basic to write a compiler, but never got beyond the conceptual stages. 

I didn’t really get started until a company called Softworks ported a C compiler over. The only good thing about this compiler was that it actually ran on the machine. It was incredibly buggy and if I recall, I was responsible for most of the reports. One particular bug I ran into was the compiler would generate assembler code and would convert the variables to the C variable name by adding the letter “c” in front of it. Well, I had a variable called “lr” and this created a variable called “clr” in assembly. Of course, this was a 68K opcode, which meant either the assembler or the actual code choked on the result (I don’t remember which). I did succeed in getting the first 3D scenes running with this compiler, however.

When the Macintosh shipped, it only had 128K of RAM and a single disk drive. The way you would program on this machines was something like the following:

-	Boot from the system disk 
-	Switch disks and load the editor/code disk and edit the code.
-	Exit the editor, switch disks again and load the compiler disk and run the compiler.
-	Switch back to the code disk for the compiler to access.
-	Link the application, probably having to switch back to the compiler disk to do this.
-	Exit the compiler, attempt to run the executable and probably fail totally locking up the machine.
-	Repeat.

The Mac probably asked for the system disk every so often during this process, but I may be thinking about how Windows still asks for the CD when you are trying to do something new on the PC.

There was no debugger, or much of any other support tool at that point. But I could at least see some results after a time. My first “walkthrough” of a scene ran at about one frame every one or two seconds. I wasn’t terrible discouraged, as I knew I would be able to speed it up quite a bit. The real question was if it would be enough.

I developed a number of concepts that allowed Colony to run pretty fast in the end. A few of them were:

-	I used 256 pseudo degrees. This meant that I could do a sin/cos transform by doing an 8 bit lookup with wraparound. That is, to rotate, just add or subtract from the 8-bit value and ignore overflows. Thus if we add 5 to 255 we would get 260 which was really 5. No need for testing or bit anding. I could then do a simple index into the array. The cos value was just the sin where we added 64 to the index. This meant I could use the same table – remember we only had 128K in RAM.
-	The next thing was to use a grid structure. What this meant was that I could compute a single edge of a grid and figure out the rest of the grid points by simply adding the resulting values. That is, I computed a dx and dy, and I could find every other single point of the grid by just adding or subtracting dx and dy from the last points. The only thing I had to deal with were the offsets of the original point.
-	I also had to deal with rooms and spatial clipping. I actually invented the idea used later for Wolfenstein of ray casting to determine visibility. This turns out to be a 2D problem for this kind of structure. I did not think about doing textures at this point. In any case, this was on a single bit system, so there wasn’t much I would be able to do with textures. The way this worked is I would traverse the grid from the user’s position and keep track of left and right clip edges. These went from the top to the bottom of the screen, so were relatively easy to manage. When I came to an opening in the walls – say a corridor going out to the left or right, I would recurse and continue on. 
-	I made a simplifying assumption that the user’s eye-point was halfway between the floor and the ceiling. This meant that I only needed to compute the floor points and mirror them for the ceiling (a simple subtraction). To make the ceilings of rooms appear higher (or holes appear lower) I just extended the line of the wall up to the top of the screen. Since you couldn’t really look up, it appeared to be a very high ceiling.
-	All of the rest of the 3D objects (chairs, tables, creatures) were done with a regular 2D transform (they all moved and rotated in the plane). The exceptions were the eye-balls where I computed the radius scale and drew circles for the ball itself and ellipses for the iris and pupil. This was a very nice effect.
-	I also added 2D images on the walls, such as doors and graphics by subdividing the computed edges of the rectangle defining a given wall. That is, I split it into an 8x8 grid and then used these new points to draw the figure. This limited my choices to some degree, but I was able to generate a complete alphabet and a few additional characters – including the apple logo – this way. Of course, since I did a linear computation (x1 + x2)>>1, the grid was not quite in perspective, but since they were applied to relatively small areas, I doubt that anyone ever noticed.

I laid out the first level map of the game by hand. The way it worked was each cross of the grid had 16 bits of information – I think I called these cells. Two bits defined the presence or absence of walls coming out of that point, another two bits defined presence of wall ornaments (the 2D images described above), one for each wall, another bit defining if there was a hole in the floor here, and another bit to determine if there was any “furniture”. The rest of the 16 bits was used as an extension mechanism such that if there was an object in the cell, there was an index to it. If there was a door in a wall, I had an extension mechanism that would define if the door was open or closed and, very important, where the door was linked to. It was not always the other side… It all seemed to fit in the 16 bits. What was really interesting was that the maps when saved to disk were about 4000 (4K) bytes in size. There were about ten maps altogether, so that the entire game world fit into about 50K bytes. When you added the 2D animations, I was still able to get the entire game onto one 400K byte floppy disk (single sided). I used a second disk for the sounds, and it was totally full. You didn’t need it to play the game, though.

There could only be one 3D object in a cell at a time. This meant that I could not have two pieces of furniture overlapping, nor could I have a two creatures inhabit the same space. I also set up a collision system such that if I walked into a cell with a 3D object in it, it could trigger some kind of event. For example, if you attempted to enter a cell with a creature in it, I would have the creature turn to look 180 degrees from whatever direction you were looking. This meant that if you were to walk straight into a creature, it would turn around to “see” you. Of course, if you backed into the creature instead it would look 180 degrees AWAY from you. This was quite useful when you got into a corner – just turn away and back into the creature and it would then start wandering away.

The algorithm the creatures used for seeing was something like a Bresenham’s line algorithm. If it’s “line of sight” happened to intersect the cell you were standing in, it would attack. Otherwise it turned and moved randomly. Since much of the game was played in long narrow corridors, creatures had a tendency to find you pretty quickly.

Finally, I had both a basic 3D engine and a data set to apply it to. I finally succeeded in loading the new map while I was visiting with my future parents-in-law at their cabin on Lake Paradox. They must have thought I was crazy, spending all of my time inside programming on my Mac instead of enjoying the water. They were right of course. 

By this time, I had switched development systems to MegaMax C. This was a beautiful compiler, generating efficient and robust code. It also represented a turning point, where the project went from being a hobby to something more serious. The majority of the final game was written with this compiler.

I knew exactly what I was going to see when the world first showed up, as I had done a number of hand drawn simulations of the scene. It was quite a thrill to see the real thing, however. Even the motion through the scene worked. I started in a hallway looking down the hall toward where the hall took a turn to the right. I also knew enough about the code at this point that if I were to go to the end of this hall and try to turn the corner, I would crash. I just couldn’t resist however, and had to do it. I walked down the virtual hallway, turned the virtual corner, and really crashed. But it was extremely interesting that at no time did I ever consider not doing it. I just had to see what was on the other side of that corner. This is when I really started thinking I might have something really interesting.

This was the summer of 1984. Teri and I were married in October that year.

As I mentioned, the first map was done completely by hand and took a very long time not just to create, but even more time to digitize, as I had to follow the rules I described above. I realized that first, there was no way I wanted to do that again, and second, I was really interested in using the system to develop a series of games, not just one. This meant that I needed some kind of CAD tool to develop these worlds. I created a 2-D CAD system, that I called the MapMaker that let me click to add walls and bring up dialogs to extend the cell definitions. This turned out to be critical for the project later. What was interesting was that once I had the MapMaker working, I realized that it generated the worlds flipped left to right – a mirror image of the actual 3D version. Since I was writing it for myself, I decided to leave it as is. In the end, I had to create all of the maps for the game this way. It probably would have taken me a few hours to fix it, but I guess I had other priorities.

One problem that I had to solve was interactions with objects in the world. I had a number of control panels, elevator buttons, etc. in the game where I needed some way to represent and interact with them. I decided to do this with 2D animation. I had bought a copy of VideoWorks from a company called MacroMind and saw most of what I needed with that. I read in one of the Mac magazines that they were planning a progammable version of VideoWorks. Since I really needed this, and did not want to develop it myself, I made a pilgrimage to Chicago where MacroMind and Marc Cantor were. This was the summer of 1985. I had read about them in MacWorld magazine and was extremely impressed with everything I saw there. They had pictures of the team zooming around their office on their chairs and of course their product was really amazing.

The company was located in a small storefront with a nice old wooden floor. I thought it was pretty hip. I met with Elliot Axelrod and Marc and explained what I was doing and what I needed. Unfortunately, they told me that VideoWorks Interactive, (I think that was what they were calling it) would not be available for some time. In fact, I don’t think they had even started working on it yet. 

Marc was quite an interesting character then – he still is, but then it was fresh and he didn’t irritate me as much as he would in later years. Of course he made fun of what I was doing – I guess he could, he had a hot product on the market at the time and I was nobody. I remember that Elliot looked a bit uncomfortable with it. Anyway, I made my first pitch of my system as a game development tool. They were selling the Mac version of MazeWars at the time, and I suggested using my stuff for the next version. Marc did not seem to be very interested in that, so I left the place a bit humbler and quite frustrated. Not only did they not have the technologies I needed, but someone whom I had admired had trashed my work. Not the first time, and it hasn’t been the last – Marc even did it to me again on stage at MacWorld Tokyo about eight years later. 

The upshot was I needed to develop my own interactive animation system. This turned into my first serious object oriented project. I was still coding in C, but I needed a way of managing the animated objects and treating them as discrete entities in the scene. I wound up developing a very nice little package that let take pictures I had created in MacPaint and drop them into the editor. Then I could add additional images to each of these objects so that they could animate, and I could add links between the objects so that one object could act as a trigger for another one – or for the external game world. This tool became critical for linking all of the spaces together and developing all of the control panels and widgets I would need later.

At about this time, I upgraded my Mac to a 512K machine. This allowed me to actually keep parts of the system resident all of the time in a RAM disk – a virtual disk drive that existed in the extended memory. This dramatically enhanced my development performance.

Soon after this, I made my first trip to MacWorld Boston with the intent of finding a partner/publisher for the game system. I took a relatively naïve approach to this effort. I had written a document that described the technology and a number of game ideas that could be created using the system. I also had a number of demo disks that I took with me to pass out. Interesting note was that I saw Richard Stallman in front of the building passing out flyers to boycott Apple. I was at LMI when he was single handedly reverse-engineering the entire Symbolics system so that LMI could keep up with them. We had gone out to many late night Chinese dinners in Chinatown over the time I worked there and he had even introduced me to a Xerox Alto where he taught me how to use a mouse. That is, I tried to pick it up and he slapped my hand down and said – “keep it on the table!” We had even discussed writing some kind of game together, though this never went any further than a discussion or two. We even discussed the idea of giving software away for free. I was opposed to it, so these discussions tended to be a bit more heated. So when I saw him, I thought – hey an old friend! I’ll say hello. Which I did. He looked at me without a hint of recognition and handed me a flyer. Yet another humbling experience.

The largest game company at the show was Mindscape and I decided to start at the top. I walked into their large booth and tried to talk to some of the people there. I was able to get the attention of one of the people there, Scott Berfield, who turned out to be a producer there. I convinced him to let me demo the system to him on one of the Macs they had set up for the show. You can imagine that given how long it took to boot a Mac and get a game running properly on it, it was something of a big deal to take it out of service demoing for a period of time. To his credit, he gave me a shot and I showed him what I had. He was pretty excited at what he saw and asked me to come back to the booth later when one of the company vice presidents would be available. Finally, someone was interested in my work. 

While I waited for the meeting, I visited the other game companies, including Spectrum Holobyte. This was a relatively new startup and had just released a submarine simulation game called Gato. Since they were a bit smaller, it was easier to meet with their decision makers. Again, the reaction was terrific. They wanted to work with me, and more than that, they had just licensed the rights to a book and movie that would be appearing soon and they thought would make a great game. Now I was getting somewhere. A game company with an idea of how to apply the technology to their own content. This was what I was looking for.

I stopped back at the Mindscape booth to meet with their VP – Sandra Schneider. Scott took me back to meet her and introduced us. I told her about the technology I had developed and her only question was “Do you have a game?” I told her that I didn’t – that I had developed a technology that automates the process of developing games. Her response was simple – “Well, come back when you have a game”. She had no interest in seeing the demo, nor in listening to anything else Scott or I had to say. The meeting was over. Scott apologized as we left, but I felt OK as I thought I might have a partner over at Spectrum. 

I don’t remember much about the Spectrum guys except that they were excited about their license and how it would be the next James Bond franchise. That was fine with me. About three months later, they sent me a reviewer’s copy of the book, which I read in one day. It was terrible. It was a terrible book, and it was a terrible vehicle for a game. I wrote up a few ideas and sent it to them, but about this time they started having financial problems and I think they were sold to another company. I never heard from them again. I also sent demo disks to Electronic Arts and Broderbund. Producers from both companies contacted me soon after and we had a number of discussions back and forth, but nothing ever quite materialized out of this.

The writing was on the wall – Sandy was right. No one was interested in game technology; they just wanted games. So now that I had the game engine, it was time to develop one. I would have to eat my own dog food. 

I had a number of ideas that I had been working on. A simple one was the MazeWars clone with a better renderer. In hindsight, this might have been the right move, as later games have demonstrated. I also had prototyped a tank game, and a game loosely based on an Indiana Jones type character called “Valley of the Kings”. I had worked with some friends on this, one to help with the coding and another who happened to be an Egyptian Archaeologist. I went so far as to model the actual tombs in Luxor and had started thinking about a mega-tomb for some long lost pharaoh. The game would start at the Oriental Institute at the University of Chicago, where I received my degree in Mathematics. It was a very cool building and would be fun to model. I started working on a story line where you were received a letter calling for you to rescue your former professor and his beautiful daughter. You had to grab some artifacts from the museum before you left and of course there was a bad guy already there trying to prevent you from leaving. 

The other game was you would play a Space Marshal and would have to save a colony that had been set up on some distant planet. The actual storyline emerged from this basic concept and the limitations of the technology I had to work with.

But, that is as far as I got with it. My friends dropped out after a bit, they had real lives, and I also had some other priorities.

This was 1985. Teri and I had started looking for a house because our first child was about to be born, but we found that we couldn’t afford anything reasonable in Boston, or even at a significant radius from the city, on my consultant pay. We had looked at the North Carolina Research Triangle as a possibility, so I contacted a recruiter and found a job working at the Thomas Lord Research Center working with robotic tactile and force torque sensors and telepresence. It was a lot of fun and required a great deal from me, so I parked the game for a while. After about a years absence, I dusted off the game and picked up where I left off. I started work on the real thing.

Now the problem was turned around – I had to use the tools I had made to develop yet another application. All further development of the tools was at the service of getting the game done. There would be no more general purpose solutions. 

By this time LightSpeed C had come out and changed everything for me. It wasn’t just a compiler, but a complete C development environment. This compiler was created by Mike Kahl, who built a really beautiful system. It made programming the Mac even more fun. 

Also, I had purchased a huge 20-megabyte disk drive that really improved the development process. It was amazing to write some code and have it 

The first decision was of course, which game? I went with the space opera for no other reason than I realized I couldn’t make people in the world. I had certainly tried. I had created some bit-mapped people that would respond when you put the cursor over them. One of them would raise his hands when you pointed at him. The problem was that the bit-mapped characters in the 3D rendered environment just didn’t fit. That meant only one thing – alien life forms.

I had the game start with a communication to you, the Space Marshal about losing contact with the colony and a request to go investigate it. On the way there, your ship collides with a black hole (remember, it was a game) and you crash land on the planet. I had digitized a single strain from Holst “The Planets” for the beginning credits and game introduction and played this little clip over and over until you crashed. Dah-dah-dah-daah, daah, dah-dah-daaah. You awake to find yourself in darkness. Your ship’s reactor cores are destroyed, so you too are stranded on the planet. 

You could see the ship – white on black, and your first task is to figure out how to turn on the lights. I made this part of the game non-threatening. No bad aliens anywhere. You had a nice environment to get used to the motion of the game and a number of puzzles to solve to get off the ship. This is where some of the most sadistic problems were. The game was not particularly well balanced. Here is a small list of them:
	
-	The button for the planet buster bomb and the lights looked identical. One was on the left of the control panel, the other on the right.
-	There was a smoking cigarette on the wardroom cabinet. If you picked up the cigarette you would die.
-	There was a jail cell on the ship – you are a Marshal remember? The floor plates were electric pads that would shock and potentially kill you.
-	There were vents above the reactor that you could fall through. This wouldn’t necessarily kill you, but could cause damage.
-	The airlock was the worst puzzle of all for some reason. Many, many people took a long time to figure out that you had to close the first airlock door behind you before opening the next one in front of you.
-	Many people died because they did not put their space/power suit on before they left the ship.

I, of course, provided no hints as to why they died. My theory was that if you died, how could you possibly know how it happened? My theory was wrong. Remember that the ship was supposed to be the “safe” area for the game player. It got harder after this. Much of the problem was that I was totally paranoid about people completing the game in a couple of hours and demanding to get their money back. I figured I had to make it interesting, but it became somewhat sadistic as it developed. Lot’s of pointless hallways, rooms you could get into, but not out of, infinite hallways that looped back on themselves. It was very mean. People seemed to enjoy it though.

I knew that the majority of the game would take place in a colony, and had decided that it would be built underground. No windows to worry about. I also decided to add a Battlezone like section at the beginning. One of the employees at LMI had purchased one of the stand-up versions of this game and I had played it for days. I got so good at it that I could predict every single action that occurred in the game and the only reason the game would end is I would get tired and walk away from a full rack of lives. I can still play it pretty good, but the controllers tend to be broken on the machines that are still left.

Once you “escaped” the ship, you found yourself on the surface of the planet. Here you met your first aliens. These were crab like creatures with jaws that opened and closed as the moved toward you. While you were fighting these off, you also had to find the entrance to the underground colony. This turned out to be a bit more difficult than I thought it would be. I had made the planet wrap around such that if you went due west, you would come back to your original position from the east, and if you went due north, you would wrap around from the south. In short, the planet was shaped like a torus. I then placed the Colony such that it was exactly north-east from the ship. This meant that if you were to go in any other direction, you might find it, but it would take some time. Also, the “landmarks” were randomly generated as you walked through the scene, so you could not even rely on these to help you figure out where you were and how to get where you wanted to go. People came up with an incredible number of methods to find the colony, most of which were wrong. A favorite was someone suggested that you go north with just a slight angle east (or west). This was the barber-pole method where you would create a spiral around the torus. This would work every time, … eventually. The steeper the angle, the longer it would take to get there. Many people assumed that the entrance moved every time they played the game. It never did.

Once in the colony, you the Space Marshal, had to figure out what was going on. There were no people in sight and there were quite a number of alien creatures that appeared to be angry at you. What had happened – stop me if you have heard this one before – was the colonists had been working on teleportation technology and had accidentally opened a portal between another dimension and this one. One of the alien queens had teleported in and had started decimating the colonists, who had no power suits after all. They realized pretty quickly that they were all going to be destroyed, but they also noticed that the creatures didn’t seem to recognize a human in deep freeze as a problem for them. There were six children in the colony and they were all placed in cryogenic chambers. Your job was simple, rescue the children, repair your ship’s reactor, get off the planet, and blow the damn thing out of space. 

To do that, you needed to find the children, find the forklift to carry them out of there, find the teleportation pods that were needed to rescue some of the children, and find a working reactor core to repair the ship. Easy. No. 

Once I had something more of a game to show off, I revisited MacWorld Boston. Again, I approached Scott Berfield with my wares, and this time, magic happened. Sandy Schneider saw the demo of the game, and instantly decided that Mindscape would be interested. It looked like I finally had a publisher. Scott would be the producer, and after a bit of negotiation, we set off to make the game.

By this point, I had most of the game design done, but was simply a great producer and really helped to make it great. He had a number of ideas and suggestions most of which I incorporated. He was totally supportive and made sure that nothing got in the way of making the game as good as possible. 

As helpful as Scott and the guys at Mindscape were, however, I still did every bit of the programming and created the artwork and the sounds. The only thing I did not do in that game were two pictures of the game player seen in a mirror – one with the power suit off and one with it on, and one of my producer’s screaming his head off used when you died. I think people heard this particular sound more than the rest put together. Virtually every other bit on the disk was mine. 

The next year was used to refine the game and game play. Every element was revisited, bugs were eliminated, puzzles added (never deleted of course). I still have the notebooks picturing how I designed all of the objects in the world. Outside of the maps, I digitized everything by hand. 

I had been working in my office at home on the game. This was balanced with two children, my wife (who also needed some attention, but was a saint – everyone else said so), and my “real” job. Having toddlers made things a bit more interesting. They were fascinated by the computer. My older son wanted to try it out himself. He had seen me put the floppy disks into the drive many times, and had determined, correctly, that that was how you used a Macintosh. Unfortunately, he put the disk in upside down. It turned out that there was simply no way to remove a disk that has been inserted this way. Also, Apple had purposely designed access to the interior of a Mac to be a difficult operation. I was in trouble, because I didn’t have one of the special long torx screwdriver tools that Apple required to open the case. But I did have a long triangular file that by some miracle just happened to fit the screws. Once I had access to the outside of the disk drive, it was a simple matter to eject the disk. Of course, I had to repeat this process the next day before my son got the hint that playing with daddy’s computer wasn’t a great idea.

When it came time for the sounds for the game, I had to look many places. The sound quality available to me wasn’t the greatest, both because it had to fit on a disk and because I didn’t have great digitizing equipment. Some of the sounds I found around the house. I got the sound of toilet flushing by putting a microphone into our toilet at home (not in the water) and flushing. For many of the other sounds I used, I did what many artists have done since. I sampled. The sound of the forklift came from the movie “Aliens”. Another sound was a digitized Star Trek phaser greatly slowed down and reversed. Yet another was the sound of Dave Bowman’s voice as he fell into the star gate in 2001 (“My god, it’s full of stars”). This was used in the 2001 memorial room.

When it finally came time for quality assurance and game play testing, I think Colony may have been the most robust game Mindscape ever tested. They found a total of 12 bugs in the thing. The best anyone had ever done before that was well over 100. I attribute this to a number of factors. First, the development tools I created greatly reduced the potential for bugs. The tools were quite robust and had been in development for a number of years. The other thing was I simply hate bugs. At the same time, to me, one of the best adventure games in the world is tracking down and killing a bug in my code. I simply can’t sleep until I have solved a bug problem. 

One of the worst bugs I ever had to deal with was in this game. Once the game player made it to the Colony, every so often the system would crash and burn at totally random times. You might be playing for ten minutes when it happened or ten hours, but it would just die in a totally random way. I couldn’t trace it. In fact, it seemed like the code was extremely clean. I spent two or three weeks going over the code, rewriting to clarify, or eliminate potential problems. One of the things you learn from this process is that bugs can be a wonderful tool to improve the overall quality of the system.

The system would ultimately crash even if you just stood in one place and didn’t do anything at all. This made me realize that it probably wasn’t something that the game player was doing, but perhaps one of the autonomous creatures that inhabited the world. Finally, I created a real-time map of the world where I could track the movement of the creatures. What was supposed to happen was that the creatures would wander around the world, pretty much at random. When they hit a wall, they would simply turn in another direction. The same thing would happen if they hit another creature… except one of the creatures was special. There was a slow-moving slug like creature that knew how to follow the game player’s trail. When it came across another creature, rather than bouncing off and risk losing the trail, I made it so that it would destroy the other creature and stay on target to find you. This worked great, except that on some rare occasions, this slug could do to a wall what it did to the other creatures. That is, it could delete it. This meant that the virtual door was now open for this creature to explore the rest of the RAM on the Macintosh, deleting and modifying it as it went along. Of course, it was just a matter of time before it found some juicy code. In other words, the bug was a REAL bug.

Many people have suggested that the Colony was based upon a particular movie or story. The reality was that I had taken elements from perhaps hundreds of different stories I had read. That’s the way it often works. Some of the designs came from movies at the time. The teleportation pods were similar to the ones used in The Fly. The forklift was a poor-man’s version of the heavy lifter at the end of Aliens, though I had the story for the game done well before these movies appeared. Actually, an article appeared in one of the Mac magazines that listed about twenty different sources of ideas that I had used in writing the game. I was not aware of more than half of these, so perhaps I stole the ideas from the same place they did. (I actually had one colleague at work anonymously leave this article on my desk with every single one of these items underlined, as if to say – you’re not so creative, look who you stole from). And for those elements I did actually “borrow” – so what? As my friend Mike Backes likes to quote “good artists create, great artists steal”. 

There must be something pretty compelling about teleporting alien creatures and off-world colonies. It seems like these ideas get used and reused on a regular basis. They are neat ideas after all, and have been used to create some of the most interesting worlds ever imagined. They are worth revisiting.

The last part of the game was developed with Peter Sills replacing Scott Berfield as producer. Scott had moved on to Sega. Peter was great and makes a cameo appearance in the game as death. He was pretty enthusiastic when he took over, wanting me to add a whole host of new features. For my part, I was ready to ship this thing, and was getting a bit concerned that as is, it was a bit over the top. Turned out that I was WAY over the top.

The Colony shipped in 1988 and went on to win “Best Adventure Game of the Year” from MacWorld magazine and more recently “The Top Ten Mac Gaming Thingies of the Last 1000 Years” http://www.macworld.com/2000/01/bc/gamesgameroom/  and launched my next career as an entrepreneur. Mike Backes got hold of a pirate version of the game before it was released and brought me on board to work on James Cameron’s movie “The Abyss”, which was funny, because I had borrowed somewhat from his previous movie “Aliens” for the game. I developed a virtual camera and set using the same tools I had developed for the game.

I also got a phone call from Tom Clancy, author of “Hunt for Red October” among others. He happened to be one of my favorite authors. I was even reading “Clear and Present Danger” at the time, so it was kind of strange to be talking to him. Interestingly, he never asked for hints – just wanted to talk about the ideas behind the game and where this technology would be going someday. Tom became a great friend and was my first outside investor in my next venture, Virtus Corporation, where I wrote Virtus Walkthrough. Later, we founded Red Storm Entertainment together where he and the team we had put together created Rainbow Six, one of Tom’s best books and one of the top game franchises in history. We designed another game together, called “Derelict”, which I think has the makings of another classic. I doubt this will ever get made though.
 
The Colony turned into a pretty good game, though it was much harder than it should have been. Many people took months of daily play to complete it. I was told by more than one person that I had caused their divorce. The problem was that I was totally paranoid. I mentioned that I was concerned that people would complete the game in only a few hours. It is interesting to note that even I couldn’t complete it in less than about seven hours of sustained game play. In some ways, I really went too far with it. I guess I made up for that when I helped develop “Timeline”, a game where people really could complete it in two hours and really did ask for their money back. Now I think I have a pretty good idea of where the balance should be. Perhaps I will do another one someday.

Now I am developing a meta-tool. I am working on a new operating system called Croquet with Alan Kay, the father of the personal computer, David Reed whom Alan calls “the ‘slash’ in TCP/IP”, and Andreas Raab, one of the system architects for Squeak. Croquet is an open source project that we hope will foster a rebirth in innovation on the PC, just as Alan’s work helped create the Macintosh, David Reed’s helped create the Internet, and I would like to think that my work helped create modern computer games. I hope Croquet fosters the same excitement in people that the my first Macintosh did for me, over twenty years ago. It is time for something new.

 


Further Info:

Wikipedia article:

[https://en.wikipedia.org/wiki/The_Colony_%28video_game%29]

MacWorld top 10 gaming thingies article:

[http://www.macworld.com/2000/01/bc/gamesgameroom/]

Article in University of Chicago Magazine about me:

[http://magazine.uchicago.edu/9910/html/smith.htm]

Description of the game:

[http://www.mobygames.com/game/sheet/gameId,3489/]

Somewhat recent review:

[http://www.mobygames.com/game/view_review/reviewerId,5736/gameId,3489/platformId,2/]

My current efforts as CTO of Croquet:

[http://www.croquet.io]

My Croquet Blog:

[https://www.croquet.zone/]

Croquet Videos:

[https://www.youtube.com/watch?v=90Sbb9CeQtY]

Colony Amiga:

[https://www.youtube.com/watch?v=RcXzqQ2oKMY]

