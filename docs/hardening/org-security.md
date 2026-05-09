# GitHub Organization Security

> This page is for anyone running or working inside a GitHub organization — developers, team leads, CTOs, and non-technical founders alike. You don't need to be technical to understand this. But you *do* need to understand it.

---

!!! abstract "TL;DR"
    - A GitHub Organization is not the same as a personal account — it's a shared workspace with its own security controls
    - Owner access should be limited to 2-3 people maximum — never give it out casually
    - Outside Collaborators are for contractors and freelancers — not full team members
    - Revoking access when someone leaves is not automatic — you have to do it manually
    - Deploy keys survive member removal — check them every time someone leaves
    - The audit log exists and you should be checking it

---

## What Is a GitHub Organization

If you have a personal GitHub account, you already know what that looks like — one user, your repos, your settings. A GitHub Organization is different. It's a shared account that a team, company, or project lives under. Instead of repos living under `github.com/yourname`, they live under `github.com/yourcompany`.

Organizations give you things a personal account can't:

- Multiple owners and members with different permission levels
- Teams that map to your actual team structure
- Org-wide security settings that apply to every repo at once
- An audit log that tracks who did what and when
- Centralized billing, secrets management, and access control

**This isn't just for big companies.** If you're a solo founder hiring one developer from Fiverr to build a feature, you need a GitHub Organization. If you're a freelancer bringing in a subcontractor for one project, you need a GitHub Organization. The moment someone else touches your codebase, you need to control what they see, what they can push to, and what happens when the work is done. Organizations give you that control regardless of your company size.

**Who needs a GitHub Organization:**

- Any company or startup with more than one developer
- Any open source project with multiple maintainers
- Any solo developer who plans to hire or collaborate
- Anyone hiring contractors, freelancers, or agency developers — even for a single project

If you're building something that will eventually have a team, start the org now. Migrating repos from personal to org later is annoying. Starting right is free.

**One important distinction:** Your personal account still exists alongside the org. You use your personal account to log in and then act as a member or owner of the org. The org itself is a separate entity — it has its own settings, its own billing, its own security controls. Changes you make to your personal account settings don't automatically apply to the org.

**GitHub pricing — what you need to know:**

At the time of writing:

- **GitHub Free** — basic org features, public repos, limited private repo features
- **GitHub Team** — ~$4/user/month — unlocks most org security features, branch protection rulesets, required reviewers, and more. This is where most small teams should be.
- **GitHub Enterprise Cloud** — ~$21/user/month — adds SAML SSO, audit log API access, advanced compliance controls, internal repo visibility, and enterprise-grade policy enforcement
- **GitHub Enterprise Server** — ~$21/user/month — self-hosted version of Enterprise Cloud

For most small to medium teams, **GitHub Team** gives you everything covered in this page. Enterprise features are called out specifically where they apply.

---

## Roles — Who Can Do What

This is the most confused topic in GitHub org management. Here's the plain English version.

### Owner

Full administrative access to everything. Can change org settings, add and remove members, delete repos, manage billing, and override any permission. This is the most powerful role in the org.

**Rules:**
- Always have at least 2 owners and no more than 3 — you need a backup in case one account is compromised or locked out, but the fewer owners the smaller your attack surface
- Owner access should never be given to contractors, freelancers, or temporary collaborators
- Never give owner access casually — it's the keys to everything

!!! warning "If you are the sole owner of your org"
    Create a completely separate personal GitHub account — not connected to your main email, not used for anything else — and add it as a second owner. Store those credentials securely and offline (a password manager or written down in a physically secure location). This is your break-glass account. If your primary account is ever compromised, hijacked, or locked out, you can still access and recover your org. A separate account that has never committed code and has no activity is far less likely to be targeted. This is not optional if you care about business continuity.

### Member

The default role for everyone on your team. Members can access repos based on the base permissions and team assignments you set. They can create repos, open issues, and collaborate — but they can't change org-level settings.

This is the right role for: full-time employees, long-term collaborators, core team members.

### Outside Collaborator

A person who has access to one or more specific repos but is **not** a member of the organization. They don't appear in your org's People tab as a member. They can't join teams. They can't see other org repos unless explicitly given access.

This is the right role for:
- Contractors and freelancers working on a specific project
- Consultants brought in temporarily
- Agency developers working on one repo
- Anyone you want to give limited, scoped access without bringing into the full org

!!! warning "The most common mistake"
    Making contractors full org Members instead of Outside Collaborators. A member can see all your org's repos (depending on base permissions), join teams, and has broader access than you probably intend. When the contract ends and you forget to remove them — and you will forget — they still have that access. Outside Collaborators are scoped to specific repos and are easier to clean up.

**Real world decision guide:**

| Who | Role |
|---|---|
| Full-time employee | Member |
| Co-founder | Owner or Member |
| Long-term contractor (6+ months, core team) | Member |
| Freelancer on one project | Outside Collaborator |
| Agency developer | Outside Collaborator |
| Consultant reviewing code | Outside Collaborator (Read only) |
| Someone you're not sure about | Outside Collaborator until you're sure |

**How to add an Outside Collaborator:**

1. Go to the specific repo they need access to
2. `Settings → Collaborators and teams → Add people`
3. Search their GitHub username or email
4. Select the permission level (Read, Triage, Write, Maintain, or Admin)
5. Send the invite — they'll receive an email and must accept before access is granted

**How to add an Org Member:**

1. `Your org → People → Invite member`
2. Search their GitHub username or email
3. Select their role (Member or Owner)
4. Send the invite — they accept via email
5. After they join, add them to the appropriate teams

**How to remove a Member before you need to:**

Know this before you're in a hurry. When someone leaves:

1. `Your org → People`
2. Find the member
3. Click the three dots (`...`) next to their name
4. Select `Remove from organization`
5. Confirm — they lose org membership immediately

Then go through each repo and check for any direct collaborator access they had outside of team membership. Teams are removed automatically — direct repo access is not always.

---

## Base Permissions — The Setting Nobody Knows Exists

Base permissions is an org-wide setting that controls the default level of access every member has to every repository in your org. Most organizations never change this from the default and end up either too permissive or confused about why people can see things they shouldn't.

**Where to find it:**
`Your org → Settings → Member privileges → Base permissions`

**The options:**

- **No permission** — members can only access repos they've been explicitly added to
- **Read** — members can view and clone all repos (default on most orgs)
- **Write** — members can push to all repos
- **Admin** — members have admin access to all repos (almost never appropriate)

**What to set it to:**

For most small teams: **Read** is reasonable — people can see everything but can't push without explicit permission.

For security-conscious orgs or orgs with sensitive IP: **No permission** — members only see what they're explicitly given access to.

For any org: **never Write or Admin** as the base — that means every new member you add immediately has write or admin access to every repo in your org before you've intentionally given them anything.

!!! tip "For small companies and sole proprietors"
    Set base permissions to **No permission** and assign access explicitly as needed. It takes a little more setup but means nobody ever accidentally has access to something you didn't intend. Every new hire, every contractor, every collaborator starts with nothing and gets only what they need for their specific role.

    If someone pushes back and demands higher access than their role requires — pause. Ask them to explain specifically why they need it. Document the reason. Legitimate contributors almost never need admin access to do their job. Someone who is insistent about needing elevated permissions without a clear technical reason is worth paying attention to. It may be nothing. It may not be. Either way, require them to show you exactly why the access is necessary before granting it. "I just work better with more access" is not a reason.

---

## Teams — Why They Exist and How to Use Them

A GitHub Team is a group of org members that you can assign permissions to collectively. Instead of adding five developers individually to twelve repos, you create a team, add the developers to the team, and give the team access to the repos. One change — everyone on the team gets it.

**Why teams matter for security:**

- When someone joins, add them to the right teams and they have the right access automatically
- When someone leaves, remove them from all teams and their access disappears everywhere at once
- Teams map to your actual structure — `frontend`, `backend`, `devops`, `security`
- Team access is auditable — you can see exactly which teams have access to which repos

**The catch:** Outside Collaborators cannot be added to teams. Teams are members only. If you have a contractor who needs access to multiple repos, you either make them a member (not recommended unless appropriate) or add them to each repo individually as an outside collaborator.

**Creating a team:**
`Your org → Teams → New team`

Give it a name that matches your actual team structure. Add members. Then go to the repos that team needs and add the team with the appropriate permission level.

---

## Inviting Access — Doing It Right

When you invite someone to your org or a repo, think before you click.

**For org membership:**
`Your org → People → Invite member`

Before sending the invite, decide:
- Should this person be a Member or Outside Collaborator?
- What teams should they be on?
- What base permission will they inherit?
- Is there anything they should explicitly NOT have access to?

**For outside collaborators:**
`Repo → Settings → Collaborators and teams → Add people`

Add them directly at the repo level with the minimum permission they need:
- Read — can view and clone
- Triage — can manage issues without write access
- Write — can push code
- Maintain — project management without destructive actions
- Admin — full repo control (almost never appropriate for outside collaborators)

**Principle of least privilege:** Give people the minimum access they need to do their job. You can always add more. Taking access away after the fact is awkward and often forgotten.

---

## Revoking Access — The Part Everyone Gets Wrong

Access revocation is where most organizations fail. Someone leaves the company or the contract ends, and in the chaos of transition, nobody removes their GitHub access. Weeks later they still have write access to your production codebase.

**Removing a member:**
`Your org → People → click the member → Remove from organization`

**Removing an outside collaborator:**
`Repo → Settings → Collaborators and teams → remove them`

Or at the org level:
`Your org → People → Outside collaborators → remove`

**What removal does:**
- Removes them from all teams
- Removes their org membership
- They lose access to private repos they had through team membership

**What removal does NOT automatically do:**
- Remove deploy keys they added (more on this below)
- Remove personal access tokens they created
- Remove any GitHub Apps they authorized
- Remove them from repos they were added to directly outside of team membership

**Offboarding checklist — run this every time someone leaves:**

- [ ] Remove from organization or revoke outside collaborator access
- [ ] Check all repos for direct collaborator access and remove
- [ ] Audit and revoke any deploy keys they added
- [ ] Check for and rotate any org secrets they had access to
- [ ] Review recent audit log activity before removing
- [ ] Check for any GitHub Apps authorized under their account
- [ ] If they were an Owner, verify remaining owners and transfer if needed

---

## Deploy Keys — The Access That Survives

This is one of the most overlooked security issues in GitHub org management and one of the most commonly asked about once something goes wrong.

A deploy key is an SSH key added directly to a specific repository that grants read or write access to that repo. They're used for automated deployments — a server uses the deploy key to pull code without needing a user account.

**The problem:** Deploy keys are attached to the repo, not the person. When you remove a team member from your org, their deploy keys are NOT automatically removed. If they added a deploy key to a repo — either for legitimate automation or otherwise — that key continues to work indefinitely until someone manually removes it.

**Where to check:**
`Repo → Settings → Deploy keys`

Every repo has its own deploy keys section. There is no org-wide view of all deploy keys across all repos. You have to check each repo individually.

**After any team member departure:**
- Go through every repo they had access to
- Check the deploy keys section
- Remove any keys you don't recognize or that were added by that person
- Rotate any secrets associated with automated deployments they managed

---

## Branch Protection at the Org Level

If you haven't read the [Branch Protection →](rulesets.md) section yet, head there first — it covers repo-level protection in detail. Then come back here for the org-level layer on top of it.

You've probably seen branch protection at the repo level — requiring pull request reviews before merging, requiring status checks, blocking force pushes. What most people don't know is that you can enforce these rules across your entire organization at once using **Rulesets**.

**Repo-level branch protection** applies only to the repo you configure it on. Every new repo starts with no protection unless you manually add it.

**Org-level Rulesets** let you define rules that apply to all repos in the org — current and future — automatically.

**Where to find it:**
`Your org → Settings → Rules → Rulesets`

**What to enforce org-wide:**

- Require pull request reviews before merging to `main`
- Block force pushes to `main` and `production` branches
- Require signed commits (if you've set up commit signing)
- Require status checks to pass before merging
- Restrict who can push directly to protected branches

**Why this matters:** Without org-level rulesets, every new repo someone creates in your org has zero protection by default. A developer can push directly to main, force push over history, or merge without review. Org-level rules close that gap automatically.

---

## Org Secrets vs Repo Secrets

GitHub Actions secrets let you store sensitive values — API keys, credentials, tokens — that your CI/CD workflows can access without exposing them in your code.

There are two levels:

**Repo secrets** — stored on a specific repo, accessible only to workflows in that repo.

`Repo → Settings → Secrets and variables → Actions`

**Org secrets** — stored at the org level, can be shared across multiple repos.

`Your org → Settings → Secrets and variables → Actions`

When you create an org secret you choose its scope:
- **All repositories** — every repo in the org can access it
- **Private repositories** — only private repos can access it
- **Selected repositories** — only the specific repos you choose

**Security considerations:**

- Don't use "All repositories" for sensitive secrets unless every repo genuinely needs them
- Rotate org secrets regularly and especially after any team member departure
- Use repo secrets for anything specific to one project
- Use org secrets for shared infrastructure credentials — deployment keys, notification services, shared API keys

---

## Repo Visibility — Public, Private, and Internal

Every repo in your org has a visibility setting that controls who can see it.

**Public** — anyone on the internet can view the code, issues, and most repo content. They can't push without explicit permission, but they can read, fork, and clone freely.

Use for: open source projects, public documentation, anything you intend to share with the world.

**Private** — only org members and outside collaborators explicitly added to the repo can see it.

Use for: proprietary code, client work, internal tools, anything containing sensitive logic or credentials.

**Internal** *(GitHub Enterprise Cloud only)* — visible to all members of your enterprise (which can include multiple organizations) but not to the public. A middle ground between public and private for large organizations.

Use for: shared internal libraries, internal documentation, tools used across multiple teams within a large company.

**The right way to build — start private, go public deliberately:**

Always start a new repo as private. Always. It costs nothing and protects everything. When you're ready to open source something or make it public, treat that transition as a security event — not a setting change.

Before making any repo public, check:

- **Git history** — run `git log` and look through commits for any hardcoded credentials, internal paths, employee names, client names, or anything that shouldn't be public. Private doesn't mean your history is clean. Every commit you ever made while the repo was private becomes public the moment you flip the switch.
- **Config files** — check for any `.env` files, config files, or settings files that contain environment-specific values, internal URLs, or credentials. Even if they're in `.gitignore` now, check whether they were ever committed.
- **Comments and TODOs** — developers leave internal notes in code comments. `// TODO: remove this before launch`, `// using prod API key here temporarily`. Search your codebase before going public.
- **Internal tooling** — if you have scripts or tools that reference internal infrastructure, internal domains, or internal systems — remove or sanitize them before going public.

**How to keep internal tooling private while going public with your product:**

Split your repos intentionally. Your public-facing product repo goes public. Your internal tooling, deployment scripts, infrastructure code, and anything with internal architecture details stays private. They don't have to live in the same repo and they shouldn't.

**IP Protection:**

If you're building something proprietary — a product, a client deliverable, anything with commercial value — it should be in a private repo. Changing a repo from private to public is instant. Anything already indexed or cached by search engines, bots, or automated scanners before you change it back may already be out there. Treat visibility settings as a security control, not a convenience toggle.

---

## Requiring 2FA Across Your Organization

Two-factor authentication at the individual account level is covered in the [Securing Your Account →](../setup/account.md) section. At the org level, you can require it for everyone.

**Where to enable it:**
`Your org → Settings → Authentication security → Require two-factor authentication`

**What happens when you enable it:**

- All current members and outside collaborators who don't have 2FA enabled are immediately removed from the org
- They receive an email explaining why
- They can re-request access once they enable 2FA on their personal account

**What this means for you:**

Before enabling org-wide 2FA, communicate to your team. Give them a deadline to enable it. Check who has it enabled before flipping the switch — GitHub shows you this in the People tab.

**GitHub accepts these 2FA methods (from strongest to weakest):**

- **Security key (hardware key)** — a physical device like a YubiKey. Strongest option. Phishing-resistant.
- **Authenticator app (TOTP)** — apps like Authy, Google Authenticator, or 1Password. Strong and practical for most teams.
- **SMS** — a code sent to your phone number. Weakest option. Vulnerable to SIM-swapping attacks. Better than nothing, but encourage your team to use an authenticator app instead.

**Outside collaborators:** The 2FA requirement applies to them too. If they don't have it enabled, they lose access when you enable the requirement. Give outside collaborators notice before enabling.

**Why this matters:** A single compromised team member account with write access to your repos can push malicious code, exfiltrate your entire codebase, or destroy your git history. 2FA on every account in your org dramatically reduces that risk.

---

## The Audit Log — Your Org's Black Box

The audit log records every significant action taken in your organization. Most founders and even many developers don't know it exists. It's one of the most valuable security tools GitHub gives you for free.

**Where to find it:**
`Your org → Settings → Logs → Audit log`

**What it records:**

- Who joined or left the org and when
- Permission changes — who was given what access to what
- Repo creation, deletion, visibility changes
- Branch protection rule changes
- Secret creation and access
- OAuth app authorizations
- Billing changes
- Failed authentication attempts
- Actions workflow runs and modifications

**What a suspicious audit log entry looks like:**

Here's an example of what you might see — and what it means:

```
2025-05-01 03:42:17 UTC  |  repo.add_member
Actor: contractor_username
Repo: yourorg/internal-payments-api
Permission granted: admin
```

Three things wrong with this entry: it happened at 3am, a contractor was given admin access (not write — admin), and it was to your most sensitive repo. You didn't authorize this. That's the kind of entry that tells you something is wrong.

Normal entries look like team leads adding developers during business hours with appropriate permission levels. Anything outside that pattern — unusual times, unusual actors, unusual permission escalations, repos changing visibility — warrants investigation.

**When to check it:**

- When someone leaves the org — review their recent activity before removing access
- When you notice something unexpected — a commit you didn't expect, a setting that changed
- Regularly — monthly at minimum for any org with sensitive IP
- Immediately if you suspect unauthorized access

**Audit log retention:** GitHub retains audit log events for 180 days on free plans. GitHub Enterprise extends this. If you need longer retention, export the log regularly via the API.

---

## Leaked Credentials in Org Repos — What to Do Right Now

This happens. A developer commits a `.env` file, hardcodes an API key, or accidentally pushes credentials to a repo. If the repo is public, automated scanners will find it within minutes. If it's private, the risk is lower but still real.

**Immediate steps — in this order:**

1. **Revoke the credential immediately** — don't wait. Go to whatever service issued it and revoke or rotate it now. Removing it from the repo does not invalidate it.

2. **Remove it from the repo** — delete the file or the line containing the credential and push a new commit. This removes it from the current state of the repo.

3. **Remove it from git history** — the credential still exists in every previous commit. Use `git filter-repo` or GitHub's push protection to scrub it from history. This is a destructive operation — coordinate with your team before running it on a shared repo.

4. **Check the audit log** — look for any unusual API usage or access in the window between when the credential was committed and when you rotated it.

5. **Enable push protection** — GitHub's push protection scans for secrets before they're committed and blocks the push. Turn it on for every repo.
`Repo → Settings → Code security → Push protection`

Or enable it org-wide:
`Your org → Settings → Code security → Push protection`

**For non-technical founders:**

If someone tells you a key was leaked in your GitHub repo, the first question is: has it been rotated? Not removed from the repo — rotated at the source. That's the only thing that actually stops the exposure. Everything else is cleanup.

---

## Quick Action Checklist

A scannable reference for founders and team leads.

**Before your first hire:**
- [ ] Create a GitHub Organization (don't use your personal account)
- [ ] Set base permissions to Read or No permission
- [ ] Create a backup owner account (separate personal account, stored securely)
- [ ] Enable push protection org-wide
- [ ] Decide: will contractors be Members or Outside Collaborators?

**When setting up a new org:**
- [ ] Set base permissions to Read or No permission
- [ ] Create teams that match your actual team structure
- [ ] Require 2FA for all members
- [ ] Enable push protection org-wide
- [ ] Create an org-level ruleset for branch protection on main
- [ ] Set at least two owners

**When adding someone new:**
- [ ] Member or Outside Collaborator? (contractors → Outside Collaborator)
- [ ] Add to appropriate teams only
- [ ] Grant minimum access needed
- [ ] Confirm 2FA is enabled on their account

**When someone leaves:**
- [ ] Remove from org or revoke outside collaborator access
- [ ] Check all repos for direct collaborator access
- [ ] Audit deploy keys on all repos they had access to
- [ ] Rotate any secrets they had access to
- [ ] Review audit log for their recent activity
- [ ] Verify remaining owners if they were an owner

**Monthly:**
- [ ] Review the audit log for anything unexpected
- [ ] Check the People tab for anyone who shouldn't still be there
- [ ] Verify outside collaborators are still active and needed
- [ ] Review org secrets and rotate anything old

**After any incident:**
- [ ] Rotate all credentials that could have been exposed
- [ ] Check audit log for the full scope of access
- [ ] Review and tighten permissions based on what happened
- [ ] Enable any missing protections before moving on
