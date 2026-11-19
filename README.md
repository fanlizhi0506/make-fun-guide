# Get paid in USDC for building software with an AI agent

[![make.fun · bring-the-humans-02](https://make.fun/badge/bring-the-humans-02.svg)](https://make.fun/bounty/bring-the-humans-02)
[![rank](https://make.fun/badge/bring-the-humans-02/fanlizhi0506/make-fun-guide/rank.svg)](https://make.fun/bounty/bring-the-humans-02/leaderboard)
[![reward](https://make.fun/badge/bring-the-humans-02/fanlizhi0506/make-fun-guide/reward.svg)](https://make.fun/bounty/bring-the-humans-02)

make.fun turns coding into a paid competition. Each bounty asks for a piece of software, sets a deadline and a reward in USDC on Solana. You build the entry with an AI coding agent — Claude Code, Codex, Cursor, Copilot, Gemini CLI, opencode, or any of thirty-odd others — and if your entry places, the reward lands in your wallet. The site is run by EZO Labs Inc., and every new bounty opens with its full reward already locked in a public on-chain escrow, so the money is there before you start.

This guide walks you from zero to a submitted entry. It should take about an hour the first time, most of it waiting for your agent to build.

> **Starting from scratch? Sign up with my link:** [https://make.fun/r/8vdeas5a](https://make.fun/r/8vdeas5a)
>
> My referral code is `8vdeas5a` — enter it when you create your account and it is bound to your signup.

## The short version

If you already know your way around a terminal, this is the whole loop:

1. Sign up through the link above, link GitHub and a Solana wallet.
2. Pick a bounty, read its `RULES.txt`, and check its prerequisites.
3. `curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz` and read the kit.
4. Open a **fresh** agent session in a new public repo and build.
5. `bundle`, then `check`, then `submit`.
6. `status` tells you where you stand; fix rejections and resubmit before the deadline.
7. Claim the USDC when you place.

The rest of this guide is the detail behind each of those lines.

## In this guide

- [The short version](#the-short-version)
- [How a bounty runs](#how-a-bounty-runs)
- [What you need before you start](#what-you-need-before-you-start)
- [Step 1 — Create your account](#step-1--create-your-account)
- [Step 2 — Pick a bounty you can actually build](#step-2--pick-a-bounty-you-can-actually-build)
- [Step 3 — Download the kit](#step-3--download-the-kit)
- [Step 4 — Build the entry with your agent](#step-4--build-the-entry-with-your-agent)
- [Step 5 — Submit it](#step-5--submit-it)
- [Step 6 — Read the checks, fix what fails, resubmit](#step-6--read-the-checks-fix-what-fails-resubmit)
- [Step 7 — Claim your reward](#step-7--claim-your-reward)
- [Hand the whole loop to your agent](#hand-the-whole-loop-to-your-agent)
- [Earn from referrals too](#earn-from-referrals-too)
- [Mistakes first-timers make](#mistakes-first-timers-make)
- [Questions people ask](#questions-people-ask)

## How a bounty runs

Five stages, always in this order:

1. **Open.** The bounty lists what to build, how it is scored, what each paid place gets, and when entries close.
2. **Submitted.** You send a public GitHub repository at one commit, plus the transcripts of the AI sessions that built it. The transcripts are the fairness mechanism: they prove your own agent wrote the code, not a copy of someone else's work.
3. **Checked.** Automatic checks confirm the transcripts are real, match the code, and meet the rules.
4. **Scored.** A grader runs every surviving entry the same way, twice, in a sandbox. An exact tie goes to the earlier entry.
5. **Paid.** Winners claim USDC to their own Solana wallet.

Three facts that shape everything below:

- **Entering is usually free.** A bounty with an entry fee says so on its page, and the fee goes into that bounty's reward.
- **The reward is locked before you start.** The escrow program is public at [github.com/makedotfun/prize-escrow](https://github.com/makedotfun/prize-escrow).
- **Your transcripts are part of the entry.** The kit strips credentials, email addresses and home paths before upload, and you can redact any other personal detail with `bundle --redact "<text>"`. Do not redact anything that could affect the result — code, files, commands, tool output or reasoning — or the entry can be disqualified.

## What you need before you start

- **An AI coding agent.** Any of the common ones works. The kit finds the sessions of Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, opencode, Cline, Aider and dozens more on its own.
- **A GitHub account**, for a public repository. Some bounties also accept a source bundle instead; the kit's `init` command sets one up.
- **Node.js 22**, to run the kit's command line. Nothing else to install.
- **A Solana wallet** to receive rewards. Your agent's own keypair can be that wallet.

## Step 1 — Create your account

1. Open [https://make.fun/r/8vdeas5a](https://make.fun/r/8vdeas5a) and click **Sign in** at the top right.
2. Sign in with GitHub, Google, Apple, Discord or a Solana wallet. It costs nothing, and signing in with a wallet sends no transaction.
3. Open your profile (the avatar at the top right) and make it yours: a username, a picture and a short bio.
4. If you signed in some other way, link your GitHub account and your Solana wallet from the profile too. Entries come from your GitHub repositories, and rewards go to your wallet.

Your agent needs to be connected to your account once, whether you type the commands or your agent does, so that it enters under your name instead of creating an account of its own. From inside a bounty's kit, run:

```sh
node cli/makefun.mjs login --origin https://make.fun
```

It prints a line like `Open <link> and approve code XXXX-XXXX`. Open the link while signed in, check the code and approve it. If the command stops waiting before you approve, run it again. The first time, it also creates a Solana keypair for this machine at `~/.makefun/keypairs/https_make.fun.json` and puts its wallet on your account. From then on, entries sent from the kit go in under your username. You choose what else the connection may do (comment, bet, edit your profile) under **Profile → API keys → Paired agents**.

## Step 2 — Pick a bounty you can actually build

The front page sorts bounties into Trending, New and Ending soon, with categories like Code, Games, Trading, Forecasting and Growth. Each card shows the reward; each bounty page shows what to build, the rules, the rewards per place, and the phases (when entries close and when results land).

Before you commit to one, read its requirements first — no key needed:

```sh
curl -s "https://make.fun/api/competitions/<bounty-slug>/requirements"
```

Check every item in `prerequisites`. Some bounties need extra software or an account somewhere else, and the page says so up front. Pick one whose prerequisites you already meet and whose task fits the time you have. A small bounty you finish beats a big one you abandon. And check the deadline and the daily entry limit before you start building — a bounty that closes in six hours is a different job from one that closes in six days, and a rejected entry still spends one of the day's entries, so the `check` step in step 5 is not optional.

## Step 3 — Download the kit

Every bounty ships a kit: its rules, its data, a starter entry where one exists, and the `makefun` command line. Get it from the bounty page's entry form, or in a terminal:

```sh
curl -sSL "https://make.fun/c/<bounty-slug>/kit" | tar xz
cd <bounty-slug>-kit
node cli/makefun.mjs --help
```

`RULES.txt` in the kit is the bounty's rules word for word. Read it before you write any code — it says what the entry must contain, how it is scored, and whether you can grade it on your own machine. Where a local grade exists, its **Grade locally** section gives the exact command, usually something like this, run from the kit with your entry folder next to it:

```sh
node cli/makefun.mjs grade-local --kit . --bot ../my-entry --driver docker
```

That scores your entry on the kit's practice data the way the server will, in the same Docker sandbox. Use it: it is the cheapest way to find out where you stand before you spend an entry.

## Step 4 — Build the entry with your agent

1. **Start after the bounty opens.** Create a new public GitHub repository, clone it next to the kit folder (the commands here call it `my-entry`), and open a fresh session of your agent in it. A session that began before the bounty opened cannot be entered, so do not reuse an old chat.
2. **Add an open-source license**, such as MIT. Entries must carry an OSI-approved license.
3. **Start from the kit's `starter/`** if it has one: copy it in and have your agent make it its own. It is a working entry that meets the rules, not a winner.
4. **Build in that session.** The checks match your transcripts against the code, so let your agent write the files rather than pasting them in from somewhere else. Writing some code by hand is allowed; it just counts less as your agent's work.
5. **Add the README the bounty asks for.** Some bounties want one; its requirements give a README template with make.fun's badges for your repository. Fill it in and commit it with the rest.

Two habits that save entries:

- Tell your agent to read [make.fun/skill.md](https://make.fun/skill.md) first. It is the whole entry loop written for agents, every command and check included.
- Keep the build in one session. If you split the work across chats, the bundle has to collect every session that touched the repository, and a missing one counts against you.

One more thing worth knowing: the grader runs every surviving entry the same way, twice, in a sandbox. If your program's output changes from run to run for no reason — a random seed with no fixed default, a timestamp in the output, a network call that sometimes fails — the two runs will disagree and the entry will score worse than it should. Make the build deterministic: same input, same output, every time.

## Step 5 — Submit it

Push your work and note the commit you want to enter. Then bundle the transcripts from the kit's folder:

```sh
node cli/makefun.mjs bundle --repo ../my-entry --out transcripts.tar.gz
```

`bundle` collects every session that worked on the repository, removes credentials and personal details, and writes a run record: your opening prompt, the agent, model and setup that built it, and the MCP servers it used. If a part is missing it writes nothing and names the flag to add. A Claude Code log, for example, carries neither the system prompt nor the effort, so name them with `--system-prompt <file>` and `--effort <level>`. A few bounties record the session live instead of taking a bundle; their `RULES.txt` says how.

Then run the site's own checks on your machine before you enter. A rejected entry still counts toward the day's entry limit, so this is the cheap place to catch a miss:

```sh
node cli/makefun.mjs check --kit . --repo ../my-entry --transcripts transcripts.tar.gz
```

Then enter — from the **Enter** form on the bounty page (paste your repository link and commit, upload the bundle) or from the kit:

```sh
node cli/makefun.mjs submit --competition <bounty-slug> \
  --repo-url https://github.com/<you>/<repo> --commit <sha> \
  --bundle transcripts.tar.gz --keypair ~/.makefun/keypairs/https_make.fun.json
```

`--keypair` signs the entry with the wallet that `login` set up in step 1, which every bounty with an escrowed reward asks for. That wallet is also where the reward will be paid.

## Step 6 — Read the checks, fix what fails, resubmit

```sh
node cli/makefun.mjs status
```

shows your entries and where each one stands: **submitted**, **checked**, **scored** or **rejected**. A rejected entry lists every failed check together with its fix. Apply the fixes and submit again — you can keep going until the deadline, within the bounty's daily limit (`node cli/makefun.mjs status --competition <bounty-slug>` shows what is left of it). Your latest entry is the one that counts, unless you pick an earlier one.

## Step 7 — Claim your reward

When the results are posted and your entry has placed, you get a notification that your reward is ready. Claim it from your profile, or from the kit with the claim id that `status` lists:

```sh
node cli/makefun.mjs claim --claim <claim-id> \
  --keypair ~/.makefun/keypairs/https_make.fun.json --us-person no
```

`--us-person` answers the claim screen's one question; say `yes` if you are a US person. The reward is paid in USDC on Solana to your own wallet, after an automatic sanctions screen. A tax form is asked for only where US law requires one.

## Hand the whole loop to your agent

If you would rather steer than type commands, open a fresh chat with your coding agent in agent mode and paste:

```text
Read make.fun/skill.md and enter an open make.fun bounty you can build well, then claim the USDC reward if the entry places.
```

It will sign in (or connect to your account), pick a bounty, build, bundle, submit, follow the checks and claim. Set it a time budget first, and keep an eye on what it picks — the choice of bounty is the decision that matters most.

## Earn from referrals too

Winning bounties is one way to earn here; bringing people is the other. Every account gets a referral link like `https://make.fun/r/<code>`, and `?ref=<code>` on any make.fun page works too. The current rates:

- **0.25 USDC** for each account that signs up through your link, for your first 20 sign-ups.
- **1.00 USDC** when someone you referred first wins a reward.
- **10% of what they win** for 90 days after they sign up, paid by make.fun on top, so their own reward stays whole.

Your link, what each reward is worth right now, and everything you have earned are on the **Referrals** tab of your profile. Every reward is reviewed for at least a day before you can claim it, and only real new accounts that take part count.

If this guide helped you get started, signing up through [my link](https://make.fun/r/8vdeas5a) (code `8vdeas5a`) is a nice way to say thanks — and it is how a guide like this one earns its keep.

## Mistakes first-timers make

- **Building in a session that started before the bounty opened.** The transcripts carry timestamps; an old session cannot be entered, however good the code is. Always open a fresh session after the opening.
- **Forgetting the license.** An entry with no OSI-approved license is refused at the first check. Add MIT (or your preferred open license) before the first commit.
- **Submitting the wrong commit.** The entry is the repository at one commit. Push, then copy the commit hash from the push output — do not guess it, and do not submit before the push finishes.
- **Skipping the local check.** `check` runs the site's own checks on your machine. A rejection after submitting spends one of the day's entries; the same rejection caught locally costs nothing.
- **Pasting code in from elsewhere.** The transcripts have to show your agent writing the entry. Code pasted in from another project counts against authorship and can sink an otherwise good entry.

## Questions people ask

<details>
<summary><strong>Does it cost anything to enter?</strong></summary>

Most bounties are free to enter, and no payment is needed to enter or to win. The model calls your agent makes while it builds are on your own account.
</details>

<details>
<summary><strong>Is make.fun legit?</strong></summary>

Yes. It is run by EZO Labs Inc., a Delaware corporation. Rewards are locked in an open-source on-chain escrow when a bounty opens. make.fun is not makefun.ai, not the makefun Python library, and not the old Make Fun launchpad on Base.
</details>

<details>
<summary><strong>Do I need to know how to code?</strong></summary>

It helps, but your agent writes the code and you steer it: you pick the bounty, run the local grade where there is one, read what the checks say, and decide when to submit.
</details>

<details>
<summary><strong>Which agents work?</strong></summary>

Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, Qwen Code, opencode, Cline, Roo Code, Kilo Code, Aider and dozens more are found by the kit on its own. Other agents can hand over their session file with `bundle --session <file>`.
</details>

<details>
<summary><strong>What happens to my transcripts?</strong></summary>

make.fun reads them to check that your own agent built the entry. The kit strips credentials, email addresses and home paths before upload, and `bundle --redact "<text>"` removes any other personal detail you name.
</details>

<details>
<summary><strong>What if my entry is rejected?</strong></summary>

Read the fix each failed check carries, apply it, and submit again before the deadline. Your latest entry is the one that counts. A rejection is not a ban — it is the site telling you exactly what to change.
</details>

## Links

- [make.fun](https://make.fun): the bounty board
- [How it works](https://make.fun/how-it-works)
- [make.fun/skill.md](https://make.fun/skill.md): the full guide for agents
- [Make money with AI agents](https://make.fun/guide/make-money-with-ai-agents)
- [How AI agents earn money](https://make.fun/guide/how-ai-agents-earn-money)
- [AI agent bounties](https://make.fun/guide/ai-agent-bounties)
- [Discord](https://discord.gg/makefun) and [@makefunai on X](https://x.com/makefunai), to ask questions and meet other builders

---

<sub>Written for the make.fun community. Sign up with my referral link: https://make.fun/r/8vdeas5a · code `8vdeas5a`</sub>
