---
title: "Automate Character Highlighting with Highland"
---

[Highland](https://quoteunquoteapps.com/highland-2/), the program I use for screenwriting, has a useful feature to highlight a character's dialogue.

![Return to Black Creek highlighted script](/images/return-to-black-creek-highlighted.png)

Recently I cohosted a reading of *Return to Black Creek*, a comedy slasher I penned with my writing partner Stephen T. Holmes. To help the actors find their lines, we sent each a personalized PDF of the script with their character highlighted.

Saving a custom script by hand for all 15 of our actors would be tedious. Especially with the last-minute tweaks I can't resist. Highland lacks a built-in way to automate this, but fortunately the [*.highland* file format](https://quoteunquoteapps.com/highland-2/highland-format.php) is easy to work with.

Highland uses the [TextBundle format](http://textbundle.org). I was unfamiliar with it before, but it's used by several popular apps like Bear and Ulysses. Basically it's a few text files in a ZIP container.[^1] The one we care about is *characterHighlighting.json*. It's a dictionary of character names with their highlight colour in hex format.

```json
{
  "OLD MAN WILKINS" : "D1E3F4"
}
```

To highlight a character programmatically, we need to unzip the file, change *characterHighlighting.json*, and zip it back up. In Python this looks like this:

```python
import json
import os
import tempfile
from zipfile import ZipFile

file_path = "Return to Black Creek.highland"
character_highlighting = {"OLD MAN WILKINS": "D1E3F4"}

# We write the updated script to a temporary file then replace the original later.
temp_fd, temp_path = tempfile.mkstemp()
os.close(temp_fd)

with ZipFile(file_path, "r") as in_zip, ZipFile(temp_path, "w") as out_zip:
    character_highlighting_path = os.path.join(
        os.path.dirname(in_zip.namelist()[0]),
        "characterHighlighting.json",
    )

    # Copy items from the original zip file to the temporary one.
    # But skip characterHighlighting.json.
    for item in in_zip.infolist():
        if item.filename == character_highlighting_path:
            continue

        content = in_zip.read(item.filename)
        out_zip.writestr(item, content)

    # Now write characterHighlighting.json.
    character_highlighting_json = json.dumps(character_highlighting)
    out_zip.writestr(character_highlighting_path, character_highlighting_json)

# Overwrite the original file with the temporary one we created.
os.remove(file_path)
os.rename(temp_path, file_path)
```

To export the result to a PDF I used Keyboard Maestro to click through Highland's menu items.

![Keyboard Maestro export to PDF macro](/images/keyboard-maestro-export-highland-pdf.png)

You can probably use AppleScript to achieve the same result. I just don't have patience for AppleScript's arcane syntax.

If you need to automate character highlighting in your own Highland script, I uploaded a [more robust version](https://github.com/mminer/hlhl) of the above code to GitHub. To run it, download *hlhl* and run `./hlhl /path/to/screenplay.highland` in the terminal.

```
usage: hlhl [-h] [--clear-others] [--color COLOR] FILE CHARACTER

Highlights a character in a Highland screenplay.

positional arguments:
  FILE            Highland file
  CHARACTER       character to highlight

optional arguments:
  -h, --help      show this help message and exit
  --clear-others  remove other highlights
  --color COLOR   hex color value (default: D1E3F4)
```

---

[^1]: When digging into the *.highland* file I discovered that BBEdit can edit text files inside ZIP archives as easily as any other file. It's a neat trick.
