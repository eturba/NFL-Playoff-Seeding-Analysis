# NFL-Playoff-Seeding-Analysis

## 📌 Introduction
The NFL is built for parity, using tools like the draft and salary cap to promote competitive balance. Yet some franchises consistently outperform the rest, seemingly resisting the league’s equalizing forces.

This project analyzes NFL playoff seeding from 2002–2025 to understand how dominance forms and persists. By focusing on seeding rather than win–loss records, it examines which teams remain perennial contenders, how new dynasties emerge, and whether top playoff positions are truly accessible to all. Using data-driven analysis and visualizations, the project reveals how NFL power has shifted across eras.

## 🗂️ Loading the Dataset & Checking for Quality
```Python
# Import libraries
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings("ignore")
```
```Python
# Load dataset
df = pd.read_csv(r"C:\Users\turbe\OneDrive\Documents\NFL_Playoff_Seeding - Sheet1.csv")
df
```
```Python
# Check data types and structure
df.info()
```
```Python
# Check for missing values
missing_values = df.isnull().sum()
missing_values
```
```Python
# Ensure no duplicate teams entries for the same season
duplicates = df.duplicated(subset=['season', 'team']).sum()
print(f"Duplicates Found: {duplicates}")
```
```Python
# Validate seed range (Should be 1-7)
print(f"Seed Range: {df.seed.min()} to {df.seed.max()}")
```
```Python
# Check confrence names (Should be AFC & NFC only)
print(f"Confrences found: {df['conference'].unique()}")
```
```Python
# List unique team names for naming consistency
print(sorted(df['team'].unique()))
```

### 📊 What's Inside the Dataset
- Seasons covered: 2002–2025 (24 seasons)
- Conferences: AFC & NFC
- Total teams: 32
- Granularity: One row per team per season

### 🔑 Key variables:
- season- NFL season year
- team- Team name
- conference- AFC or NFC
- seed- Playoff seed (1–7)

### ✅ Data Quality Summary
- No duplicated entries found across all 300 records
- Consistent team naming across all unique franchises, including teams that have recently changed their name (Washington Commanders)
- All values follow the seed ranges (1–7)
- 100% completeness with zero missing or invalid values identified

## ⚖️ Is the NFL Truly a League of Parity?
  The NFL promotes competitive balance through mechanisms like the draft and a hard salary cap. This section examines how many different franchises have earned the #1 seed.
  ```Python
  # Calculate unique teams that achieved the #1 seed
unique_one_seed_teams = df[df['seed'] == 1]['team'].nunique()

## Visualise the total count as a big number card
plt.figure(figsize=(6,3))
plt.text(
0.5, 0.5,
f"{unique_one_seed_teams}\nUnique Teams",
ha='center',
va='center',
fontsize=28,
weight='bold'
)

plt.title('How Many Teams Have Gotten the #1 Seed?\n(2002-2025)', fontsize=13)
plt.axis('off')
plt.show()
```

Analysis: Over 24 seasons, 20 of the NFL’s 32 franchises (62.5%) have reached the No. 1 seed at least once. This pattern suggests that certain teams have dominated the top position during specific stretches, forming recognizable dynastic periods. At the same time, the fact that a majority of teams have eventually claimed the top seed indicates that the league’s parity mechanisms are having an impact, even if elite control persists in cycles.
Next, we will take a look at how frequently 'new' teams have reached the #1 seed in their conference over time.
```Python
# Filter #1 seeds and mark the first time a team appears as the #1 seed
df_seeds = df[df['seed'] == 1].sort_values('season')
df_seeds['is_new_team'] = ~df_seeds['team'].duplicated()

# Track the cumulative count of unique #1 seeds per conference
df_seeds['unique_conf'] = df_seeds.groupby('conference')['is_new_team'].cumsum()

# Plot the growth unique #1 seeds over time by conference
plt.figure(figsize=(10,6))
colors = {'AFC': 'red', 'NFC': 'blue'}
for conf in ['AFC', 'NFC']:
    subset = df_seeds[df_seeds['conference'] == conf]
    final_count = subset['unique_conf'].iloc[-1]
    line_width = 3 if conf == 'AFC' else 2
    plt.step(subset['season'], subset['unique_conf'], where='post',
            label=f'{conf} (Unique: {int(final_count)})',
            color=colors[conf], linewidth=line_width, marker='o')
    
plt.title('Cumulative Unique Teams Reaching the #1 Seed (2002-2025)', fontsize=13)
plt.xlabel('Season')
plt.ylabel('Cumulative Count')
plt.xticks(range(2002,2025, 2))
plt.grid(axis='y', linestyle='--', alpha=0.5)
plt.legend()
plt.show()
```
Analysis: Over the 24-year span, 56% of AFC franchises and 68% of NFC franchises have captured the No. 1 seed. This imbalance raises the possibility that the AFC has experienced longer or more concentrated periods of dominance by a few teams, while the NFC appears to exhibit a higher degree of competitive turnover and parity.
## 🏆 Which Teams Have Dominated the NFL Over Time?
Making the playoffs is a high achievement, especially when an organization is able to do so consistently. Let's take a look at which teams have reached the playoffs the most often:
### 📈 Which Teams Reach the Playoffs Most Often?
```Python
# Count total postseason appearances for the top 15 franchises
team_appearances = df['team'].value_counts().head(15)

# Plot frequency of playoff qualification
plt.figure(figsize=(12,6))
ax = sns.barplot(x=team_appearances.values, y=team_appearances.index, palette='viridis')
for i, v in enumerate(team_appearances.values):
    ax.text(v + 0.1, i, str(v), va='center')
plt.title('Top 15 Teams with Most Playoff Appearances (2002-2025)', fontsize=15)
plt.xlabel('Number of Appearances')
plt.ylabel('Team Name')
plt.show()
```
Analysis: The data shows a clear tier of top-performing franchises. The **Packers** & **Patriots** lead the league with a **75%** (18 out of 24) playoff appearance rate since 2002. This consistency across two decades proves that elite organizations have found a way to remain permanent **playoff contenders**.  

### 🥇 Which Teams Claim the #1 Seed Most Frequently?
```Python
# Filter and count teams that secured the #1 seed
seed_one_teams = df[df['seed'] == 1]['team'].value_counts()

# Visualise regular-season conference leaders
plt.figure(figsize=(12, 6))
ax = sns.barplot(x=seed_one_teams.index, y=seed_one_teams.values, palette='magma')
for i, v in enumerate(seed_one_teams.values):
    ax.text(i, v + 0.1, str(v), ha='center')
plt.title('Teams with Most #1 Seeds (AFC & NFC)', fontsize=15)
plt.xticks(rotation=45)
plt.show()
```
Analysis: While steady playoff appearances signal success, earning the **No. 1 seed** represents true regular-season superiority. The data reveals a clear separation between frequent playoff teams and those that consistently lead their conferences. The **Patriots** established the standard with seven top-seed finishes, followed by the **Eagles** with five. Notably, despite the Patriots and Packers being tied in total playoff appearances, the Patriots demonstrated a far higher level of dominance by securing more than twice as many No. 1 seeds.

### Which Teams Dominated Each Era of the NFL
Let's take a look at which teams dominated each 5-year period. We will take a look at which teams finished 1st or 2nd in their conference.
```Python
# Categorise seasons into 5-year buckets (Eras)
df['era'] = (df['season'] // 5) * 5

# Identify teams securing top-two seeds (1 or 2) per era
era_analysis = df[df['seed'] <= 2].groupby(['era', 'team']).size().reset_index(name='top_seed_count')

# Extract top 3 dominant franchises for each 5-year cycle
era_analysis = era_analysis.sort_values(['era', 'top_seed_count'], ascending=[True, False]).groupby('era').head(3)

print("Dominant Teams (Seed 1 or 2) by Era:")
display(era_analysis)
```
Analysis: Examining the data in five-year intervals highlights how sustained NFL success often stems from strong organizational foundations that support long-term competitiveness. The **Patriots** cemented their elite status by securing a top-two seed across three consecutive periods (2000–2015) and claiming the #2 seed again in 2025. In contrast, the 2020s have been shaped by a "New Guard," led by the **Bills** and **Chiefs**. Overall, the data shows that top franchises consistently discover and maintain a “winning formula,” allowing them to remain contenders year after year.
