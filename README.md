# cs-179-project

## General Overview:
Our final project for my graphical model AI class. To grossly simplify, we use graphical models to try and estimate
the skill level of each pro starcaft player. This is done by getting a players win/loss ratio of ever other player, 
then through that ratio we place that player in any skill level 0-5 (like a less complex version of ELO).
After arranging all players into their respective skill levels, we use validation data to play out matches, and let the 
graphical model give us the hypothetical winner. We then check to see if its correct, and measure the correctness of the 
graphical model.

## In Depth:

### Skill estimation:
As previously mentioned, we get the win/loss ratio of all players' matchups. That is, if player x plays against player y 13 times
and wins 10 times, then player x will have a favorable win/loss. Through this ratio we decided to build a Markov Chain model (or a "stan model" as its named in the project),
where each node (aside from the central one) was a player who our main player played matches against, and each edge their probability of winning a match.

For the Markov chain parameters, we decided to scale each game to .1 weight, a very low number. This reason is three fold: first, each pro player has so many games played
against the same player that having the odd 1 loss or 1 win shouldn't weight too much on the players' record. Second, after much testing we found that having any number above
or below .1 would severely underfit or overfit the data respectively. Third, similar to the first reasoning, we recognize that in professional sports (even e-sports)
some teams/players simply have a hard time against another particular player. Perhaps their playstyles clash, perhaps its a mental block. Regardless, having a bad streak
with one particular player shouldn't give them a much lower score if they are completely stomping the rest of the competition.


For sampling the data, we decided on using MCMC because we correctly assumed that each player would have many, many different chains (rival players)
associated with it, and as such we needed a method that could, at a reasonable speed, correctly interpret and generate a skill estimation.
For MCMC parameters, we decided to go with the standard 1,000 samples and 2 chains, as, after some testing these parameters where what worked best in the end.
Again, too high sampling/chains and the program would either slow down to a crawl or spit out garbage skill estimations, or too low of those numbers and we
would get overfitting.

Once the skill estimation is finished, we are left with a decimal point between 0 and 5. Interestingly, it seems that our model interpreted higher 
numbers with a lower skill level, as players widely known for their excellence where given a number very near zeroes. This could have also been 
possible due to how we treated the markov chain- perhaps we reversed the winners and the losers somehow? Despite scouring the code for many hours,
we could not find a defenite solution to it, so instead we reversed all scores, and we were finally given accurate numbers, with the closer a players'
skill number reached 0 the worse they where.

### Validation Check
First we checked the skill estimations of top players, to give a general check that they where close to 5 (they where). Once that was done we looped through all
the matches played in the validation data and first tried to predict a winner. We tried implementing two different methods of prediction: one based solely on who
has the higher skill level, and another based on a probability which was based on the difference in their skill level and the number of matches they played. 
Interestingly, both methods gave us about the same results. Once we got the models' prediction, we check to see if the model was right, and in the end we 
calculated how many times the model was correct using a simple correct/total calculation.

### Conclusion:
For the overall correcteness of the model, we kept getting around a 61% correct rate. While this is extremely low (close to a coin flip), we recognize the potential
faults withing the program. For starters, we failed to account for the fact that most, if not all, of these matches are best-of-threes or best-of-fives. Thus, while on
a per-game basis we may only have a 61% chance of being correct, when we take into account the extra games necessary to win a best-of series (and how best-of series usually
do end with the better overall player winning due to its very nature) we may have had a higher percentage. We also recognize that the skill estimation itself is rudementary
at best; simply having win/loss ratio against another player is not enough. What races each player plays, which maps they played on (perhaps certain players are very good
in certain maps) and many other factors could go into who will eventually win. This point is moot however, as graphical models oftentimes lack the complexity of being able 
to handle such number of probabilities (at least that we were taught of in this class). Perhaps we could have used a formula to condense all this information into one 
singular number, but that seemed a bit out of scope for both this assignment and my current knowledge. As it stands, a 61% chance to be correct based solely on the each
players record against each other is honestly not such a bad number in hindsight. 

