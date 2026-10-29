# Store Menu (desktop)

Store-Grade Equipment Menu that downloads the STORE-GRADE TESTED MATERIAL feed straight from RazorERP.

## Use
1. Install `Store Menu Setup 1.4.0.exe` (one click, no admin rights needed).
2. Open Store Menu, enter your RazorERP username and password, keep "Remember me" checked, and click
   **Log in and load the menu**.
3. The app signs in to https://sensiblerecycling.razorerp.com, goes to Sales > Feeds > Inventory Feeds,
   and downloads /Sales/Content/DownloadActual?feedId=4092. The menu loads.
4. From then on it re-downloads the feed every time it opens, every 30 minutes while open, and when you
   click **Refresh from RazorERP**. If the RazorERP login expires it signs in again by itself.

Settings lets you change the RazorERP address or feed ID, log out, add Slack, and save a setup file.

## Password safety
- The password is saved encrypted with Windows' own protection (DPAPI), only on that computer.
- Uncheck "Remember me" to keep it only until the app closes.
- If a saved password stops working, the app forgets it and asks again, so background refreshes
  can never lock the account with repeated wrong attempts.
- Setup files never contain anyone's RazorERP login.

## If automatic login doesn't work
Use **Log in in a RazorERP window instead** on the login screen. The real RazorERP page opens; log in
there and it closes by itself once you're in.

## If it says RazorERP showed a page instead of the feed
Click **Download it in a RazorERP window**. Inventory Feeds opens; click the download for the
STORE-GRADE TESTED MATERIAL feed like you normally would. The app catches the file, remembers the
exact link it came from, and uses that link for every automatic refresh after that.
Settings > Copy problem details copies what RazorERP showed (page title and address, no passwords).

## Sending the order
Orders go out through Slack only (email sending was removed in 1.6). When an order is created, the bot
posts it to the orders channel with the CSV attached and tags the manager. If Slack isn't set up, the
success screen has "Copy for Slack" so you can paste the message and drag the CSV in yourself.

Departments on the checkout form, in order: Store, Testing, Ebay, Warehouse, Manager.

Orders with department **Manager** are posted as PRIORITY: a big red-siren header
"PRIORITY: MANAGER REQUEST", a red color bar down the side of the order details, and
"Handle first", with the CSV posted right below it. Other departments post the normal way.

## Fulfilled with a checkmark (1.7)
When an order is done, react to its Slack message with a checkmark (:white_check_mark:, :heavy_check_mark:
or :ballot_box_with_check:). For priority orders, react to the red PRIORITY alert. Store Menu checks every
2 minutes and whenever you open Past Orders, then shows the order as **Fulfilled** with who reacted.
Removing the checkmark puts it back to Sent. You can also click Mark fulfilled on an order by hand.

The bot needs these extra scopes (OAuth & Permissions > Bot Token Scopes), then **Reinstall to Workspace**:
files:read (to find the order message), reactions:read (to see the checkmark), users:read (to show the name).
The token stays the same after reinstalling.

## Tagging the manager
Add a manager line to slack.txt (or fill in "Manager to tag" in Settings > Slack):

    manager=U0123ABCD

Use the manager's Slack member ID (in Slack: click their name > View full profile > the three dots >
Copy member ID). A user group ID (S0123...) or the word here also works. Every order message starts
with that mention, so it shows up highlighted for the manager and notifies them.

## Categories
RazorERP category paths are shortened: "Root Category|Resale|Desktops & Laptops" shows as
"Desktops & Laptops", with subcategories (Laptops, Desktop Computers, All-in-One, Workstations...)
worked out from the model names.

## Feed formats
CSV, tab-separated, and Excel (.xlsx/.xls) feeds are all handled (Excel is converted using the first sheet).

## Build
npm install
npm start            # run
npm run dist         # Windows installer (on Linux needs wine)
`defaults.json` presets the RazorERP address and feed ID.

## Slack codes from a text file (1.5.0)

Instead of typing the bot token and channel ID into Settings, create this file:

    Documents\Store Menu\slack.txt

with these lines:

    token=xoxb-your-bot-token
    channel=C0123ABCD
    manager=U0123ABCD

Easiest way: put your filled-in slack.txt in the same folder as the installer before running it. The installer copies it to Documents\Store Menu\slack.txt automatically (overwriting an older one), so Slack works right after install.

The app reads it every time it posts, so edits take effect right away (no restart). Values in the file override Settings. Bare lines (just the xoxb- token and the channel ID) also work, and lines starting with # are ignored. The app also looks for slack.txt next to Store Menu.exe and in your Store Menu Orders folder.


## Updates (1.8+, GitHub Releases)
The app checks https://github.com/ReuseitJax/store-menu/releases when it opens and every 4 hours.
A newer version downloads in the background, then a green bar says "Restart and update". If nobody
clicks it, the update installs the next time the app closes. Settings > Updates shows the version and
has "Check for updates".

One-time setup: create a **public** repo named `store-menu` under ReuseitJax (it can be empty; only the
installer goes in the releases, not your source or tokens).

To ship an update: each release folder comes with **Publish.bat** next to the installer, blockmap and
latest.yml. Install the GitHub CLI once (cli.github.com) and run `gh auth login`, then double-click
Publish.bat. It creates the public repo if it doesn't exist yet and uploads the three files as release vX.Y.Z.

To ship an update by hand:
1. Raise "version" in package.json (for example 1.8.0 -> 1.8.1).
2. Build: `npm run dist`.
3. On GitHub: Releases > Draft a new release > tag `v1.8.1` > upload these three files from `dist`:
   `Store-Menu-Setup-1.8.1.exe`, `Store-Menu-Setup-1.8.1.exe.blockmap`, `latest.yml` > Publish release.
   (Or set a GH_TOKEN environment variable and run `npm run release` to build and upload in one step.)
Everyone on 1.8.0 or newer picks it up automatically. Anyone on 1.7 or older needs to install 1.8.0 by
hand once.
