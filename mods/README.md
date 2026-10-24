# Publish IK3 mods

The client reads [catalog.json](./catalog.json) on the `main` branch of
`KrakenPiracy/ik3-client`. Titles, descriptions, and new entries all come from
this file. No launcher rebuild is required when you publish or edit a mod.

## Folder structure

```text
mods/
  catalog.json
  rebirth/
    matchstats.gscc
  ffa/
    matchstats.gscc
```

Keep each variant in its own folder so GitHub can store scripts with the same
filename. Upload actual compiled scripts; do not use Git LFS for these files.
Individual scripts can be up to 16 MiB. Both `.gscc` and `.gsc` are supported.

## Add your first mods

1. On GitHub, open this repository's `mods` folder and choose **Add file → Upload files**.
   Upload the `rebirth` folder containing your real `matchstats.gscc` file and commit to `main`.
2. Upload your `ffa` folder the same way when its script is ready.
3. Edit `mods/catalog.json` and add an entry for each uploaded script. For example:

```json
{
  "schema_version": 1,
  "mods": [
    {
      "id": "rebirth",
      "title": "Rebirth",
      "description": "Your description of the Rebirth mode goes here.",
      "file": "mods/rebirth/matchstats.gscc"
    },
    {
      "id": "ffa",
      "title": "Free For All",
      "description": "Your description of the FFA mode goes here.",
      "file": "mods/ffa/matchstats.gscc"
    }
  ]
}
```

Only add entries for scripts you have uploaded. The starter catalog is empty
so users cannot install placeholder or missing scripts.

For every future mod, add another folder and another catalog entry. Keep `id`
stable and unique; it can contain lowercase letters, digits, hyphens, and
underscores. Titles and descriptions are plain text. Changing the script's
destination filename requires users to remove and reinstall that mod, so keep
the filename stable for automatic updates.

## Update a published mod

Replace the script at the same GitHub path and commit to `main`. Edit the title
or description in the catalog whenever needed. There is no version number or
checksum to maintain. Commit related catalog and script edits together when
possible, or upload new scripts before adding catalog entries.

The launcher refreshes the catalog and installed mods at startup, every five
minutes while open, and before launching a game with installed mods. It fetches
the latest script even if the catalog did not change. Users do not click an
Update button. New catalog entries appear automatically, but are installed only
when a user selects **Install**.

Updates need an internet connection and a running launcher. Offline requests,
missing uploads, locked files, and other failures keep the installed copy and
retry at the next check. While the launcher's game process is running, script
changes are deferred until it exits or the next launch. A batch file that starts
a detached game and immediately exits cannot report that game's lifetime to the
launcher; keep the batch waiting for the game process if you need that behavior.

## Where files go

Every script is installed into the game's **jupiter/Assets/Scripts/mp** folder.
The client finds `jupiter` beside the detected `Start_IK3.bat`, in one of its
parents within the located installation, or uses that folder if the batch file
is already inside `jupiter`. It creates `Assets/Scripts/mp` if needed, but requires
the real `jupiter` folder to exist.

Installing FFA over Rebirth's `matchstats.gscc` replaces that file and makes FFA
the active owner. Only FFA receives subsequent automatic updates. Removing FFA
deletes its installed script; it does not restore Rebirth or the original file.
Mods using different filenames can coexist. Unrelated game files are untouched.

Removing a catalog entry hides it for new users; existing users keep their copy
and can still remove it. Per-installation ownership lives in `.ik3-mods.json`
inside the target folder. Do not manually edit that record.
