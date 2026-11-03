# Keep learning feed

**For:** grow.iqdesign.ai and every supplier edition cloned from it
**What it runs:** the "Keep learning" course list and the "Updated [date]" line above the page headline

---

## In 30 seconds

- The course list and the "Updated" date on the page both come from **one file: `feed.json`**.
- That file lives in this GitHub repository. The website reads it **every time someone opens the page**.
- When the file changes, the page shows the change **within about 5 minutes**. You don't edit or republish anything in Framer.
- A **daily Claude task** keeps the file current. You can also edit it by hand in your browser.

```
 Daily Claude task          GitHub (this repository)             grow.iqdesign.ai
 ─────────────────  edits  ─────────────────────────  read on   ─────────────────────────────
 finds and checks   ─────▶  feed.json                 ─────────▶ Keep learning list (15 cards)
 courses + events           (checked automatically)   every visit "Updated November 2, 2026"
```

---

## What's in this repository

| File | What it's for | Who touches it |
|---|---|---|
| `feed.json` | **The list itself.** Every course, event and podcast on the page, plus the "updated" date. | The daily task, or you by hand |
| `feed.schema.json` | The rules `feed.json` must follow (field names, date format, allowed values). | Nobody, unless the page changes |
| `.github/workflows/check-feed.yml` | Checks `feed.json` against those rules after every change and shows a green tick or red cross. | Nobody |
| `README.md` | This guide. | — |

---

## One-time setup (about 20 minutes)

You only do this once. Each step says what you should see when it worked.

- [ ] **Step 1.** Create the repository
- [ ] **Step 2.** Upload the files
- [ ] **Step 3.** Copy the feed's address
- [ ] **Step 4.** Connect it to Framer and publish
- [ ] **Step 5.** Give the daily task permission to update the file
- [ ] **Step 6.** Test it

### Step 1. Create the repository

1. Sign in at **github.com**. Use the account that should own this long term. The site will be hosted from Ryan's Framer account, so a Truax Marketing GitHub account makes sense.
2. Click **+** (top right) → **New repository**.
3. **Repository name:** `iq-grow-feed`
4. Choose **Public**. The website has to read the file without a password. Everything in it is already shown on the public page. **Never put anything private in this repository.**
5. Leave **Add a README file** unticked (you'll upload this one).
6. Click **Create repository**.

✅ You see an empty repository page that says "Quick setup".

### Step 2. Upload the files

1. On that page, click the link **uploading an existing file**.
2. Drag in **`feed.json`**, **`feed.schema.json`** and **`README.md`**.
3. Click **Commit changes**.
4. Now add the automatic check. Folders that start with a dot don't always upload by drag-and-drop, so create this file by hand:
   1. Click **Add file** → **Create new file**.
   2. In the name box, type exactly: `.github/workflows/check-feed.yml`
      (typing each `/` creates a folder as you go).
   3. Open `check-feed.yml` from the files you were sent, copy **all** of it, and paste it into the big text box.
   4. Click **Commit changes**.

✅ The repository shows three files plus a `.github` folder.

### Step 3. Copy the feed's address

1. Click **`feed.json`** in the repository.
2. Click the **Raw** button (top right of the file).
3. Copy the address from your browser's address bar. It looks like this:
   ```
   https://raw.githubusercontent.com/YOUR-ACCOUNT/iq-grow-feed/main/feed.json
   ```
4. Keep it handy for Step 4.

✅ Opening that address in a new tab shows the list as plain text, starting with `{ "updated": …`.

### Step 4. Connect it to Framer and publish

1. Open the IQ Design project in Framer.
2. In the left sidebar, click **Assets**, open **Code**, and click **`Feed.tsx`**.
3. Near the top you'll see this line:
   ```ts
   export const FEED_URL = ""
   ```
4. Paste your address **between the two quote marks**, with no spaces:
   ```ts
   export const FEED_URL = "https://raw.githubusercontent.com/YOUR-ACCOUNT/iq-grow-feed/main/feed.json"
   ```
5. Framer saves code automatically. Close the code tab.
6. **Don't** fill in the Course Library's "Feed URL" box in the right-hand panel. `FEED_URL` drives both the list and the "Updated" date; that box only changes the list, and is there for testing.
7. Click **Publish**.

This is the **only** time you publish for the feed. After this, changes to `feed.json` appear on the live page by themselves.

✅ On the live page, the "Keep learning" list matches your file.

> **Supplier editions:** each cloned Framer project keeps the same `FEED_URL`, so every edition updates from this one file at the same time.

### Step 5. Give the daily task permission to update the file

The daily Claude task needs a key that can edit **this repository only**.

1. On GitHub, click your profile photo (top right) → **Settings**.
2. At the bottom of the left menu: **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.
3. Fill in:
   - **Token name:** `IQ feed – daily task`
   - **Expiration:** 1 year (put a calendar reminder a week before it expires)
   - **Repository access:** **Only select repositories** → choose `iq-grow-feed`
   - **Permissions** → **Repository permissions** → **Contents:** **Read and write**
4. Click **Generate token** and **copy it now**. GitHub only shows it once.
5. Store it where the daily task keeps its secrets (for example the scheduled task's environment variables, as `GITHUB_TOKEN`). **Never** paste it into Framer, this README, an email or a chat.

> If the repository belongs to a GitHub **organisation**, an admin may need to allow fine-grained tokens first: Organisation **Settings** → **Personal access tokens**.

✅ The token appears in your token list with access to `iq-grow-feed`.

### Step 6. Test it (2 minutes)

1. In the repository, click **`feed.json`**, then the **pencil icon** (Edit).
2. Change the `updated` date on line 2 to today, for example `"2026-11-02"`.
3. Click **Commit changes**.
4. Open the **Actions** tab. Within a minute, **Check feed** shows a **green tick**.
5. Wait up to 5 minutes, then open the live page and refresh (**Ctrl+Shift+R** on Windows, **Cmd+Shift+R** on Mac).

✅ The "Updated" line above the headline, and the one in the Keep learning section, show the new date.

---

## How the page uses the file

Every time the page opens, it reads `feed.json` and applies these rules (so even a slightly messy list displays correctly):

1. **Only published items show.** An item with `"status": "review"` or `"draft"` stays hidden. No status means published.
2. **Past events disappear by themselves** the day after their `date`.
3. **Order:** items carrying CEU (`type` `ceu`) first, then events (soonest first), then courses, then podcasts.
4. **Top 15** are shown, numbered 01–15 in that order.
5. **Filter buttons** show the items whose `paths` include that filter (table below). "Start here" shows all 15.
6. **Each card links to its `url`** in a new tab. Items without a `url` still show, but can't be clicked. V3 says every listing should link out, so fill these in.
7. **Safety net:** if the file can't be read, isn't valid, or the address is wrong, the page shows its **built-in copy of the original 15 listings, dated October 27, 2026**, so the section is never empty. If the live page shows that date long after you've changed the file, see Troubleshooting.

---

## What each field means

One item looks like this:

```json
{
  "title": "Using AI Visualizations to Grow Your Design Practice",
  "provider": "ASID Minnesota working group · IMS, Minneapolis",
  "type": "ceu",
  "date": "2027-01-13",
  "label": "In person · CEU pending",
  "paths": ["studio", "early", "ceu"],
  "url": "https://example.org/the-session-page",
  "status": "published"
}
```

| Field | Required? | What to put | How it shows on the page |
|---|---|---|---|
| `updated` (top of file) | Yes | The date the list last changed, as `YYYY-MM-DD`. **Only change it when the list changes.** | "Updated November 2, 2026" (hero + Keep learning) |
| `title` | Yes | The session, course or podcast name | Card title |
| `provider` | Yes | Who runs it (organisation · person) | Grey line under the title |
| `type` | Yes | One of `ceu`, `event`, `course`, `podcast` | Sets the order (see above) |
| `date` | Yes | `"YYYY-MM-DD"` for a dated session, or `null` for on-demand and recurring | Dated items show "Upcoming · Jan 13, 2027" |
| `label` | Yes | A short tag: `"1.5 CEU"`, `"Free"`, `"Certificate"`. Can be `""` | Small caps line at the bottom of the card |
| `paths` | Yes | One or more of the filter keys below, e.g. `["studio", "ceu"]`. Can be `[]` | Which filter buttons show it |
| `url` | Yes | The provider's own page, starting `https://`. Use `null` only if there isn't one yet | Makes the card clickable |
| `status` | No | `published` (shows), `review` or `draft` (hidden) | — |
| `availability` | No | For undated items: `"Recurring"` or `"On demand"` | Shown beside the label |

**Filter keys**

| Key in `paths` | Filter button on the page |
|---|---|
| *(any item)* | Start here |
| `studio` | Running your own studio |
| `kb` | Kitchen and bath |
| `showroom` | Showroom or dealer |
| `early` | Early in your career |
| `ceu` | Carries CEU |

> **`type: "ceu"` and the `ceu` path do different jobs.** The type puts it at the top of the order; the path makes it appear under "Carries CEU". A CEU course normally has both.

**Formatting rules** (the automatic check catches these):
- Text and dates go in double quotes: `"Free"`, `"2027-01-13"`. `null` has no quotes.
- A comma goes between items, but **not** after the last item in a list.
- Dates are always `YYYY-MM-DD`.

---

## Editing by hand

All of this happens in your browser on GitHub. Open **`feed.json`** → **pencil icon**, make the change, then **Commit changes**.

- **Add an item:** copy an existing `{ … }` block, paste it after another, put a comma between them, and change the details.
- **Remove an item:** delete its whole `{ … }` block and the comma before or after it.
- **Hide an item without deleting it:** add `"status": "review",` inside it.
- **After any change:** set `updated` to today, commit, and check the **Actions** tab shows a green tick.

If you see a **red cross**, click it. The run names the exact item and field to fix (for example `/items/3/date`, which means item 4, since counting starts at 0). Fix it and commit again.

---

## The daily Claude task

This runs on Ryan's side, separately from the website build. Copy the brief below into the scheduled task's instructions. It follows the plan in the approved V3 notes.

```text
You maintain feed.json in the GitHub repository <OWNER>/iq-grow-feed. It powers the
"Keep learning" list on grow.iqdesign.ai. Run once a day.

Starting point (first run only): a one-time copy of Designers at the Helm's courses and
events, converted to the fields in README.md.

Every run:
1. Remove events whose date has passed.
2. Find new, relevant AI-for-design sessions, courses and podcasts from ASID, IIDA, NKBA,
   IDCEC, AIA and trade-market sessions. Add only ones you have verified on the provider's
   own page.
3. Re-check existing listings on the provider's own page: fix dates, titles and URLs;
   set "status": "review" on anything you can't confirm.
4. Change only a handful of listings per run.

Rules:
- Edit only feed.json. Keep every field name and format exactly as in README.md and
  feed.schema.json. Validate against feed.schema.json before saving.
- Every listing needs a "url" to the provider's own page.
- Change "updated" to today's date only if at least one listing changed. If nothing
  changed, don't commit.
- Commit message: one line per change, e.g. "Added: …", "Removed (past): …", "Updated: …".
- Once a week, send Ryan a short summary of that week's commits.

To save: use the GitHub API with the token in GITHUB_TOKEN:
GET  /repos/<OWNER>/iq-grow-feed/contents/feed.json   (gives the current "sha")
PUT  /repos/<OWNER>/iq-grow-feed/contents/feed.json   (base64 content + that sha + message)
```

The repository's history (**Code** → **Commits**) is the full log of every change, and any change can be undone from there.

---

## Troubleshooting

| What you see | Most likely reason | What to do |
|---|---|---|
| A change isn't showing yet | GitHub keeps the file cached for up to 5 minutes | Wait 5 minutes, then hard-refresh (Ctrl+Shift+R / Cmd+Shift+R) |
| The page still says "Updated October 27, 2026" and shows the original 15 | The page can't read your file: `FEED_URL` wasn't set, or the site wasn't **published** after setting it, or the address is wrong, or the repository is private | Open the `FEED_URL` address in a browser (Step 3 check). Make sure the repository is **Public**. Re-check Step 4 and publish. |
| Red cross in **Actions** | Something in `feed.json` breaks the rules | Click the run; it names the item and field. Fix, commit, and look for the green tick. |
| An item is missing | Its date has passed, its `status` isn't published, it's outside the top 15, or `type` is misspelled | Check those four fields |
| An item is missing under one filter only | Its `paths` don't include that filter's key | Add the key (see the filter table) |
| A card can't be clicked | Its `url` is `null` or doesn't start with `https://` | Add the provider's link |
| "Nothing on this path yet. Try Start here." | None of the top 15 has that filter's key in `paths` | Add the key to the relevant items |
| The daily task stopped updating | The token expired or its access was removed | Make a new token (Step 5) and update the task's secret |

---

**Questions:** the Framer side (page, Feed.tsx, publishing) is Aidan at Team Pixel. GitHub and the daily task are the repository owner.
