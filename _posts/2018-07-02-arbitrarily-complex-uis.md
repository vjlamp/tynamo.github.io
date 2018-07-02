---
layout: post
title: Arbitrarily complex UIs
---

# Arbitrarily complex UIs

Two years ago I read a blog post about [some guy writing a Tetris webapp with Clojure in two hours](http://timothypratley.blogspot.com/2015/07/you-should-be-using-figwheelreagent.html) and I rembember thinking there's no way. There's simply no way I can develop anything that complete in just two hours. But it was right then and there I decided to pick up Clojure. In the last 12 months, I've not only developed, but published three Clojure/Script projects and I can confidently say I'm able to develop at the same pace I'm designing an application.

One of the projects I developed is Coachaide. In all honesty, it did take a bit longer than two hours to "complete", but I'll use it as an example here because it's still a simple hobby project. I had written the first, client-side/in-browser only version as an experiment in two weeks last Summer. At the beginning of the year, I had some time to kill, so I added touch gestures, simplified and reworked the UI, generalized the concept to support multiple game types, added multiple balls *and* wrote user management and a whole server side for it, complete with Postgres backed database in three manic weeks (thanks family for putting up with me!).

<iframe markdown="0" style="float: right;width: 40%;height: 500px;min-width: 300px;" src="https://soccer.coachaide.com" frameborder="0" allowfullscreen="true">
</iframe>

The world is of course full scrappy little tacticboards, but far fewer of the more intelligent user interfaces like the one in Coachaide. It understands passes, tracks players positions in time, works online and offline (because it aggressively caches all resources on the client thanks to the programmable cache of a progressive webapp), scales to any screen size (go ahead and try resizing your browser) and works on all modern devices with touch or mouse interfaces. Certainly you can implement the same with older and more conventional web technologies, but the effort required for a free-to-use tool is too big and too costly, which is why so many of these hobby project stay in the drawer, never quite reaching the "good-enough-to-publish" state.

For another example, let's compare two competing implementations I had developed for the same, unnamed UI-heavy project. Earlier, I had already implemented the project as a complete Tapestry app with Ember frontend. It took me months to write it (although that includes design). For practice, I rewrote it with Clojure in less than two weeks (obviously it's far easier when you know what the goal is). These two web apps are functionally almost fully equivalent except I had added a few more bells and whistles in the Clojure version - just because I could.

### Lines of code - Tapestry version with Ember
(output by github.com/AlDanial/cloc v 1.72 from "src" using "--exclude-dir=assets"
 to remove Ember .js files)

Language           |  files     |     blank    |  comment    |    code  |
-------------------|------------|--------------|-------------|----------|
JavaScript         |     8      |       870    |      782    |    4629  |
Java               |    130     |      2250    |     2369    |    8780  |
CSS                |     18     |       527    |      223    |    2297  |
HTML               |     22     |        76    |       23    |    1185  |
XML                |     11     |       141    |      116    |    1101  |
**SUM:**           |  **188**   |    **3864**  |   **3513**  | **17992**|

### Lines of code - Clojure/Script version with Reagent
(output by github.com/AlDanial/cloc v 1.72 from "src" and "resources" combined)

Language        |    files     |    blank    |  comment      |     code   |
----------------|--------------|-------------|---------------|------------|
ClojureScript   |       15     |      256    |      399      |     2027   |
Clojure         |       15     |      204    |      330      |     1183   |
Java            |        2     |       44    |      151      |      127   |
ClojureC        |        3     |       28    |       48      |      115   |
CSS             |        3     |      165    |      135      |     1544   |
SQL             |        9     |       40    |      145      |      155   |
HTML            |        2     |        6    |       10      |      107   |
Markdown        |        2     |       20    |        0      |       58   |
**SUM:**        |   **51**     |  **763**    |  **1218**     | **5316**   |

As a summary, reaching the equivalent functionality (and more) required less than 1/3 of the lines of code in the Tapestry implementation, and if you've ever written Tapestry, you know it's fairly succinct for Java. The lines of code is still hardly the right measurement stick, but the development time is. It's hard to explain why it's so much faster to develop in pure Clojure if you haven't experienced it yourself. While the stateless nature of functional programming is clearly part of it, the advantages go far beyond it. One big part is there is not constant switching between the template and the code, but just one view, as provided by Reagent (the better React). Like this guy described it: ["Clojure fits into the React model like toothpaste in tubes and the community has switched almost universally ... to one of the React wrappers"](https://news.ycombinator.com/item?id=9490638).

Another one of those things is that I feel don't have bugs anymore, I just have logical errors :) It may be a bit difficult to grasp the higher order functions at first but like they say, it's very easy to reason with Clojure code. Since you are mainly dealing with pure values, there's barely any null checking or other major sources for classic bugs in object-oriented world, like non-initialized values, missing references or stale object state.

Also, did you see that ClojureC in the cloc summary? That's Clojure crossover code, same code that's compiled both to Java and Javascript. Except for node.js and Scala.js, there's really nothing out there that allows you to write the same code and then let you decide whether you want to run it on the client or on the server or both.

But I digress. Expectations for good user interfaces have risen at the same time as it's become easier to build them. It's gotten more common to require a single, responsive UI that works on all devices instead of building a different interface for each each platform and screen format. Usage patterns have also changed. <https://www.coachaide.com> sees northward of 70% of the userbase from mobile and I expect that trend to only accelerate. Increased usage of mobile requires more design effort because screen real estate on mobile is so limited. Mobile usage also requires more tapping and less typing.

Personally, I've never really learned to love developing user interfaces. Back-end algorithms (I've developed a few fairly complex ones over the years) often seem so much simpler to me. There are fewer possible inputs and once you get your algorithms right, they might just work for years without requiring any fixing or updating. In the user interface, there are always too many ways to achieve the same goal or present the same data and I'm never really quite sure what the right answer is - whether it's positioning elements, choosing colors etc. That, together with typical issues building user interaces with conventional object-oriented/component-based patterns and the usual problems associated with local state, UI dispatches, notifier patterns etc. have always been a turn off for me.

Now though, armed with Reagent and [re-frame](https://github.com/Day8/re-frame), I feel confident building arbitrarily complex user interfaces. My pace of development doesn't seem to suffer like before when I develop the application further and the complexity increases. One of the simple measures is lines of code per file. From 10,000 feet high, the more lines in a file, the more complex it is. One of the files in coachaide.com has over 2000 lines. If it was a class file, I would have refactored that class (file) into smaller classes a long time ago, following a classic composite pattern. But Clojure, and functional languages in general, are remarkably shallow, and this seems to apply both to my own user interface code as well as Clojure ecosystem and supporting libraries in general (for examples, consider [Ring+Compojure](http://clojure-doc.org/articles/tutorials/basic_web_development.html) vs servlets+any Java web framework or jaba jaba, links here).

As great as this brave, new world of user interface development is, the one thing I'm still wondering is: where are all these great apps? I see surprirsingly few successful examples of scalable, responsive, progressive webapps out there. Am I too far ahead of the curve? Are there just too many existing apps out there with expensive resources thrown at them that nobody cares about making stuff better, faster & cheaper?

PS. Don't worry, haven't forgotten about Tapestry. I'll be happy to continue maintaining Tynamo libs even if I dabble with other stuff every once and awhile :)

PPS. Originally, there were no "neutral" players in Coachaide. It took me 30 mins to implement the end-to-end functionality, from the button in the UI to persistence changes for storing the new info in the DB.

PPPS. I hadn't planned on localizing Coachaide to multiple versions, but after finding out about [tempura](https://github.com/ptaoussanis/tempura), I decided ti o give it a go, nand ended up internationalizing and localizing it to Finnish over one weekend. Thanks to my neighbor Laura, there is also Spanish translations!

PPPPS. Before publishing this, I decided implemnt full-text search for Coachaide. That took a bit longer, but just for the heck of it, I implemented **both** remote and local (as in client-side) full-text searches for it!
