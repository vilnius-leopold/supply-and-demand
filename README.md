Supply and Demand
=================

A design pattern for decoupled modules.


#### Disclaimer

I consider myself a noob. Or, I consider myself too much of a noob to write an article about decoupled systems and to "invent" a ?new? design pattern. You can understand this piece of text as a brainstorm, an inspiration, a monolog.  
At first I was thinking of researching more about design patterns and decoupled systems before I put this out into the wild. But knowing, that error is inevidable, it doesn't really matter. If I was trying to reinvent the wheel or my thoughts where complete nonesense, someone will tell. As a noob I can only benefit from that. So give me a punch!



###The Scenario

I'm writing this application. Put all my code into well defined modules with there own responsibilities. Separation of concerns - sweet, my code works like a clockwork. And with each feature that I add I just create a new module, encapsulate my feature nicely and expose an API that triggers all the magic inside the black box.



###The Problem

Now that the features get more complex, the code demands the modules to transfer data between each other. Module A does something where it requires a resource only Module B can provide. We want to make sure that each Module keeps it's own responsibility. So not allow several Modules to access / write to / manipulate a certain resource.

#### What we already know
Let's say Module A holds the cookie silo. It keeps track of the cookie stock, adds and removes cookies from this silo. Module A knows all about it's cookies.  
Now Module B got a cookie as a present from it's grandma and wants to put it into the cookie silo to store it for later use.  
We can't just let Module B put the cookie into the silo, but Module B doesn't know anything about cookies or cookie silos, so it'll probably put it into a wrong shelf and forget to inform Module A to update the stock, so now Module A has a corrupt inventory... so in the end everything becomes a huge mess.  
So instead letting Module B put a cookie into the silo, it passes the cookie over to Module A that safely stores it in the silo without creating a mess.

#### and?
So the question now is how does Module B hand over the cookie to Module A.  
  
Option A) If both module instances are part of the global namespace instanceA can just call instanceB.addCookie(grandmasCookie); But do you really want to have a ton of modules floating around in the global namespace? Not really! And I didn't even start talk about those modules having submodules. So that brings us to...  
  
Option B) What I used to do (and still do a lot) is to __register__ one Module in another. So after module instanciation I would do something like:
````
var instanceA = new ModuleA();
var instanceB = new ModuleB();

instanceB.registerModule(instanceA);

so now I could access instanceA inside of my instanceB. Something like this:
function ModuleB() {
	...
	registeredModules.instanceA.addCookie(grandmasCookie);
	...
}
````
While in conclusion Option B) is much better than Option A), Module A and Module B still have to know alot about eachother. About  their existenc and how the api looks like each one of them is exposing. You start loosing focus of the module you're currently writing because you constantly have to think about what other modules in your code do. It'll become a headache keeping all this in mind.  
That might be fine if you have only few simple modules, but let's say you have a larger project and you really like to have Modules that are small, concise and are only responsible for a very concrete task. That's great, keeps the code readable and your project is well structured. But now we end up having a lot of modules. And now the modules require al kinds of services and resources from each other. You end up registering a ton of modules inside of your modules.  
That wouldn't be much of a problem if we lived in a perfect world. But let's face it, projects grow dynamically and features get added or change. So your code needs to adapt to that. You start refractoring and outsourcing a functionality from one Module into a new Module, merging two into one and so on... Or, what happens a lot to me, I only start understanding the structure of my project once I actually wrote some "brainstormy-messy-modules". So you endup renaming all the registered modules. Very frustrating! Because, as we all know, we __should have known better__ from the start, because __we are all geniuses__ and refractoring hurts our ego, because we need to admint we didn't think something through, we didn't forsee something and as programmers we're so proud of our superiour logical jelly processor that we keep inside our sculls, right?  
Let's not go down this road of  uneccarry work, stress, self-haterid and self-doubt.



### Supply and Demand pattern to the rescue!



#### About the Maniac who wrote this


