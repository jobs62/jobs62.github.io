# Software Engineering Is a Game of Loops

## The myth of the big bang

There's this fantasy version of engineering where you think hard, design carefully, build the thing, ship it, and it works. One clean arc from intent to outcome.

For changes that are cheap to reverse (so, not your database schema), I've never seen it happen. Not once.

What actually happens is loops. Tiny, annoying, repetitive loops. Attempt → feedback → swear → adjust. That's the job.

## The loop stack

Your codebase is sitting under a pile of validation loops, each one slower and more expensive than the one below it:

- **Editor / LSP** — ~100ms. Typos, type errors, missing imports. The free stuff.
- **Compile** — seconds. The file you forgot to save, the dep that broke overnight.
- **Local tests / static analysis** — seconds to minutes. The thing you just broke while "just refactoring a little."
- **CI** — minutes. What your laptop lied about. Different OS, flaky test, the env var you have locally and nobody else does.
- **QA / staging** — hours to days. Someone clicking buttons in an order you never imagined.
- **Production** — days to weeks. The browser nobody tests, the load you can't fake, real users with real garbage data.

A type error in the editor costs you three seconds. The same type error in production costs you a hotfix, a rollback, a postmortem, and that Slack thread where someone asks "how did this get through?" like it's a mystery.

It's not a mystery. The inner loops were asleep.

## Catch it in the tightest loop that can see it

This is the whole game: every defect should die in the cheapest loop capable of killing it.

- Missing import? LSP. Not CI.
- Type mismatch? Compiler. Not a code review comment.
- "We agreed not to do X"? Make it an `enum`, not a wiki page.
- Something a unit test can prove in 50ms? Don't wait for CI to ask the same question in 8 minutes.
- Flaky tests people just re-run? Those are worse than no tests. They teach the monkey to ignore the signal.

You're not trying to make every loop catch everything. You're trying to make each loop honest about what it's supposed to catch, so the next one up isn't drowning in noise.

## The ten-day loop (aka how features go to die)

Worst pattern I know isn't bad architecture. It's a feedback loop so long it turns into a cliff.

You've seen this:

1. Someone works a feature branch for a week.
2. Pushes. CI red. Fixes. CI green.
3. Review takes two days. Comments. More fixes.
4. Merges. QA finds three regressions. Back to square one.
5. Ten days later the feature is "done," the original context is gone, the author has already context-switched to something else, and the fix is a patch on a patch on a shrug.

That's not a process problem. The inner loops (editor, local build, local tests) were either missing, slow, or untrusted. So everything fell through to the expensive loops. The team isn't slow because the engineers are dumb. They're slow because their tight loops are broken and everything cascades outward.

## Fast teams don't have faster people

They have shorter loops.

When the LSP catches most of the dumb stuff before you even save, local tests catch the next chunk before you push, and CI stops being the place where obvious mistakes are discovered and becomes independent confirmation that the change works in a clean, standardized environment, QA gets to do actual QA instead of being a human fuzzer for typos. Production incidents start being interesting instead of embarrassing.

Each loop you tighten doesn't just save time on the bugs it catches. It lets every loop above it stop babysitting and start doing the hard work.

## LLMs don’t change this. They make it worse if you’re sloppy.

Agentic coding, copilots, "here's a PR the bot wrote while you slept", none of it retires the loop stack. It just puts a very fast, very confident dumbass at the wheel.

LLMs are incredible at producing code-shaped text. They are not incredible at knowing whether that text is correct, safe, or even what you asked for. It will happily hallucinate an API that doesn't exist, "fix" a race condition by deleting the lock, or refactor three files and quietly break the fourth one it didn't read.

LLMs accelerates whatever loop culture you already have. Tight loops → faster shipping. Leaky loops → faster production incidents and a repo full of plausible nonsense.

## What actually helps

1. **Editor setup is not optional.** Strict LSP, real-time lint, format-on-save. Make it the default, not a wiki page of "recommended settings." If half the team has a worse feedback loop than the other half, you've already lost.

2. **Make the build fast or people won't run it.** If `tsc` or `clippy` takes 10 minutes, it might as well not exist. Two seconds and people run it constantly. Build speed is a product feature for your own team.

3. **If you can't run the test locally, you don't have a test. You have a CI prayer.** CI-only checks are how you invent 10-minute loops for 5-second questions.

4. **Kill flaky tests or quarantine them.** "Just re-run it" is how you train everyone to ignore red builds. A lying loop is worse than no loop.

5. **Stop making humans do robot work.** Formatting, import order, license headers, basic lint. if a reviewer is still leaving style comments, your automation is incomplete.

6. **Small PRs. Seriously.** A 30-line change gets real review in minutes. A 4k-line monster gets skimmmed in days and everyone pretends they read it. Stacked diffs help if your tools don't suck.

## The only philosophy that matters

You're not trying to get it right the first time. You're trying to get it wrong as cheaply and quickly as possible.

Every line you write is a hypothesis. The loops are the experiment. A tight loop says "let me check this in two seconds." A loose one says "let's find out next Thursday." One of those compounds. The other produces surprises at 2am.

Your LSP won't catch business logic bugs. Your unit tests won't catch that the button is ugly on mobile. That's fine. Inner loops exist to filter noise so the slow, expensive, human loops can actually look for signal.

Tighten the loops. Make them trustworthy. And for the love of god, don't let a typo make it to code review. That's just rude.
