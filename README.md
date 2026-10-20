# Build your own TikTok analytics dashboard with Claude

This is the full setup for the dashboard from my video: Claude's Dashboard feature does the building, and Apify pulls your TikTok data in. Give it about an hour, plus some back and forth with Claude.

## What you'll end up with

A dashboard with four tabs that tells you what's working and what to post next, not just how many views you got.

| Tab | What it shows | Why it's useful |
| --- | --- | --- |
| Performance | A follower goal tracker, your baseline stats (median views, engagement, save, share and follow rates), performance by topic, format and tone, and every post in a searchable table | Shows which types of content are worth doing more of, not just what got lucky |
| What to post next | Content ideas based on what people keep asking for in your comments, plus a trend radar for your niche | Turns your comments into a content plan |
| Brand and audience | Your content lanes with a target mix, your tone of voice, and who's commenting and about what | A reference so you're not starting from scratch every time you make something |
| Newsletter (optional) | Signups over time and which posts drove them | Shows the posts that grow your audience off TikTok, even when their views look low |

## What you need

1. **A paid Claude plan** with the Dashboard feature. It's in beta and still rolling out, so check you can see it before you start.
2. **An Apify account.** This is the part people miss. Apify is what scrapes your public TikTok posts and comments so Claude has something to analyse. The free plan is enough.
3. **The Apify connector added to Claude**, so Claude can run Apify for you (Step 1).
4. **Your TikTok Studio data** for the numbers Apify can't see: saves, watch time, new followers per post and follower demographics (Step 2).
5. **Optional:** exports from your newsletter platform (I use Beehiiv) if you want signup tracking.
6. **Optional, for the weekly recap:** an n8n account and a Telegram bot. More on that below.

## Step 1: Connect Apify to Claude

1. Sign up at [apify.com](https://apify.com).
2. In Claude, open **Settings, then Connectors**.
3. Find **Apify** in the list and connect it. If it isn't listed, add a custom connector with the address `https://mcp.apify.com`.
4. Sign in to your Apify account when it asks.

To check it worked, start a new chat and ask Claude: "Use Apify to get my latest 5 TikTok posts from @yourhandle." If you get your posts back, you're set.

## Step 2: Export what Apify can't see

Apify only sees what's public: views, likes, comments, shares and follower count. The numbers that tell you the most, like saves, watch time and new followers per post, only live in TikTok Studio.

1. Open **TikTok Studio** on desktop and go to **Analytics**.
2. Download what you can from the **Overview**, **Content** and **Followers** sections.
3. If a number can't be downloaded, screenshot it or paste it into the chat and ask Claude to turn it into a table.

## Step 3: The prompt

Paste this into a new Claude chat, fill in the brackets and attach your TikTok Studio files.

```
Using my Apify connector, scrape my public TikTok profile [@yourhandle]: all posts since [date] with views, likes, comments and shares, plus the comments on those posts. I've also attached TikTok Studio exports for saves, watch time, new followers per post and follower demographics, which Apify can't access. Tag each post by topic, format and tone.

Then build me a content analytics dashboard using Claude's Dashboard feature, with these tabs:

1. Performance: a follower goal tracker (my target is [10,000] followers by [31 Dec]; show where I'm at, weeks left and how many new followers I need each week), a summary of my posts (median views, engagement rate, save rate, share rate, follows per 1,000 views), performance broken down by topic, format and tone, a date filter on my account totals, and a searchable table of every post.
2. What to post next: content ideas based on what people repeatedly ask for in my comments, and a trend radar of recent news in [my niche] matched to what my audience asks about.
3. Brand and audience: my content lanes with a target mix, my tone of voice and rules, and the themes and types of people coming up in my comments. Here's how I describe my brand: [a few sentences about what you post, who for, and how you want to sound].
4. Newsletter (optional): signups over time and which posts drove them, from my attached [Beehiiv] exports.

Treat my uploaded files as data I'll refresh myself, not a live connection.
```

## Step 4: Iterate

Your first version won't be perfect, and that's normal. Mine took a few rounds. What usually comes up:

- **A section shows grey boxes or dashes that never load.** Tell Claude which section and ask it to check the page code fills that section. This happened to me with the goal tracker, and it was a one-line fix.
- **Topics are tagged wrong.** Give Claude your own topic list and ask it to re-tag every post.
- **A number looks off.** Click it on the dashboard to see where it came from, then tell Claude what you expected.
- **You want something added.** Just ask, the same way you'd ask a person. I added the goal tracker and the date filter after the first version.

## Optional: a weekly recap on Telegram

This is a separate automation, so you don't have to open the dashboard to see how your week went. Every Saturday morning I get a message with my weekly views, likes, comments and shares, my top post, my follower change since last week, how I'm tracking against my goal, and one thing to focus on next.

You'll need:

1. An **n8n** account, with your **Apify account connected in n8n** as well. It's a separate connection from the one in Claude.
2. An **Anthropic API key** added to n8n.
3. **Telegram**, plus a bot: message **@BotFather**, send `/newbot` and keep the token it gives you. Then message **@userinfobot** to get your chat ID. Send your new bot one message before you test, or it can't message you back.

Then ask Claude (with n8n connected) or n8n's own AI builder:

```
Build an n8n workflow that runs every [Saturday at 8am]. Use an AI agent with the Apify MCP tool to scrape my public TikTok profile [@yourhandle], and calculate my views, likes, comments and shares for the last 7 days, my top post, and my follower change since last week (store each week's follower count in an n8n Data Table so it can compare). Include how I'm tracking against [10,000] followers by [31 Dec] and one thing to focus on next week. Send it to me on Telegram as plain text.
```

Add your Telegram token in n8n yourself (n8n won't let Claude create credentials) and paste your chat ID into the Telegram node.

## Good to know

- **The dashboard doesn't sync constantly.** It's built from your Apify scrape and your exports, so it updates when you refresh them. Ask Claude to re-run the scrape and swap in new exports every week or two.
- **The weekly recap is live.** It scrapes fresh numbers every time it runs.
- **Saves only come from TikTok Studio.** No public scraper can see them, so they only update when you re-export.
- **Costs:** Apify charges per result, but scraping one profile costs fractions of a cent, so the free plan's monthly credit covers it.
