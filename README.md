# weather-newsrooms

Updated for ONA 2024 in Atlanta

## Overview

- The coming days
  - [Heat Risk](https://www.wpc.ncep.noaa.gov/heatrisk/)
  - [Excessive Rain](https://www.wpc.ncep.noaa.gov/qpf/excessive_rainfall_outlook_ero.php)
  - [Winter Storm Severity Index](https://www.wpc.ncep.noaa.gov/wwd/wssi/wssi.php)
  - [Severe storm risks](https://www.spc.noaa.gov/products/outlook/)
  - [Records](https://www.wpc.ncep.noaa.gov/exper/ndfd/ndfd.html)
  - The forecast for [free](weather.gov)!
  - [Your Places: Extreme Weather](https://www.nytimes.com/interactive/2023/us/extreme-weather-forecast-maps.html)
- Finding and using historical data

  - Yesterday
    - Weather forecast office = WFO
    - Google "Atlanta WFO"
    - Click on "Cllimate and Past Weather"
    - Click on "Observed Weather"
    - "Daily Climate Report"
  - Earlier this month
    - NOW Data tab
    - "Daily Data for a month"
  - A while ago
    - Go to https://www.ncei.noaa.gov/access/search/data-search/daily-summaries
    - Type in Atlanta
    - Choose "Preview" on the Atlanta Hartsfield Airport
    - Pick a year month
    - View the PDF
    - Now download all the data
    - But what are these columns?
    - See the descriptions here: https://www.ncei.noaa.gov/metadata/geoportal/rest/metadata/item/gov.noaa.ncdc:C00861/html
  - Over time, approach 2
    - Go to https://builder.rcc-acis.org/
    - Enter ATL
    - por (period of record)
    - por
    - mint (minimum temperature)
    - can convert to csv: https://www.convertcsv.com/json-to-csv.htm
    - can throw into datawrapper
    - Can see trends!
  - Records???

    - Same, but elements are:
    - Record lows: [{"name":"mint","interval":[0,0,1],"duration":1,"smry":{"add":"date","reduce":"min"},"smry_only":"1","groupby":"year"}]
    - Record highs: [{"name":"maxt","interval":[0,0,1],"duration":1,"smry":{"add":"date","reduce":"max"},"smry_only":"1","groupby":"year"}]
    - Documentation: https://www.rcc-acis.org/docs_webservices.html#title8

  - Monitoring real-time warnings
    - Recipe for Slack alerts
      - Variables in the Make file for location and type
      - Where to put the secrets
      - Setting up Slack

## Monitoring Warnings in Real Time

### The Weather Service API

- Documentation: https://www.weather.gov/documentation/services-web-api
- Click on "Specification"
- Building a URL:
  - Base endpoint: `https://api.weather.gov/alerts/active`
  - We want actual warnings, not tests: `?status=actual`
  - Area? Let's say Maryland, Virginia and D.C. You can get fancier here, but states are easy: `&area=MD,DC,VA`
  - Code. This is the warning type. Tornado warning, tornado watch, etc. List is [here](https://www.weather.gov/nwr/eventcodes).
  - Limit. `&limit=500`
- But what about monitoring it?

### Let's play with some code!

1. Sign into Github (or quickly make an account if you haven't already)
1. Go to my Github page **[github.com/jkeefe/](https://github.com/jkeefe)**
1. Click on the pinned repository at the top: [weather-newsrooms-ona24](https://github.com/jkeefe/weather-newsrooms-ona24)
1. Chose the "Fork" button
1. Note that the **owner** is now **you**. Click "Create fork"
1. After a minute, you will have a new screen. Note that your name is up at the top! This is your copy. You can use this now or just watch and return to it later. (If you see **jkeefe** instead of your name, you're on the wrong screen. Go find your copy in your github account.)
1. Now click the green "<> Code" button and, after you do, the "Codespaces" tab under it.
1. Click "Create Codespace on Main"

### Take a look around!

- File list on the left side
- Editing happens in the big window
- There's a terminal window at the bottom to run things

### Weather Warnings Code

- Look at the Makefile in that folder
- Open the Terminal
- Type `npm install`
- Type `make download`

Don't do `make warnings` yet. Let's look at what this does!

- okay, when John says so, do `make warnings`

## Making a Warnings Bot

It'd be great to get notified when there are new warnings! One way to pull that off is to send new warnings to Slack. Here's how to do that ...

### Setting up Slack to receive real-time warnings

Slack is a messaging platform used by a lot of newsrooms, and it's surprisingly good at showing messages from robots. Also? This will work in a _free Slack workspace_. So even if you don't have full access to your organization's Slack system, you can do this all on your own if you want; just make a new workspace at `slack.com`.

You'll need to make a Slack _app_ (it's easier than that sounds) and get a "bot token."

The only catch is that depending on your existing Slack setup, you may need to get an administrator to approve the creation of an app. The good thing is that you are only requesting the ability to `chat:write`, which is simply posting into a channel.

OK, here's what to do:

- Do steps 1, 2 and 3 in this [Slack app quickstart](https://api.slack.com/start/quickstart)
- In step 2, "Requesting Scopes" you just need the `chat:write` scope and it will only work in channels where the bot is invited.
- The thing you want is the "Bot User OAuth Token" which always starts `oxob-`. That's the token.
- Locally, you can enter the following to test it directly into your Slack:

```
export SLACK_TOKEN=[your_token]
```

For example:

```
export SLACK_TOKEN=xoxb-123-456-abc-zyz
```

Next we need the Slack _Channel_ id.

- Go to your Slack workspace and pick (or make) a channel where you want your warnings to appear (I called mine **#alerts**.)
- In that channel, invite the bot to the channel! For example, type: `/invite @warnings_bot` (using whatever you called your bot)
- Next, get the channel ID, which you can find by clicking on the channel name at the very top of the screen.
- The ID is at the very bottom of the pop-up window, and you can click the little copy icon to copy it.
<img width="624" alt="Screenshot 2024-09-19 at 1 31 24 PM" src="https://github.com/user-attachments/assets/a6fbb5c6-844d-4ff0-b3da-6cdf90a5c6c5">

- In the terminal type:

```
export SLACK_CHANNEL=[channel ID]
```

For example:

```
export SLACK_CHANNEL=C123ABC45
```

- Now type `make slack`!

You should see something like this appear:

<img width="706" alt="Screenshot 2024-09-19 at 1 30 51 PM" src="https://github.com/user-attachments/assets/3b2d6fbc-bdc9-4867-8092-01c950e9687d">

If you click on the "reply" link, you'll see that the bot has included the details as a thread!

<img width="578" alt="Screenshot 2024-09-19 at 1 31 02 PM" src="https://github.com/user-attachments/assets/7548734f-5d8b-4308-b748-ba536c4555ea">

### Running your code as a Github Action

Github actions allow you to run your code in the cloud.

The driver of any github action is a yaml file in the `.github/workflows` directory of a repo, [like this one](.github/workflows/warnings.yml).

In short, here's what our `warnings` Github Action does:

- It starts running according to a cron statement ([here every 10 minutes(https://github.com/jkeefe/nicar2024-weather/blob/39ae476058f19021be90a70dbc59b60cef120fd5/.github/workflows/warnings.yml#L5C1-L5C103)])
- Spins up a [computer running ubuntu](https://github.com/jkeefe/nicar2024-weather/blob/39ae476058f19021be90a70dbc59b60cef120fd5/.github/workflows/warnings.yml#L12-L14).
- [Checks out](https://github.com/jkeefe/nicar2024-weather/blob/39ae476058f19021be90a70dbc59b60cef120fd5/.github/workflows/warnings.yml#L17C1-L20C25) this repo
- [Loads node.js](https://github.com/jkeefe/nicar2024-weather/blob/39ae476058f19021be90a70dbc59b60cef120fd5/.github/workflows/warnings.yml#L22C1-L32) and installs packages (or pulls them from a cache if nothing has changed).
- [Reads](https://github.com/jkeefe/nicar2024-weather/blob/39ae476058f19021be90a70dbc59b60cef120fd5/.github/workflows/warnings.yml#L48) a SLACK_TOKEN secret
- [Runs make all](https://github.com/jkeefe/nicar2024-weather/blob/39ae476058f19021be90a70dbc59b60cef120fd5/.github/workflows/warnings.yml#L56) just like we did in the terminal
- [Commits](https://github.com/jkeefe/nicar2024-weather/blob/39ae476058f19021be90a70dbc59b60cef120fd5/.github/workflows/warnings.yml#L58C1-L65C31) the new data to the repo (saving our `seen.json` for next run)

To get this working, you need to do a few key things:

**Let the Action write back to the repo**

- Settings > Actions > General > Workflow Permissions > Read and Write permissions > SAVE
- Don't forget to click "Save!"
  <img width="959" alt="Screenshot 2024-03-06 at 9 49 13 PM" src="https://github.com/jkeefe/nicar2024-weather/assets/312347/daa35671-8fec-4dde-a1d0-d769733097ca">

<img width="868" alt="Screenshot 2024-03-06 at 10 11 08 PM" src="https://github.com/jkeefe/nicar2024-weather/assets/312347/64df0600-0ae7-4857-99d0-45510ee0faa4">

**Let the Action know your Slack Token**

- Settings > Secrets and varialbes > Actions > Repository Secrets > New Repository Secret

<img width="569" alt="Screenshot 2024-03-06 at 9 41 52 PM" src="https://github.com/jkeefe/nicar2024-weather/assets/312347/3d1873cf-2bc5-420f-898d-30265864e6a9">
<img width="813" alt="Screenshot 2024-03-06 at 9 43 08 PM" src="https://github.com/jkeefe/nicar2024-weather/assets/312347/599a8e19-1cde-49ff-89b0-9c3e20d84177">

- Enter `SLACK_TOKEN` in the top box
- Paste your "Bot User OAuth Token" which always starts `oxob-` into the larger box
- Click the "Add Secret" button

<img width="912" alt="Screenshot 2024-03-06 at 9 44 53 PM" src="https://github.com/jkeefe/nicar2024-weather/assets/312347/99a855c4-46d7-482e-ad55-5123920e0a0f">

**Let the Action know your Slack Channel**

- Again, you want a New Repository Secret
- This time, enter `SLACK_CHANNEL` in the top box
- Paste your channel ID in the bottom box.

Then ... run your action:

- Actions > warnings > Run workflow dropdown > Run workflow button
  <img width="445" alt="Screenshot 2024-03-06 at 9 40 45 PM" src="https://github.com/jkeefe/nicar2024-weather/assets/312347/6db8365f-30be-4a49-9c31-5b3fa28e2068">
  <img width="589" alt="Screenshot 2024-03-06 at 9 40 54 PM" src="https://github.com/jkeefe/nicar2024-weather/assets/312347/5b535489-b742-43d5-8e8f-ea57a2533ece">

- Click the "warnings" label next to the yellow dot to watch it in action.

#### Run the action every 10 minutes

To make the bot run automatically, find the `warnings.yml` file in the `.github/workflows` folder.

Then uncomment (so remove the `#` and a single space) from lines 4 and 5. The file should now look like this at the top (the indentations matter and should be exact):

```
name: warnings

on:
  schedule:
    - cron: '*/10 * * * *'   # <-- Set your cron here (UTC). Uses github which can be ~2-10 mins late.
  workflow_dispatch:
```

#### Save your work!

You need to save your work back to your repository for it to keep after you close your browser today. Read on for how to do that.

## More details about Codespaces

#### How to save your work

This is an ephemperal instance! The instance will live in your account for a few days, but unless you take active steps, it will disappear. Which is good!

But if you make changes to the code you want to save, you need to proactively save your code back to the repository to make sure you have it. Here's how:

- Save all of the files you want to commit to your code
- Maybe even close them to make sure!
- Click the github source-control button
- Enter a commit message, like "edited readme"
- Use the blue dropdown arrow
- Pick "Commit & Sync"
- You will be warned that there are no changes staged, and do you want to stage and all of your changes. Say "Yes"

#### Closing up when you're done for the day

Running computers cost money. You get 60 hours free Codespace time every month and 15 gigabytes of storage. The Codespace will shut down after you haven't used it for a while, but but let's not waste those free minutes!

- While logged into Github, go to (github.com/codespaces)[https://github.com/codespaces]
- Pick the three dots next to the Active codespace.
- Chose "Stop codespace"
- If you forget, don't worry: It'll shut down automatically after 30 minutes. But why waste that?
- Go back to the main tab, and you'll see it's gone
- Can restart
