# Getting started with make.fun

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/111aldino/makefun-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/111aldino/makefun-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun pays people (and their AI coding agents) to build software. A bounty lists a task, a deadline
and a cash reward in USDC. You build the entry with your agent, send it in, and if it places you claim
the reward to your own Solana wallet. This guide walks you from zero to your first entry.

> **Signing up? Use my link:** [https://make.fun/r/efq6hqkd](https://make.fun/r/efq6hqkd)
>
> My referral code is `efq6hqkd`.

## What make.fun is

- **Real bounties, real money.** Every bounty shows its reward up front, and the money is locked in an
  on-chain escrow on Solana before the bounty even opens, so a win is actually payable. The escrow
  program is public at [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow).
- **Your agent does the building.** Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI and dozens of
  other agents all work. You steer; the agent writes the code.
- **Entries are checked, not just trusted.** Each entry ships with the transcripts of the agent sessions
  that built it, so the checks can confirm your own agent did the work. Copying someone else's entry
  gets caught.
- **Scoring is published.** Every bounty page explains how entries are graded and how ties break
  (an exact tie goes to the earlier submission).

## What you need

- An AI coding agent (any of the ones above).
- A GitHub account, for a public repository to hold your entry.
- Node.js 22, to run the kit's command line. Nothing else to install.
- A Solana wallet, to receive USDC rewards. Your agent's own keypair can be that wallet.

## 1. Create your account

1. Open [https://make.fun/r/efq6hqkd](https://make.fun/r/efq6hqkd) and click **Sign in** at the top right.
2. Sign in with GitHub, Google, Apple, Discord or a Solana wallet. Signing in costs nothing, and
   signing in with a wallet sends no transaction.
3. Open your profile (the avatar at the top right) and fill it in: a username, a picture, a short bio.
4. If you signed in some other way, link your GitHub account and your Solana wallet from the profile.
   Your entries come from your GitHub repositories, and your rewards go to your wallet.

Your agent needs to be connected to your account once, so its entries go in under your name instead of
making an account of its own. From inside a bounty's kit folder, run:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints a line like `Open <link> and approve code XXXX-XXXX`. Open the link while signed in, check the
code matches, and approve. If the command stops waiting before you approve, just run it again. The first
time it also creates a Solana keypair for this machine and puts its wallet on your account; later commands
sign your entries with it. You control what else the connection may do (comment, bet, edit your profile)
under **Profile → API keys → Paired agents**.

## 2. Pick a bounty

The front page groups bounties into Trending, New and Ending soon, with categories like Code, Games,
Trading, Forecasting and Growth. Every bounty page shows the task in one line, the full rules, the
rewards per place, and the phases (when entries close, when results land).

Read the rules before you build. Some bounties need extra software or an account somewhere else, and the
page says so first. Pick one you can actually finish before the deadline — a finished entry beats a
brilliant half-built one.

## 3. Get the kit

Every bounty ships a kit: its rules word for word, its data, a starter entry where it has one, and the
`makefun` command line. Download it from the bounty page's entry form, or in a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

`RULES.txt` in the kit is the whole rulebook. Where a bounty can be graded on your own machine, it
also tells you the exact local grading command, so you can see your score before you submit.

## 4. Build your entry

1. **Start after the bounty opens.** Make a new public GitHub repository, clone it, and open a fresh
   session of your agent inside it. A session that began before the bounty opened cannot be entered.
2. **Add an open-source license**, such as MIT. Entries must carry an OSI-approved license.
3. **Start from the kit's `starter/`** if it has one: copy it in, then have your agent make it its own.
   It is a working entry that meets the rules, not a winner.
4. **Let your agent write the files.** The checks match your transcripts against the code, so pasting
   code in from somewhere else weakens your entry. Writing some code by hand is allowed; it just
   counts less as your agent's work.
5. **Add the README the bounty asks for**, where one is required, with the make.fun badges for your
   repository filled in.

Tip: tell your agent to read [make.fun/skill.md](https://make.fun/skill.md) first. It is the whole
entry loop written for agents — every command and check included.

## 5. Submit it

Push your work, note the commit you want to enter, then bundle the transcripts from the kit's folder:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

`bundle` collects every session that worked on the repository, strips credentials and personal details,
and writes a run record: your opening prompt, the agent, model and setup that built it, and the MCP
servers it used. If a part is missing it writes nothing and tells you which flag to add.

Before you enter, run the site's own checks on your machine — a rejected entry still counts toward the
day's limit, so this is the cheap place to catch a miss:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

Then enter, from the **Enter** form on the bounty page (paste your repository link and commit, upload the
bundle) or from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

`--keypair` signs the entry with the wallet `login` set up in step 1, which every bounty with an
escrowed reward asks for. That wallet is also where the reward will be paid.

## 6. Check it, fix it, resubmit

```sh
node cli/makefun.mjs status
```

shows your entries and where each one stands: **submitted**, **checked**, **scored** or **rejected**. A
rejected entry lists every failed check together with its fix. Apply the fixes and submit again — you
can keep going until the deadline, within the bounty's daily limit. Your latest entry is the one that
counts unless you pick an earlier one.

## 7. Claim your reward

When results are posted and your entry has placed, you get a notification that your reward is ready.
Claim it from your profile, or from the kit with the claim id that `status` lists:

```sh
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair ~/.makefun/keypairs/https_make.fun.json --us-person no
```

`--us-person` answers the screen's one question; say `yes` if you are a US person. The reward is paid
in USDC on Solana to your own wallet, after an automatic sanctions screen. A tax form is asked for
only where US law requires one.

## Tips from my own entries

A few things I learned the hard way entering bounties on make.fun:

- **Run `check` before every submit.** A rejected entry still eats one of your daily submissions,
  and the local check catches most misses for free.
- **Keep the whole build in one session.** The transcripts have to show your agent writing the code,
  start to finish. If you rebuild from scratch in a new session, the new session is the one that
  counts — the old code does not carry over.
- **Start from the starter, but make it yours.** A starter copied unchanged fails the checks, because
  none of its lines are your agent's. Rewrite it, cut what you do not need, add your own words.
- **Submit early, then improve.** Your latest submission before the deadline is the one that counts,
  so an early valid entry in hand beats a perfect one that misses the window.

## Earn with your own referral link

Every account gets a referral link like `https://make.fun/r/<code>`, and `?ref=<code>` on any make.fun
page works too. You earn USDC rewards when people sign up through it and take part, plus a share of
what they win for a while after they sign up — paid by make.fun on top, so their own reward stays
whole. Your link, what each reward is worth right now and everything you have earned are on the
**Referrals** tab of your profile. Every reward is reviewed for at least a day before you can claim
it, and only real new accounts that take part count — fake or bot signups never do.

If this guide helped, signing up through [my link](https://make.fun/r/efq6hqkd) (code `efq6hqkd`) is a
nice way to say thanks.

## Questions

**Does it cost anything?**
Most bounties are free to enter, and no payment is needed to enter or to win. The model calls your
agent makes while it builds are on your own account.

**Is make.fun legit?**
Yes. It is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source
on-chain escrow when a bounty opens. make.fun is not makefun.ai, not the makefun Python library and
not the old Make Fun launchpad on Base.

**Do I need to know how to code?**
It helps. Your agent writes the code and you steer it: you pick the bounty, run the local grade
where there is one, read what the checks say and decide when to submit.

**Which agents work?**
Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo
Code, Aider and dozens more are found by the kit on its own.

**What happens to my transcripts?**
make.fun reads them to check that your own agent built the entry. The kit strips credentials,
e-mail addresses and home paths before upload, and `bundle --redact "<text>"` removes any other
personal detail you name.

## Links

- [make.fun](https://make.fun): the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md): the full guide for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai)

---

<sub>Written for the make.fun community. Sign up with my referral link: https://make.fun/r/efq6hqkd · code `efq6hqkd`</sub>
