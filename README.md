# EDA_IPLdata

# Perform EDA on IPL Data
import numpy as np 
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt 
matches_df = pd.read_csv("IPL_matches.csv")

matches_df.head()
# DATA INFORMATION
print(matches_df.info())

matches_df["umpire3"].isnull().sum()
matches_df["umpire3"].tail(10)
matches_df.describe()
# Matches we have got in the dataset 
matches_df['id'].max()
# Seasons we have got in the dataset
matches_df['season'].unique()
**Team won by Maximum Runs**

matches_df.iloc[matches_df['win_by_runs'].idxmax()]
matches_df.iloc[matches_df['win_by_runs'].idxmax()]['winner']
**Team won by Maximum Wickets**
matches_df.iloc[matches_df['win_by_wickets'].idxmax()]['winner']
**Team won by minimum runs**
matches_df.iloc[matches_df[matches_df['win_by_runs'].ge(1)].win_by_runs.idxmin()]['winner']
**Team won by Minimum Wickets**
matches_df.iloc[matches_df[matches_df['win_by_wickets'].ge(1)].win_by_wickets.idxmin()]
matches_df.iloc[matches_df[matches_df['win_by_wickets'].ge(1)].win_by_wickets.idxmin()]['winner']
## Observation : 
> 1. **Mumbai Indians** is the team which won by maximum and minimum **runs**
> 2. **Kolkata Knight Riders** is the team which won by maximum and minimum **wickets** 
## Season Which had most number of matches
plt.figure(figsize=(12,6))
sns.countplot(x='season', data=matches_df)
plt.show()
> In **2013**, we have the most number of matches
plt.figure(figsize=(12,6))
data = matches_df.winner.value_counts()
sns.barplot(y = data.index, x = data, orient='h')
plt.show()
> **Mumbai Indians** are the winners in most of the matches
## Top Player of the match winners
top_players = matches_df.player_of_match.value_counts()[:10]
#sns.barplot(x="day", y="total_bill", data=df)
fig, ax = plt.subplots(figsize=(15,8))
ax.set_ylim([0,20])
ax.set_ylabel("Count")
ax.set_title("Top player of the match Winners")
top_players.plot.bar()
sns.barplot(x = top_players.index, y = top_players, orient='v', palette="Blues");
plt.show()
> **CH Gayle** is the most Successful player in all match winners
**Number of matches in each venue:**
plt.figure(figsize=(12,6))
sns.countplot(x='venue', data=matches_df)
plt.xticks(rotation='vertical')
plt.show()
> There are quite a few venues present in the data with **"M Chinnaswamy Stadium"** being the one with most number of matches followed by **"Eden Gardens"** 
**Number of matches played by each team:**
temp_df = pd.melt(matches_df, id_vars=['id','season'], value_vars=['team1', 'team2'])

plt.figure(figsize=(12,6))
sns.countplot(x='value', data=temp_df)
plt.xticks(rotation='vertical')
plt.show()
> **"Mumbai Indians"** lead the pack with most number of matches played followed by **"Royal Challengers Bangalore"**. There are also teams with very few matches like *'Rising Pune Supergiants'*, *'Gujarat Lions'* as they are new teams that came in only last season.
**Number of wins per team:**
plt.figure(figsize=(12,6))
sns.countplot(x='winner', data=matches_df)
plt.xticks(rotation=90)
plt.show()
> **MI** again leads the pack followed by CSK.
**Champions each season:**

Now let us see the champions in each season.
temp_df = matches_df.drop_duplicates(subset=['season'], keep='last')[['season', 'winner']].reset_index(drop=True)
temp_df
**Toss decision:**

Let us see the toss decisions taken so far.
temp_series = matches_df.toss_decision.value_counts()
labels = (np.array(temp_series.index))
sizes = (np.array((temp_series / temp_series.sum())*100))
colors = ['gold', 'lightskyblue']
plt.pie(sizes, labels=labels, colors=colors,
        autopct='%1.1f%%', shadow=True, startangle=90)
plt.title("Toss decision percentage")
plt.show()
          
> Almost 55% of the toss decisions are made to field first. Now let us see how this decision varied over time.
plt.figure(figsize=(12,6))
sns.countplot(x='season', hue='toss_decision', data=matches_df)
plt.xticks(rotation='vertical')
plt.show()
> It seems during the initial years, teams wanted to bat first. Voila.! Look at the **2016 season**, most of the toss decisions are to field first.
# Since there is a very strong trend towards batting second let us see the win percentage of teams batting second.
num_of_wins = (matches_df.win_by_wickets>0).sum()
num_of_loss = (matches_df.win_by_wickets==0).sum()
labels = ["Wins", "Loss"]
total = float(num_of_wins + num_of_loss)
sizes = [(num_of_wins/total)*100, (num_of_loss/total)*100]
colors = ['gold', 'lightskyblue']
plt.pie(sizes, labels=labels, colors=colors,
        autopct='%1.1f%%', shadow=True, startangle=90)
plt.title("Win percentage batting second")
plt.show()
> So percentage of times teams batting second has won is **53.2**. Now let us split this by year and see the distribution.


**Top players of the match:**
# create a function for labeling #
def autolabel(rects):
    for rect in rects:
        height = rect.get_height()
        ax.text(rect.get_x() + rect.get_width()/2., 1.02*height,
                '%d' % int(height),
                ha='center', va='bottom')
temp_series = matches_df.player_of_match.value_counts()[:10]
labels = np.array(temp_series.index)
ind = np.arange(len(labels))
width = 0.9
fig, ax = plt.subplots(figsize=(15,8))
rects = ax.bar(ind, np.array(temp_series), width=width)
ax.set_xticks(ind+((width)/2.))
ax.set_xticklabels(labels, rotation='vertical')
ax.set_ylabel("Count")
ax.set_title("Top player of the match awardees")
autolabel(rects)
plt.show()
> **CH Gayle** is the top player of the match awardee in all the seasons of IPL.
## Top Umpires:
temp_df = pd.melt(matches_df, id_vars=['id'], value_vars=['umpire1', 'umpire2'])

temp_series = temp_df.value.value_counts()[:10]
labels = np.array(temp_series.index)
ind = np.arange(len(labels))
width = 0.9
fig, ax = plt.subplots(figsize=(15,8))
rects = ax.bar(ind, np.array(temp_series), width=width,)
ax.set_xticks(ind+((width)/2.))
ax.set_xticklabels(labels, rotation='vertical')
ax.set_ylabel("Count")
ax.set_title("Top Umpires")
autolabel(rects)
plt.show()
> **Dharmasena seems** to be the most sought after umpire for IPL matches followed by Ravi. Others are fairly close to each other.
