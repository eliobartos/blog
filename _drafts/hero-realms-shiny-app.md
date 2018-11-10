---
layout: post
title: Hero Realms - Shiny App
---

## Intro

Hero Realms is a great deckbuilding card game. Each player starts with same deck of weak 10 cards. Throughout the game you buy stronger cards which are added to your deck. There are many cards and interesting combinations of cards you can buy which makes the game really fun and always different.

You can also play with character expansions which slightly change the starting deck of 10 cards and add character specific abilities into play. There are 5 characters available: Fighter, Wizard, Ranger, Mage, Thief. 

I thought it would be interesting to see how characters play one against another, are they balanced, which one is counter for another. I would also like to se how I'm playing against other people and include some math into it. So I decided to make an app where you can enter the result of the game you just played and it will show you player and character stats. I sometimes refer to characters as classes.

## Building Process

I made an app in R using Shiny, a package used to build web apps. To see the app click on the link: 

<div class="center-text"><a href="https://ebartos.shinyapps.io/herorealms/">https://ebartos.shinyapps.io/herorealms/</a></div>

If you want to learn Shiny and how to make dashboards like this start with these two tutorials:
* <a href="https://shiny.rstudio.com/tutorial/written-tutorial/lesson1/">Shiny tutorial</a>
* <a href="https://rstudio.github.io/shinydashboard/get_started.html">shinydashboard tutorial</a>

### Storing the data
I wanted this app to collect data about played games. So I needed a way to store collected data. First idea was to write write it to csv but there are two problems with that approach:
1. If two users tried to enter new game at the same time, they would both load same csv file, only add their own match and save csv. That would cause one match to be overwritten/missing.
2. I planned to host an app on <a href="https://www.shinyapps.io/"> shinyapps.io </a>. But I learn that you can't have files locally there because your app is not always run on the same server. So usual methods of working with files locally won't work.

Second idea was to use dropbox. Every time a user wants to save new played game an app creates small csv file (with only that game played) and writes it to my dropbox. That way I have a file for each game and both problems 1. and 2. are solved. But on start of the app I want to show all data. That means downloading all files from dropbox to put them in one data frame. Process of downloading from dropbox lasted around 1 second for 10-15 files which was bad. Imagine having 100 or 1000 games played. That would make an app very slow. I guess this method is good if you just want to collect data, for example make something like Google Form. 

So finally I decided that I need to use a database and found free site to host my database remotely. This was obvious solution but I didn't know that you can have remote database for free and set it up in a few minutes. You can!

Great read about storing data while using Shiny can be found <a href="https://shiny.rstudio.com/articles/persistent-data-storage.html">here</a>.d

### Bayes Win Rate

One of the things I wanted to estimate was win rate of a player overall or with a specific character. 
Why I didn't just calculate win rate as percentage of wins from games played. Because if you play 2 games and you win both games I'm pretty sure that your win rate is not 100%. Problem with this is that for small amount of games played win rate will vary and won't be representative. So to estimate win rate I used Bayesian statistics.

So first I needed to set prior distribution of win rate. When you have bernoully random variable beta prior seems sensible. Beta distributin has $\alpha$ and $\beta$ parameters - $B(\alpha, \beta)$. Their interpretaion is number of wins and losses respectively. If we choose $B(5,5)$ that is the same like saying that player played 10 games and won 5 games. Now if someone playes 2 games and wins both games we get Bayes win rate $\frac{5 + 2}{10 + 2} = 58.33\%$. This seems much more plausible.

To summarise:
<br>
<div class="center-text">$ \text{Win Rate} = \frac{\text{wins}}{\text{played}}  \quad \quad
\text{Bayes Win Rate} = \frac{\text{wins} + \alpha}{\text{played} + \alpha + \beta}$</div>

We see from the formulas as we win more games Win rate and Bayes win rate will be more and more the same. 

But except this point estimate which is basically the mean of posterior distribution, we get the whole posterior distribution. Now if you have more players from that you can calculate probability that a player has highest win rate. That is calculated in "Is Best %" column.

The idea to use Bayes Win Rate came from Hearthstone. There users make different decks and want to know which deck is the best. If they just used win rate there would be a lot of decks with really high win rate but small amount of games. This way they corrected for that and are basically saying if you have that high win rate you need to prove it on more games.

Hope this post gave you a lot of material and motivation to learn Shiny and make your own app!