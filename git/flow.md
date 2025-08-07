## Recommended workflow (short version)

**One rule of thumb:** **don’t work directly on `dev`**. Treat `dev` like a shared integration branch. Do your work on short‑lived branches **off `dev`**, then open PRs into `dev`.

### 0) Establish `dev` properly (once)

```bash
# You already made it locally; make sure it's on the remote:
git push -u origin dev
```

Ask the owner to:

* **Protect** `main` (and ideally `dev`): require PRs, reviews, CI, and disallow force‑pushes.
* Confirm the flow: **feature → dev**, then **dev → main** (usually by a maintainer/release process).

---

### 1) Create a topic branch for your config work (off `dev`)

Avoid `dev/config` (it *looks* like a sub-branch of dev). Use a conventional name:

* `chore/config-setup`
* `feat/routing-guards` (if this adds app behavior)
* `refactor/router-migration`

Commands:

```bash
git switch dev
git pull origin dev
git switch -c chore/config-setup
```

---

### 2) Make small, focused commits

Examples:

```bash
git add src/routes/*
git commit -m "feat(router): add base routes"

git add src/auth/RouteGuard.tsx
git commit -m "feat(auth): add route guard component"

# IMPORTANT: do NOT commit real secrets.
# Add a template and ignore real .env
echo "VITE_API_URL=" > .env.example
git add .env.example .gitignore
git commit -m "chore(env): add .env.example and update .gitignore"
```

> **Tip:** Never commit `.env` with real keys. Commit `.env.example` and document required keys in the README.

---

### 3) Push and open a PR into `dev`

```bash
git push -u origin chore/config-setup
# Open PR: chore/config-setup → dev
```

Add a clear description (what changed, how to test, screenshots if relevant).

---

### 4) Keep your branch current (to avoid late surprises)

As teammates merge into `dev`, update your branch before/during review:

**Option A — Merge (simpler)**

```bash
git fetch origin
git switch chore/config-setup
git merge origin/dev
# resolve conflicts if any
git push
```

**Option B — Rebase (cleaner history)**

```bash
git fetch origin
git switch chore/config-setup
git rebase origin/dev
# resolve conflicts, then:
git add <files>
git rebase --continue
git push --force-with-lease
```

> `--force-with-lease` is safe for **your** topic branch (never for `dev`/`main`). It refuses to overwrite others’ work on the remote.

---

### 5) After approval

* If your team’s rule is “author merges after approval,” choose **Squash and merge** into `dev` (keeps history tidy).
* If the organization prefers maintainers to merge (and to handle `dev → main`), then wait for them.
* Eventually, a separate PR (or release process) merges **`dev` → `main`**.

---

## About conflicts

> “If I repeat the steps, in theory I should not have conflicts, right?”

Not quite. You **can still get conflicts** whenever **both** your branch and `dev` modify the **same lines** in the **same files** (or one deletes a file you changed). Config work (routes, guards, permissions) often touches shared files, so conflicts are possible.

**How to minimize conflicts:**

* Keep PRs **small and focused**.
* **Update your branch frequently** (merge/rebase on top of `origin/dev`).
* Coordinate on high‑churn files (e.g., `src/routes.tsx`, `App.tsx`).
* Use **draft PRs** early so teammates see what you’re touching.
* If the repo uses **CODEOWNERS**, ping the right reviewers promptly.

**When conflicts happen:** Git (or GitHub) will tell you. You fix them **in your topic branch**, commit, and push (or continue the rebase), and the PR updates automatically.

---

## Putting it together with your exact plan

> *“I created `dev`, made initial commit. Next I’ll do config (.env, routes, guards). Should I do `git checkout -b dev/config`?”*

Use a topic branch **off** `dev`, but name it like `chore/config-setup` (or `feat/routing-guards`), not `dev/config`:

```bash
git switch dev
git pull origin dev
git switch -c chore/config-setup
# work, commit, push
git push -u origin chore/config-setup
# open PR → dev
```

> *“After PR review I do `git rebase origin/dev` … `git push --force-with-lease`?”*

Yes—**if** your team prefers a linear history. Otherwise, just merge `origin/dev` into your branch and push (no force). Either way is fine; follow team preference.

> *“If I want to correct a particular file later…”*

* If the PR is **still open**, push more commits to the **same** branch; the PR updates.
* If the PR is **already merged**, create a **new** short‑lived branch from the **latest `dev`**:

  ```bash
  git switch dev
  git pull origin dev
  git switch -c fix/route-typo
  # commit fix
  git push -u origin fix/route-typo
  # PR → dev
  ```

---

## Extra React Router / config tips

* If you changed deps (`package.json`), lockfile conflicts can happen (`package-lock.json`/`yarn.lock`). After resolving:

  ```bash
  npm install
  npm run build
  npm test
  ```

  Commit the updated lockfile.

* Document required env keys in **README** and keep secrets out of git.
