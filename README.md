# Nebraska HS Boys Basketball TV Board

This repo powers a simple TV board that shows local Nebraska high school boys basketball schedules for a specific set of schools. It’s designed to run on a TV in an office, lobby, or shop using a full-screen browser.

Data is scraped daily from the NSAA class boys basketball pages and saved into a JSON file that the front end reads.

---

## What the board shows

For each configured team, the board displays:

- School name and NSAA class
- Current season record (wins–losses)
- Last game:
  - Opponent
  - Date
  - Result / score (if completed)
- Next game:
  - Opponent
  - Date / time
  - Home / away / neutral
  - Tournament info (if applicable)

Cards are shown in a rotating grid so multiple teams can share one screen.

---

## How the data works

- Scraper: `scraper/scrape_nsaa_boys_basketball.py`
  - Pulls from the NSAA boys basketball class pages (A, B, C1, C2, D1, D2).
  - Normalizes the tables and groups games by team.
  - Writes `data/boys_basketball.json`.

- GitHub Actions workflow: `.github/workflows/scrape_boys_basketball.yml`
  - Runs on a schedule (and can be triggered manually).
  - Installs `requirements.txt`.
  - Runs the scraper.
  - Commits updated `data/boys_basketball.json` back to the repo if anything changed.

The front end (`index.html`) then fetches `./data/boys_basketball.json` and renders the cards.

---

## Configuring which teams show up (teams.json)

The file `teams.json` defines groups of teams (usually by office or TV location).

Example:

```json
{
  "yanka-office": [
    "Aquinas Catholic",
    "David City",
    "East Butler"
  ],
  "northbend-office": [
    "North Bend Central",
    "Arlington",
    "Logan View/Scribner-Snyder",
    "Schuyler"
  ],
  "tarnov-office": [
    "Columbus",
    "Columbus Lakeview",
    "Scotus Central Catholic",
    "Humphrey-Lindsay Holy Family",
    "Twin River"
  ]
}


Notes:

The keys ("yanka-office", "northbend-office", etc.) are your office IDs.

Each array lists the exact team names as they appear in the NSAA schedules.

The URL’s office parameter must match one of these keys.

You can edit this file to match whatever teams you want on each screen.

Hosting / Viewing the board

You can host this:

As a GitHub Pages site (point it at the repo’s root), or

On any static host that serves index.html and the data folder.

Once hosted, point a TV or browser to the site URL with the appropriate query parameters (see below).

Example base URL (GitHub Pages):

Supported parameters
| Param     | Required? | Default       | Example                       | What it does                                                                                       |
| --------- | --------- | ------------- | ----------------------------- | -------------------------------------------------------------------------------------------------- |
| `office`  | Yes*      | `mead-office` | `office=northbend-office`     | Selects which group of teams to show. Must match a key in `teams.json`.                            |
| `per`     | No        | `2`           | `per=3`                       | Number of teams per screen (per “page”). Must be 1 or higher.                                      |
| `rotate`  | No        | `10` seconds  | `rotate=15`                   | Number of seconds each page is shown before rotating to the next. Minimum of about 5 seconds.      |
| `mode`    | No        | `compact`     | `mode=full` or `mode=compact` | Card layout. `compact` = summary cards. `full` = more detailed cards with extra fields.            |
| `cardmin` | No        | CSS default   | `cardmin=260`                 | Minimum card width in pixels. Increase to make larger cards (fewer per row), decrease to fit more. |
| `anim`    | No        | `on`          | `anim=off`                    | Turn fade animations and the progress bar on or off. `anim=off` disables both (instant page swap). |

*There is an internal default of office=mead-office, but unless you have a mead-office entry in teams.json, you will see a “no teams configured” message. In practice, you should always pass an office parameter.




