<html><body><h2>
	Abstract
</h2>

<p>
	This analysis documents the techniques involved in predicting which team will win in a ranked League of Legends game. Ranked games consist of two teams of five players that try to win by killing the opposing team's nexus. To learn more about League of Legends, <a href="http://leagueoflegends.com" target="_blank">follow this link</a>.
</p>

<p>
	Predicting the winner for the game League of Legends is a difficult task. There are many aspects of the game that must be considered. Player skill level, champion strength and Internet connectivity issues are just a few examples. The most accurate predictor to date is the summed total of each team's players champion win rate.
</p>

<h2>
	Methods
</h2>

<h3>
	Introduction
</h3>

<p>
	Riot Games' League of Legends API was used to collect data for this analysis. The API provided endpoints for fetching player information, player match history and detailed match information. In addition, <a href="http://www.cs.waikato.ac.nz/ml/weka/" target="_blank">Weka</a> was used for data analysis and the creation of the predictive model. Several methods were used prior to the one discussed in this article, however they were left out for brevity.
</p>

<h3>
	Data Collection
</h3>

<p>
	Approximately 1,000 games from each League of Legends ranking system (bronze through challenger) was collected for training and testing the model. Data collection consisted of querying the API for players from each rank and finding that player's previous 20 matches. In some cases more data was collected than others. For example, there are significantly more players in the bronze tier than the challenger tier. 
</p>

<p>
	While querying the API for each match, several sub-API calls were made to obtain each player's win rate for that champion. Once each player's win rates were collected, the totals were summed for each team. In the event that a player has less than 10 games on that particular champion; a win rate of 50% was assigned. Each team's total win rate never exceeds 500 percent (5 players per team with a maximum of 100% win rate per player).
</p>

<p>
	A single entry would look like the following:
</p>

<table border="1" cellpadding="1" cellspacing="0">
	<tbody>
		<tr>
			<td>
				<strong>BlueTeam</strong>
			</td>
			<td>
				<strong>RedTeam</strong>
			</td>
			<td>
				<strong>Winner</strong>
			</td>
		</tr>
		<tr>
			<td>
				385
			</td>
			<td>
				407
			</td>
			<td>
				red
			</td>
		</tr>
	</tbody>
</table>

<h3>
	Data Analysis
</h3>

<p>
	Initially, the data analysis process consisted of training and testing the model from the higher ranked match data (masters and challenger) using Weka. A number of different algorithms were used in the model: decision trees, baysian network and rule based. After testing all of the different models, the J48 decision tree algorithm performed the best (with adjustments to pruning etc).
</p>

<p>
	Here are the J48 options used:
</p>

<pre>
<code class="language-bash">weka.classifiers.trees.J48 -R -N 5 -Q 1 -B -M 2 -A</code></pre>

<p>
	With these options and using the challenger data set the model was able to successfully predict 90% of the games correctly. Testing the model against lower ranked matches showed that the model became more accurate when testing against lower tier data. For example, the master tier has an accuracy of 94% and the lowest tier (bronze) has an accuracy of 98%.
</p>

<h3>
	Predictive Model Implementation
</h3>

<p>
	The output from the Weka J48 decision tree algorithm was implemented in the Go programming language. You can see the full implementation below.
</p>

<pre>
<code class="language-go">package classifiers

/*
=== Run information ===

Scheme:weka.classifiers.trees.J48 -R -N 5 -Q 1 -B -M 2 -A
Relation:     team_win_rates_masters2
Instances:    3304
Attributes:   3
              BlueTeam
              RedTeam
              Win
Test mode:user supplied test set:     641instances

=== Classifier model (full training set) ===

J48 pruned tree
------------------

RedTeam &lt;= 285.871643
|   BlueTeam &lt;= 253.400162
|   |   RedTeam &lt;= 258.712128
|   |   |   BlueTeam &lt;= 229.873032: RedTeam (33.0/5.0)
|   |   |   BlueTeam &gt; 229.873032: BlueTeam (38.0/13.0)
|   |   RedTeam &gt; 258.712128: RedTeam (146.0/10.0)
|   BlueTeam &gt; 253.400162
|   |   RedTeam &lt;= 245.268372: BlueTeam (556.0/6.0)
|   |   RedTeam &gt; 245.268372
|   |   |   BlueTeam &lt;= 302.380981
|   |   |   |   BlueTeam &lt;= 265.680847: RedTeam (53.0/18.0)
|   |   |   |   BlueTeam &gt; 265.680847: BlueTeam (262.0/90.0)
|   |   |   BlueTeam &gt; 302.380981: BlueTeam (286.0/18.0)
RedTeam &gt; 285.871643
|   BlueTeam &lt;= 284.519928: RedTeam (954.0/50.0)
|   BlueTeam &gt; 284.519928
|   |   RedTeam &lt;= 316.890594: BlueTeam (196.0/62.0)
|   |   RedTeam &gt; 316.890594
|   |   |   BlueTeam &lt;= 324.080078: RedTeam (101.0/6.0)
|   |   |   BlueTeam &gt; 324.080078
|   |   |   |   BlueTeam &lt;= 344.303864
|   |   |   |   |   RedTeam &lt;= 330.416656: BlueTeam (7.0/2.0)
|   |   |   |   |   RedTeam &gt; 330.416656: RedTeam (7.0)
|   |   |   |   BlueTeam &gt; 344.303864: BlueTeam (5.0)

Number of Leaves  : 	13

Size of the tree : 	25


Time taken to build model: 0.03 seconds

=== Evaluation on test set ===
=== Summary ===

Correctly Classified Instances         603               94.0718 %
Incorrectly Classified Instances        38                5.9282 %
Kappa statistic                          0.8814
Mean absolute error                      0.1043
Root mean squared error                  0.2121
Relative absolute error                 20.856  %
Root relative squared error             42.3594 %
Total Number of Instances              641

=== Detailed Accuracy By Class ===

               TP Rate   FP Rate   Precision   Recall  F-Measure   ROC Area  Class
                 0.956     0.075      0.927     0.956     0.942      0.975    BlueTeam
                 0.925     0.044      0.955     0.925     0.94       0.975    RedTeam
Weighted Avg.    0.941     0.059      0.941     0.941     0.941      0.975

=== Confusion Matrix ===

   a   b   &lt;-- classified as
 306  14 |   a = BlueTeam
  24 297 |   b = RedTeam
*/

// This classifies which league of legends team will win based on the
// summation of each player's specific champion win rate for their team.
// This is the implementation of the J48 decision tree's output. Overall this
// classifier has an accuracy of rougly 92%.
// The output of this classifier is a string of "blue" or "red".
func CWRWinningTeamClassifier(blueWinRate float64, redWinRate float64) string {
	var winner string
	if redWinRate &lt;= 285.871643 {
		if blueWinRate &lt;= 253.400162 {
			if redWinRate &lt;= 258.712128 {
				if blueWinRate &lt;= 229.873032 {
					winner = "red"
				} else {
					winner = "blue"
				}
			} else {
				winner = "red"
			}
		} else {
			if redWinRate &lt;= 245.268372 {
				winner = "blue"
			} else {
				if blueWinRate &lt;= 302.380981 {
					if blueWinRate &lt;= 265.680847 {
						winner = "red"
					} else {
						winner = "blue"
					}
				} else {
					winner = "blue"
				}
			}
		}
	} else {
		if blueWinRate &lt;= 284.519928 {
			winner = "red"
		} else {
			if redWinRate &lt;= 316.890594 {
				winner = "blue"
			} else {
				if blueWinRate &lt;= 324.080078 {
					winner = "red"
				} else {
					if blueWinRate &lt;= 344.303864 {
						if redWinRate &lt;= 330.416656 {
							winner = "blue"
						} else {
							winner = "red"
						}
					} else {
						winner = "blue"
					}
				}
			}
		}
	}
	return winner
}</code></pre>

<h2>
	Conclusion
</h2>

<p>
	In conclusion, the best predictor for determining the winner of ranked League of Legends games seems to be the champion win rate. Using the model at any rank gives a very accurate prediction of 90% to 98%. If you are interested in using this implementation, then please visit <a href="http://loldestiny.tylermarrs.com" target="_blank">loldestiny.tylermarrs.com</a>.
</p>
</body></html>