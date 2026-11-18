# Publication identity checks

Anyone can contribute. External contributors may use their own Git name and email, including a personal address. SugarCasino members must use a GitHub noreply address. The checks enforce these identity and attribution rules without requiring a contributor allowlist. Authentication and code review remain separate.

A personal address fails the check for a recognized SugarCasino member. External contributors receive a warning, and the address is never printed. Use a GitHub noreply address if you want to keep your email private.

The checks apply separately to authors, committers, annotated taggers, and attribution trailers. This lets a member commit or review an external contribution while preserving its original author. Existing history stays unchanged.

Use the Node version in `.node-version` and install the hooks with `pnpm install --frozen-lockfile`. They check your effective Git identity, commit messages, and the commits you push. Refresh remote refs before pushing a new branch.

The `Publication privacy` workflow checks PR commits, titles and descriptions, pushed commit ranges, and annotated tags. It runs on `pull_request`, including fork PRs, with read-only repository permissions. For normal pushes, it checks the incoming commit range. For new branches and rewritten history, it checks changes since the merge base with main. A forced push can still be checked when the old tip is no longer available. Checkout provides the full history (`fetch-depth: 0`); missing history needed for the comparison or a missing merge base fails the check.

A manual run checks changes on the selected branch against main. History already shared with the base is skipped. Use `--all` to audit the full history.

## Member identification

`.github/publication-members.json` lists the members whose identities need protection, using their GitHub logins and known Git names. Update it when membership changes or a member uses another Git name. The initial logins, checked against the organization membership API, are `FakieSui` and `Furbor`.

Local hooks identify members by Git name or GitHub noreply login. CI also checks the GitHub account linked to each commit's author and committer through the read-only repository API. Changing a Git name therefore does not bypass the rule when GitHub can link the account. A missing or failed API response fails the check without exposing personal data.

For unlinked accounts and attributions, the checker can only use known Git names. Local hooks cannot identify a member from an unknown personal email address.

The workflow uses the read-only `GITHUB_TOKEN`, including for fork PRs. No organization-members secret or organization write access is needed. Failed checks leave commits unchanged.

## Maintainer review

SugarCasino maintainers review contributions before merging. Configure required approvals in GitHub branch protection or repository rulesets, using designated maintainers or a CODEOWNERS team. Passing the identity checks grants neither write access nor PR approval; the scripts leave those GitHub permissions to the repository settings.

## Validation

Tests use synthetic identities and local Git fixtures:

```sh
node --test scripts/validate-git-identities.test.mjs scripts/check-publication-event.test.mjs
node scripts/validate-git-identities.mjs --config
```
