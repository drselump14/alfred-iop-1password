# iop — Save secrets to 1Password from Alfred

Alfred workflow that saves a secret (API key, token, password, etc.) to 1Password
as an **API Credential** item, without ever typing the secret into a text field
or leaving it sitting in your clipboard history.

## Usage

Copy a secret, then in Alfred:

```
iop my-service-key
```

Press Enter. The workflow:

1. Creates a 1Password item (category: **API Credential**) titled `my-service-key`
   with the clipboard contents as the `credential` field
2. Wipes the secret from your system clipboard and from Alfred's clipboard history
3. Copies the item's `op://` reference to your clipboard instead

## Other triggers

The same save logic is also wired to:

- **Hotkey** (unassigned by default — set your own combo in the workflow):
  grabs the current text **selection** directly (via macOS Accessibility,
  no manual copy needed)
- **Universal Action** ("Save to 1Password & Delete"): available from the
  Services menu / Universal Actions on any selected text
- **External Trigger** (`save_to_1password`): callable from other tools.
  Example, from an AppleScript action in a PopClip-style text-selection
  app (tested with [OpenClip](https://github.com/ganeshmshetty/openclip)):

  ```applescript
  tell application id "com.runningwithcrayons.Alfred" to run trigger "save_to_1password" in workflow "com.drselump14.import_clipboard_to_1password" with argument OPENCLIP_TEXT
  ```

These three all feed selected/forwarded text through the **same** `iop` flow:
the text is stashed in a private one-shot file (not the clipboard — Alfred's
own selection-capture restores your original clipboard shortly after handing
off the text, which races with you typing the item name), Alfred opens
pre-filled with `iop `, and you type the name and press Enter exactly as above.

## Requirements

- [1Password CLI](https://developer.1password.com/docs/cli/) (`op`), signed in
  and unlocked
- [`jq`](https://jqlang.org/)
- Both expected on `PATH` at `/opt/homebrew/bin` (Apple Silicon Homebrew
  default) or `/usr/local/bin` — edit the `PATH` line in the scripts if yours
  live elsewhere

## How it saves

```sh
op item create --category="API Credential" --title="$TITLE" "credential=$SECRET_TEXT" --format json
```

The `credential` field is 1Password's standard concealed field for this
category, so it displays masked in the 1Password app/browser extension until
revealed.

## Install

Download the `.alfredworkflow` from [Releases](../../releases), or clone this
repo and re-zip `info.plist` + `icon.png` as `<name>.alfredworkflow`, then
double-click to import into Alfred.
