# iop — Save secrets to 1Password

*I got tired of the manual dance every time I had to stash a secret: copy
it, open 1Password, create the item by hand, remember to go back and clear
it from my clipboard before I forgot and pasted it somewhere dumb. This
workflow does all of that in one step.*

## Usage

Save a copied secret (API key, token, password, etc.) to 1Password as an
**API Credential** item via the `iop` keyword.

```
iop my-service-key
```

Copy the secret first, then run `iop my-service-key` and press Enter. The
workflow creates the item, wipes the secret from your clipboard and from
Alfred's clipboard history, and copies the item's `op://` reference to your
clipboard instead.

Alternatively, with text selected in any app, trigger **Save to 1Password &
Delete** via the Universal Action. The selection is handed to the same flow:
Alfred opens pre-filled with `iop `, type the item name, and press Enter.

Configure the Hotkey "Save to 1Password & Delete" to trigger the same action
directly from a text selection, without opening the Universal Action menu.

## Setup

Install the [1Password CLI](https://developer.1password.com/docs/cli/get-started/)
and [`jq`](https://jqlang.org/), then turn on the integration in 1Password:
**1Password → Settings → Developer → Integrate with 1Password CLI**.

By default the workflow looks for both on `PATH` at `/opt/homebrew/bin` or
`/usr/local/bin` (Homebrew's Apple Silicon / Intel default locations). If
yours live elsewhere, edit the `PATH` line in the workflow's two Run Script
actions (Workflow → double-click either script box).

## Other ways to trigger it

- **External Trigger** (`save_to_1password`): callable from another
  automation tool. Example, from an AppleScript action in a PopClip-style
  text-selection app (tested with
  [OpenClip](https://github.com/ganeshmshetty/openclip)):

  ```applescript
  tell application id "com.runningwithcrayons.Alfred" to run trigger "save_to_1password" in workflow "com.drselump14.import_clipboard_to_1password" with argument OPENCLIP_TEXT
  ```

Selection-based triggers (Hotkey, Universal Action, External Trigger) all
stash the handed-off text in a private one-shot file rather than the
clipboard — Alfred's own selection capture restores your original clipboard
shortly after handing off the text, which would otherwise race against you
typing the item name.

## How it saves

```sh
op item create --category="API Credential" --title="$TITLE" "credential=$SECRET_TEXT" --format json
```

The `credential` field is 1Password's standard concealed field for this
category, so it displays masked in the 1Password app and browser extension
until revealed.

## Install

Download the `.alfredworkflow` from [Releases](../../releases) and
double-click to import into Alfred.
