# doc-unit-test

Unit test documentation to validate UX flows, display text, and images. Primarily useful for process docs, `doc-unit-test` supports test definitions single-sourced in documentation or defined in separate test files to suit your infrastructure needs.

`doc-unit-test` ingests text files, parses them for test actions, then executes those actions in a headless Chromium browser. The results (PASS/FAIL and context) are output as a JSON object so that other pieces of infrastructure can parse and manipulate them as needed.

This project handles test parsing and web-based UI testing--it doesn't support results reporting or notifications. This framework is a part of testing infrastructures and needs to be complimented by other componenets.

`doc-unit-test` uses `puppeteer` to install, launch, and drive Chromium to perform tests. `puppeteer` removes the requirement to manually configure a local web browser and enables easy screenshoting and video recording.

**Note:** By default, Chromium does't run in a Docker container, which means that `puppeteer` doesn't work either. Don't run `doc-unit-test` in a Docker container unless you first confirm that you have a custom implementation of headless Chromium functional in the container.

## Status

MVP released, but still under heavy development.

## Get started

### Prerequisites

*   Node.js

### Run tests

Run the tests in the [sample/](https://github.com/hawkeyexl/doc-unit-test/tree/master/sample) directory:

```bash
git clone https://github.com/hawkeyexl/doc-unit-test.git
cd doc-unit-test
npm install
node .
```

To customize your test, file type, and ditectory options, update [src/config.json](https://github.com/hawkeyexl/doc-unit-test/blob/master/src/config.json).

## Features

## Tests

You can define tests within your documentation (see [doc-content.md](https://github.com/hawkeyexl/doc-unit-test/blob/master/sample/doc-content.md)), or as separate files. Non-JSON files only support single-line test action definitions, so make sure to keep the entire action definition on one line.

JSON files must follow the format and structure defined in [testDefinition](https://github.com/hawkeyexl/doc-unit-test/blob/master/ref/testDefinition.json). For an example, see [samples/tests.json](https://github.com/hawkeyexl/doc-unit-test/blob/master/sample/tests.json).

## Actions

Each test is composed of multiple actions. Actions in a test perform sequentially as they're defined. If one or more actions fail, the test fails.

For information on each field, see [testDefinition](https://github.com/hawkeyexl/doc-unit-test/blob/master/ref/testDefinition.json).

### goTo

Navigate to a specified URI.

Format:

```
{
  "action": "goTo",
  "uri": "https://www.google.com"
}
```

### Find

Identify if an element is on the current page based on CSS selectors.

Format:

```
{
  "action": "find",
  "css": "[title=Search]"
}
```

### Match text

Identify if an element displays the expected text.

Format:

```
{
  "action": "matchText",
  "css": "#gbqfbb",
  "text": "I'm Feeling Lucky"
}
```

### Click

Click an element specified by CSS selectors.

Format:

```
{
  "action": "click",
  "css": "#gbqfbb"
}
```

### Type

Enter text in an element specified by CSS selectors.

Format:

```
{
  "action": "type",
  "css": "[title=Search]",
  "keys": "kittens",
  "trailingSpecialKey": "Enter"
}
```

### Move mouse

Move the mouse to an element specified by CSS selectors.

**Note:** The mouse cursor is visible in both recordings and screenshots.

Format:

```
{
  "action": "moveMouse",
  "css": "[title=Search]",
  "alignH": "center",
  "alignV": "center",
  "offsetX": 10,
  "offsetY": 10
}
```

### Scroll

Scroll the current page by a specified number of pixels. For `x`, positive values scroll right and negative values scroll left. For `y`, positive values scroll down and negative values scroll up.

Format:

```
{
  "action": "scroll",
  "x": 100,
  "y": 100
}
```

### Wait

Pause before performing the next action.

Format:

```
{
  "action": "wait",
  "duration": 500
}
```

### Screenshot

Capture an image of the current browser viewport. Supported extensions: .png

Format:

```
{
  "action": "screenshot",
  "mediaDirectory": "samples",
  "filename": "results.png"
}
``` 

### Start recording

Start recording the current browser viewport. Must be followed by a `stopRecording` action. Supported extensions: .mp4, .gif

**Note:** `.gif` format is **not** recommended. Because of file format and encoding differences, `.gif` files tend to be ~6.5 times larger than `.mp4` files, and with lower visual fidelity. But if `.gif` is a hard requirement for you, it's here. Creating `.gif` files requires `ffmpeg` installed on the machine that runs `doc-unit-test` and also creates  `.mp4` files of the recordings.

Format:

```
{
  "action": "startRecording",
  "mediaDirectory": "samples",
  "filename": "results.mp4",
  "gifOptions": {
    "fps": 15,
    "width": 400
  }
}
``` 

### Stop recording

Stop recording the current browser viewport.

Format:

```
{
  "action": "stopRecording"
}
``` 

## Post-release features

- New test actions
  - Shell commands (Support substitution/setting env vars.)
  - Curl commands (Support substitution/setting env vars. Only check for `200 OK`.)
  - Compare in-test screenshots to previously captured screenshots (upgrade screenshot() to compare to existing image at path, if present)
  - Test if a referenced image (such as an icon) is present in captured screenshot
- Suggest tests by parsing document text
- Automatically insert suggested tests based on document text

## License

This project uses the [MIT license](https://github.com/hawkeyexl/doc-unit-test/blob/master/LICENSE).
