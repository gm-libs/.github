# Contributing

These are the working conventions in force across every repository this organization owns. They are not aspirational, they are what the existing history looks like, and a change that ignores them will be sent back.

Every repository here is a library, or the tooling that builds one. Read the library's own `README.md` first, and its `AGENTS.md` or `CLAUDE.md` where it has one. `AGENTS.md` and `CLAUDE.md` are instruction files for coding assistants, `AGENTS.md` being the format most tools read and `CLAUDE.md` the one Claude reads, and both double as the fastest description of how that repository expects to be worked on. A repository may add rules on top of these but never drops them.

## Start with an issue

Work begins with a GitHub issue, not with a branch. The issue says what is wrong or what is missing, why it matters and roughly what order the work should go in. The pull request that follows closes it.

Anything non-trivial gets a plan before it gets code. Understand the actual problem, read the existing code rather than assuming what it does, then write down what you propose to do and wait for agreement. A typo or a one line fix skips the plan, but still says what it is doing before doing it. The point is that nobody discovers the approach for the first time in a diff.

If you find a second problem while working on the first, file it as its own issue. A finding mentioned only in a comment thread is lost the moment the thread scrolls. Finish what you are on, then write the finding up somewhere it will be picked up again.

## Branches

Cut from a fresh default branch every time. Pull first, then branch.

```
<handle>/<type>/<kebab-title>
```

`<handle>` is your own GitHub handle, shortened if it is long: `geniusmonir` branches as `gm`, so `sarahchen` might branch as `sc` or `sarah`. Pick one form and keep using it, so the branch list stays sorted by person.

`<type>` is one of `feat`, `fix`, `refactor`, `chore`, `docs` or `test`. `<kebab-title>` is three to five words in kebab-case. `gm/fix/retry-after-header` and `gm/docs/entry-point-table` are the shape.

**Never force push to a default branch, and never rewrite its history.** On your own branch, before review, rewrite freely. Once someone has reviewed it or built on it, stop. On the public repositories this is enforced by a ruleset that rejects the push outright. On the private ones GitHub's Free plan cannot enforce it, so it rests on you.

## Commits

One line. [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/), `<type>(<scope>): <description>`, with the type matching the branch prefix. Imperative mood, so "fix retry header" and not "fixed retry header". The scope is the area the change touches.

```
feat(logger): redact bearer tokens
fix(client): parse Retry-After as seconds and as a date
chore(deps): bump pg from 8.11 to 8.12
```

There is no commit body. If a change needs explaining, the explanation belongs in the pull request, where it can be read alongside the diff and found again later. The changelog is generated from these lines at release time, so write the line for the person reading the changelog.

## Naming

- **camelCase** for variables, functions and file names, unless the framework or the repository says otherwise.
- **PascalCase** for React and Vue component files, matching the component inside them.
- **kebab-case** for branch titles, package subpaths and anything else that has to survive being lowercased.
- **SCREAMING_SNAKE_CASE** for environment variables and constants.

When an existing directory already does something else consistently, follow the directory. A file that matches its neighbours is worth more than a file that matches this list.


## Packages are versioned, every release bumps

Every change that ships bumps the package version, even a tiny one, because package managers cache by version and a rebuilt package under an old number is invisible to every consumer that already has it.

Releases are cut by the release workflow, which runs the gate, tags, and publishes an immutable version. Never publish by hand. Never commit a packed tarball, and never vendor a package into a consumer: a consumer pins the exact version it was tested with, and a range in a consumer's manifest is a bug report waiting to happen. The first release of a new package needs setup outside the repository, so ask before cutting it.

## AI assistance, declared

Coding assistants are fine to use here. What is not fine is a reviewer being unable to tell.

**Say which assistant you used, in the pull request body.** One line, the tool and the model, for example `AI assistance: Claude Code, Opus 5` or `AI assistance: none`. That is all. It changes nothing about how the change is reviewed, and it is not a confession: it tells whoever reads the diff what kind of mistakes to look for, which is a different set for generated code than for hand-written code. A pull request that does not say is assumed to have used one.

**A `Co-authored-by` trailer naming the model is welcome on the commit.** Welcome rather than tolerated, because it puts the fact where the record lives. It does not replace the pull request line, since a trailer on one commit says nothing about the other six and the pull request body is what gets read before the diff. What does not belong in a commit message is a generated-with footer or any other tool advertisement, which is marketing rather than attribution. The repository owner's own commits carry no trailer, by preference on his own history, so do not read the existing log as contradicting this. You are responsible for what you submit either way: a model wrote it, you are shipping it.

## Pull requests

Prefer one substantial pull request per section of work, with one commit per task inside it, over a scatter of tiny pull requests. Reviewing five related changes together is faster than reviewing them five times.

The body is prose. Lead with the reason the change exists, then what it does, then how you know it works. A short bold lead-in per item is fine when several distinct things changed. A checklist of ceremony is not what anyone here is reading for, and neither is a restatement of the file list that GitHub already shows.

**Anything that changes what a user sees needs a picture.** A UI kit component, a chat surface, a backup dashboard: a screenshot for a static change, a short screen recording for anything with motion, a state change or more than one step. Before and after, when there was a before.

**Show how it was verified.** The command that was run and what it returned, the case that used to fail and now does not, or the smoke test output. "CI is green" on its own is not verification, it is the floor.

Reference the issue at the top, in the shape the template gives you. `Closes #12` is what actually closes the issue when the pull request merges, so use `Closes` when the pull request finishes the work and `Refs` when it moves a larger piece along without completing it.

Write for someone who cannot see your machine. No absolute paths from your disk, no "the file I attached", no internal shorthand, no plan-document vocabulary that exists nowhere in the package. Repository relative paths and plain description only.

## Tests and CI

Code ships with its tests in the same pull request. A module gets unit tests, an exported entry point gets at least a smoke import, and a bug fix gets the case that used to fail. Test effort follows risk, so paths that touch money, permissions, signing or data integrity get the thorough treatment and the rest gets something proportionate rather than ritual.

A library that talks to Postgres, Redis or a browser tests against the real thing, in a container or a real Chromium,. Red CI means no merge. Skipped tests need a linked issue explaining when they come back.

Gate on exit codes, never on matched output. A chain like `run-tests | grep -E 'passed|failed' && git commit && git push` reports success whenever grep finds its line, failing or not. Run the checks, keep the real status, continue only when it is zero.

## Documentation moves with the code

If a change makes an existing document wrong, that document is fixed in the same pull request. No follow-up issue for it, no "TODO: update README". This covers the README's entry-point table, the changelog, usage sections when an export, option or environment variable changes, and anything under a docs directory.

The rule exists because the alternative has been tested. A README that describes an export that no longer exists is worse than no README, since a consumer will import it.

## Scripts that change data

Any script that writes, deletes, migrates or backfills reports by default and acts only when passed `--execute`.

The flag is `--execute`. Not `--write`, not `--apply`, not `--commit`, not `--yes`. They all mean the same thing, and a set of runbooks that mixes them means the person about to paste a line into a production shell has to go and look one up before it is safe to type.

The shape, every time:

- **No flag prints the report and changes nothing.** The first run of anything is usually pointed somewhere unintended.
- **Name the target before reporting any counts.** The database, the registry, whatever the script actually reached. A confident success reported against the wrong copy is worse than an outright failure.
- **`--execute` does the work**, in one transaction where the store has them, and re-counts afterwards so the outcome is verified rather than assumed.
- **The header comment states the flag**, so the usage is readable from the source and not only from whatever document happens to be open.

Flags belonging to another program are that program's vocabulary and are out of scope. Anything passed through to a third party tool is left exactly as it is.

## How things are written

Two rules apply to every piece of text produced here: documentation, README files, changelog lines, commit messages, issue bodies, pull request bodies and code comments.

**Never an em dash.** A comma replaces it, or a full stop where it was holding two independent clauses apart, since a comma between two of those is a splice. Not a hyphen: a hyphen joins words. Often the sentence that reached for a dash wanted to be two sentences, so restructure rather than substitute. The en dash stays only where it means "to" or "between", as in a year range or a route. The gate fails the build on an em dash in tracked text, so this is checked rather than trusted.

**Never hard wrap markdown.** One paragraph is one line, however long it runs. No manual breaks inside a paragraph, a list item, a table cell or a blockquote. These files are read in a viewer that wraps on its own, and a file wrapped at eighty columns renders as a narrow ribbon with half the page empty. The only line breaks are the ones between blocks, and the ones inside fenced code where they carry meaning. If you touch a hard wrapped paragraph, unwrap it.

Beyond those two: no decorative comments in code, no narration of what the next line obviously does, and no assistant register in prose. No "I'd be happy to", no "Certainly", no closing paragraph that restates the opening, no stacked triads of adjectives. Engineer to engineer, and dense.

## Secrets

No secrets in git, ever. Committed configuration is an example file with placeholder values. Real credentials are injected at build or deploy time and live in a secret store, never in a repository, a pull request body, an issue, a screenshot or a log paste. The gate fails on a committed environment file.


## Ask before you do these

- Force push or rewrite history on a shared branch, and never on a default branch at all.
- `--no-verify` on anything.
- Pushing tags, running the release workflow, or cutting the first release of a new package.
- Changing the shared CI or release workflow inside a single library rather than where it is maintained.
- Adding a dependency to a package that was a peer, or adding a peer at all.
- Adding a new entry point, since every consumer's import surface grows with it.

A yes takes one message. Undoing any of them takes considerably longer.

## Questions

Open an issue in the repository it concerns, or write to [operations@webanion.com](mailto:operations@webanion.com). Security reports go the private route instead, see [SECURITY.md](SECURITY.md).
