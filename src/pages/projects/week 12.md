---
title: 'week 12'
pubDate: 2024-11-18
description: 'Claude is a die hard cowboys fan'
author: 'Josh Stanley'
image:
    url: 'https://docs.astro.build/assets/rose.webp'
    alt: 'The Astro logo on a dark background with a pink glow.'
tags: ["astro", "blogging", "learning in public"]
---
# Week 12
I still have to manually enter the matchup data because I haven't learned how to select data based on a column value with pandas. Also had to run it twice because I forgot that the team W/L export was broken and I had to manually create the csv to replace it.

pulled stats from SportWise and the nfl.com team stats because the view I make for team stats has an undefined value that breaks the export and I dunno where it's coming from

**request file contents**

    import requests
    import json
    import pandas as pd
    from anthropic import Anthropic

    #Read your CSV files
    matchups_df = pd.read_csv('week11/2024_NFL_Schedule.csv')
    stats_df = pd.read_csv('week11/2024_NFL_Team_Stats.csv')
    injuries_df = pd.read_csv('week11/NFL_Injury_Report_2024.csv')

    #Convert dataframes to formatted strings
    matchups_str = matchups_df.to_string(index=False)
    stats_str = stats_df.to_string(index=False)
    injuries_str = injuries_df.to_string(index=False)

    def fetch_data_from_api(api_url):
        """
        Fetch data from the specified API endpoint
        """
        try:
            response = requests.get(api_url)
            response.raise_for_status()  # Raises an error for bad status codes
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Error fetching data: {e}")
            return None

    def send_to_claude(api_key, data):
        """
        Send data to Claude API
        """
        try:
            # Initialize the Anthropic client
            client = Anthropic(api_key=api_key)
            
            # Create a message with the data
            message = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=2048,
        system="You are an AI assistant tasked with generating picks for the current week of an NFL confidence pool. Your goal is to rank the picks from most confident (16 points) to least confident (1 point) based on the provided data and your existing knowledge of NFL teams and previous seasons.",
        messages=[
            {
                "role": "user",
                "content": f"""First, review the current week's matchups:

    <current_week_matchups>
    "Away Team (2024) (d54)","Home Team (2024) (d54)","NFL Season (2024) (d54)","NFL Week (2024) (d54)","City (2024) (d54)","State (2024) (d54)","Arena (2024) (d54)","Field Type (2024) (d54)","Dome (2024) (d54)"
    "Pittsburgh Steelers","Cleveland Browns","2024-2025",12,,,,,
    "Tampa Bay Buccaneers","New York Giants","2024-2025",12,,,,,
    "Minnesota Vikings","Chicago Bears","2024-2025",12,,,,,
    "Dallas Cowboys","Washington Commanders","2024-2025",12,,,,,
    "Kansas City Chiefs","Carolina Panthers","2024-2025",12,,,,,
    "Denver Broncos","Las Vegas Raiders","2024-2025",12,,,,,
    "New England Patriots","Miami Dolphins","2024-2025",12,,,,,
    "Arizona Cardinals","Seattle Seahawks","2024-2025",12,,,,,
    "Detroit Lions","Indianapolis Colts","2024-2025",12,,,,,
    "San Francisco 49ers","Green Bay Packers","2024-2025",12,,,,,
    "Philadelphia Eagles","Los Angeles Rams","2024-2025",12,,,,,
    "Tennessee Titans","Houston Texans","2024-2025",12,,,,,
    "Baltimore Ravens","Los Angeles Chargers","2024-2025",12,,,,,
    "Pittsburgh Steelers","Cleveland Browns","2024-2025",12,,,,,
    "Tampa Bay Buccaneers","New York Giants","2024-2025",12,,,,,
    "Minnesota Vikings","Chicago Bears","2024-2025",12,,,,,
    "Dallas Cowboys","Washington Commanders","2024-2025",12,,,,,
    "Kansas City Chiefs","Carolina Panthers","2024-2025",12,,,,,
    "Denver Broncos","Las Vegas Raiders","2024-2025",12,,,,,
    "New England Patriots","Miami Dolphins","2024-2025",12,,,,,
    "Arizona Cardinals","Seattle Seahawks","2024-2025",12,,,,,
    "Detroit Lions","Indianapolis Colts","2024-2025",12,,,,,
    "San Francisco 49ers","Green Bay Packers","2024-2025",12,,,,,
    "Philadelphia Eagles","Los Angeles Rams","2024-2025",12,,,,,
    "Tennessee Titans","Houston Texans","2024-2025",12,,,,,
    "Baltimore Ravens","Los Angeles Chargers","2024-2025",12,,,,,
    </current_week_matchups>

    Next, analyze the team stats for the 2024 season:

    <team_stats_2024>
    {stats_str}
    </team_stats_2024>

    Consider the latest injury report:

    <injury_report>
    {injuries_str}
    </injury_report>

    To generate your picks:
    1. Analyze each matchup using the provided data and your existing knowledge.
    2. Consider factors such as team performance, home/away advantage, injuries, and historical matchups.
    3. Rank the games from most confident to least confident.
    4. Assign confidence points from 16 (most confident) to 1 (least confident).

    For the final matchup of the week, also generate a total score prediction.

    Provide your reasoning for each pick, explaining the key factors that influenced your decision.

    Format your output as follows:

    <picks>
    <pick>
    <confidence_points>16</confidence_points>
    <winner>Team Name</winner>
    <loser>Team Name</loser>
    <reasoning>
    Explain your reasoning here
    </reasoning>
    </pick>

    [Repeat for all picks, decreasing confidence points]

    <final_matchup>
    <confidence_points>1</confidence_points>
    <winner>Team Name</winner>
    <loser>Team Name</loser>
    <total_score>XX</total_score>
    <reasoning>
    Explain your reasoning for the winner and total score prediction
    </reasoning>
    </final_matchup>
    </picks>

    Ensure that you use all confidence point values from 16 to 1, and that each team appears only once in your picks. Double-check your work before submitting your final answer."""
            }
        ]
    )
            
            return message
        except Exception as e:
            print(f"Error sending to Claude: {e}")
            return None

    def main():

        # Replace these with your actual values
        API_URL = "https://site.api.espn.com/apis/site/v2/sports/football/nfl/scoreboard"
        CLAUDE_API_KEY = "HUNTER2"
        
        # Step 1: Fetch data
        data = fetch_data_from_api(API_URL)
        
        if data:
            # Step 2: Send to Claude
            response = send_to_claude(CLAUDE_API_KEY, data)
            if response:
                print("Successfully sent data to Claude!")
                print("Claude's response:", response)
        
    if __name__ == "__main__":
        main()

**response**

```text
Successfully sent data to Claude!
Claude's response: Message(id='msg_012vd4KrFqeLeKsQ9kMn5hiV', content=[TextBlock(text="I'll analyze the matchups and generate picks based on the provided data and current NFL landscape. Here are my ranked picks from most to least confident:

<picks>

<pick>
<confidence_points>16</confidence_points>
<winner>Kansas City Chiefs</winner>
<loser>Carolina Panthers</loser>
<reasoning>
The Chiefs (7-3) have a significant advantage over the struggling Panthers (1-9). Despite some offensive inconsistencies, KC's defense remains elite. Panthers have multiple key injuries including Adam Thielen (questionable) and Brian Burns is dealing with several issues. The talent gap is massive here.
</reasoning>
</pick>

<pick>
<confidence_points>15</confidence_points>
<winner>San Francisco 49ers</winner>
<loser>Green Bay Packers</loser>
<reasoning>
The 49ers are hitting their stride again after early season struggles. While the Packers have shown improvement, San Francisco's offensive weapons and defensive front should overwhelm them. Even with Trent Williams questionable, the 49ers have too much firepower.
</reasoning>
</pick>

<pick>
<confidence_points>14</confidence_points>
<winner>Dallas Cowboys</winner>
<loser>Washington Commanders</loser>
<reasoning>
Dallas has dominated inferior opponents this year. Their offense is clicking and Washington's defense has major issues. While the Cowboys have some injuries to monitor (Dak Prescott questionable), their depth and overall talent should prevail easily.
</reasoning>
</pick>

<pick>
<confidence_points>13</confidence_points>
<winner>Detroit Lions</winner>
<loser>Indianapolis Colts</loser>
<reasoning>
The Lions continue to show they're legitimate contenders this year. Their balanced offense and improved defense matches up well against an inconsistent Colts team. Detroit's offensive line should control this game.
</reasoning>
</pick>

<pick>
<confidence_points>12</confidence_points>
<winner>Baltimore Ravens</winner>
<loser>Los Angeles Chargers</loser>
<reasoning>
Ravens have been one of the most complete teams in the NFL. The Chargers continue to underperform and have several defensive backs injured. Lamar Jackson should have a strong game against this vulnerable defense.
</reasoning>
</pick>

<pick>
<confidence_points>11</confidence_points>
<winner>Philadelphia Eagles</winner>
<loser>Los Angeles Rams</loser>
<reasoning>
Eagles have shown resilience despite not playing their best football. The Rams are improving but still lack consistency. Philadelphia's running game should control the tempo against LA's defense.
</reasoning>
</pick>

<pick>
<confidence_points>10</confidence_points>
<winner>Miami Dolphins</winner>
<loser>New England Patriots</loser>
<reasoning>
Miami's explosive offense should overwhelm a Patriots team struggling to score points. Even with some key Dolphins players questionable (Tyreek Hill, Terron Armstead), they have enough weapons to handle New England.
</reasoning>
</pick>

<pick>
<confidence_points>9</confidence_points>
<winner>Houston Texans</winner>
<loser>Tennessee Titans</loser>
<reasoning>
C.J. Stroud has transformed the Texans' offense while the Titans continue to struggle with consistency. Houston's passing attack should find success against Tennessee's secondary.
</reasoning>
</pick>

<pick>
<confidence_points>8</confidence_points>
<winner>Seattle Seahawks</winner>
<loser>Arizona Cardinals</loser>
<reasoning>
Seattle is the more complete team despite recent struggles. Arizona has shown fight but lacks the talent to consistently compete. Seahawks' home field advantage will be significant.
</reasoning>
</pick>

<pick>
<confidence_points>7</confidence_points>
<winner>Minnesota Vikings</winner>
<loser>Chicago Bears</loser>
<reasoning>
Despite quarterback issues, Vikings have found ways to win with their defense and running game. Bears continue to be inconsistent, especially on offense.
</reasoning>
</pick>

<pick>
<confidence_points>6</confidence_points>
<winner>Denver Broncos</winner>
<loser>Las Vegas Raiders</loser>
<reasoning>
Broncos have shown remarkable improvement recently while Raiders remain inconsistent. Denver's defense has found its footing and should contain Las Vegas's limited offense.
</reasoning>
</pick>

<pick>
<confidence_points>5</confidence_points>
<winner>Tampa Bay Buccaneers</winner>
<loser>New York Giants</loser>
<reasoning>
Both teams have struggled, but Bucs have shown more offensive potential. Giants continue to have major injury issues and offensive line problems.
</reasoning>
</pick>

<final_matchup>
<confidence_points>4</confidence_points>
<winner>Cleveland Browns</winner>
<loser>Pittsburgh Steelers</loser>
<total_score>33</total_score>
<reasoning>
This projects as a low-scoring defensive battle between division rivals. Browns have the better defense and home field advantage, though QB situation remains uncertain. The total of 33 reflects both teams' defensive strength and offensive limitations.
</reasoning>
</final_matchup>

</picks>", type='text')], model='claude-3-5-sonnet-20241022', role='assistant', stop_reason='end_turn', stop_sequence=None, type='message', usage=Usage(input_tokens=62160, output_tokens=1261))

```


**cost**
for two requests it cost $0.42


