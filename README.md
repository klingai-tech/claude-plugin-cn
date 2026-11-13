# Kling AI CN for Claude

Build your own AI creative workflow with Kling MCP.

This plugin contains:

- a remote OAuth MCP connection to the China endpoint `https://klingai.com/mcp/plugin`;
- three Claude Code Skills with shared connection and billing rules:

| Skill | Purpose |
| --- | --- |
| `kling-ai` | Request routing, Elements, motion-library browsing, uploads, credits, task status, and account switching |
| `kling-ai-generate-image` | Text-to-image, image-to-image, reference handling, prompt construction, and image scene guidance |
| `kling-ai-generate-video` | Text-to-video, image-to-video, omni reference, keyframes, supported audio inputs, motion control, and single-shot or multi-shot planning |

It does not bundle a local MCP server, MCP App, command, hook, agent, or API
key. Generation runs on the remote Kling service.

## Install in Claude App

In Customize → Plugins → Add → Add marketplace, add
`https://github.com/klingai-tech/claude-plugin-cn`, then install **Kling AI CN**.
Enable only one regional Kling plugin at a time. Use the App's native connector
controls to connect your China account; Claude Code commands below apply only
to Claude Code.

## Install for local review

Load the plugin directory for one Claude Code session:

```bash
claude --plugin-dir /absolute/path/to/claude-plugin-cn
```

Claude Code versions that support ZIP plugin directories can load the release
archive directly:

```bash
claude --plugin-dir /absolute/path/to/kling-ai-cn-claude-1.3.1-public.zip
```

For a persistent installation, distribute the plugin through a trusted Claude
Code marketplace and install `kling-ai-cn@klingai-cn`.

```bash
claude plugin marketplace add klingai-tech/claude-plugin-cn
claude plugin install kling-ai-cn@klingai-cn
```

## Connect

The bundled `.mcp.json` registers one HTTP server named `kling-ai`. Open
`/mcp`, select `kling-ai`, and complete OAuth in the browser. The plugin never
asks users to paste an API key into chat.

Claude Code manages OAuth discovery, client registration, and credentials.
The plugin does not configure a custom OAuth client name or require users to
override registration metadata. If authorization fails, use the actual error
from Claude Code to diagnose the connection.

When switching from the Global connection to the China plugin, finish any
outstanding task checks in the original region, disconnect the old OAuth
session, update/reload the plugin, and authorize your China account through
`/mcp`. Regional accounts, credits, and task IDs must not be assumed to carry
over. Keep only one active Kling connection.

## Use

Try one of these prompts in Claude.

- Use Kling to generate a series of three images in one request. Show the same red panda in a vintage spacesuit exiting a space station, drifting past a porthole, and turning to look at the blue Earth.
- Use Kling 4.0 to generate a 20‑second cinematic video: a mech warrior slams down from height onto the ground, sending out a shockwave that instantly blasts apart rubble and dust, with the camera rapidly pushing in for a powerful impact.
- Use Kling 4.0 Flash to turn my sneaker product photo into a 20-second, 720p video. Start from the photo, slowly orbit the shoe, and end on a close-up of the upper.
- Use Kling AI to browse the motion library and preview saved motions. After I select one, apply it to my subject image with an available motion-control model to generate a video.
- Use Kling AI to save the front and side views of my character as my first reusable Element, named “Xiaobai”, to help keep the character consistent in later creations.

[中文推荐提示词](skills/kling-ai/references/prompt-examples.md#suggested-prompts-in-chinese) · [More examples](skills/kling-ai/references/prompt-examples.md#natural-language-requests)

Element workflows cover creation, listing, details, updates, deletion, and
reuse with compatible models. Motion-library management is read-only; the
Skill does not invent motion creation or deletion tools. Motion control needs
a subject image and exactly one motion source. Available models, arguments,
and resource limits always come from the live MCP tools.

- **Images** support text-to-image and reference-guided editing. The plugin prefers the current entry point's available default when it fits the request. A continuous image series requires a model that explicitly supports both series generation and the requested count.
- **Kling 4.0** supports text-to-video, image-to-video with first, last, or intermediate keyframes, and omni reference with images, videos, audio, and Elements. It supports 3–30 seconds at 720p, with 1080p available to eligible accounts.
- **Kling 4.0 Flash** supports text-to-video, image-to-video with a single first frame, and omni reference with images, videos, and Elements. It supports 3–20 seconds at 720p.
- Both models support smart shot splitting in omni reference, with a choice between generating synchronized sound and retaining source-video audio. A separate audio reference requires a declared audio input slot, as offered by Kling 4.0 omni reference.

Video discovery includes `omni_ref_video` when available, even for text-only requests. The plugin prefers an eligible Kling 4.0 model for standard delivery and Kling 4.0 Flash when speed or credit savings matter and its capabilities fit. Available models, settings, and reference limits come from the current account's live MCP response. When the user leaves the model open and no compatible 4.0 model is available, the plugin uses a compatible live default. If a named model is unavailable or incompatible, Claude reports the specific limitation.

The Skills require explicit approval of the final settings before a
credit-consuming generation and submit each approved intent at most once.
Library queries and uploads do not authorize generation. They discover the
live tool schemas at runtime and let the host render any MCP App resource
returned by the remote server.

Claude checks the current balance before submitting. If credits are
insufficient, top up the same China account on the Kling AI website and say
“Continue the previous generation” in the same conversation. Claude checks
the original task first if it has a task number. If a new submission is
eligible, Claude rechecks the balance and resumes with the approved settings.

Generation sends the prompt, selected settings, and reference media URLs or
Element IDs to Kling. Local reference files are uploaded only when needed
through the upload flow returned by Kling. Generation and asset storage run
on the remote service; account and task queries also contact Kling.

Quality defaults follow the reference plugin: live-supported `2k` images and
`1080p` video for normal delivery, with higher quality for commercial work and
lower-cost modes when requested. Final settings are shown before submission.

## Validate

```bash
claude plugin validate . --strict
claude plugin validate .claude-plugin/plugin.json --strict
```

Check the frontmatter and bundled links for all three Skills. Run the
developer portal's directory check for the exact release commit. See the
[official pre-submission checklist](https://claude.com/docs/plugins/pre-submission-checklist).

Keep development scripts, tests, local servers, credentials, and generated
media out of the plugin directory submitted for review.

## Claude Desktop and Cowork

Claude supports plugins in chat, Claude Desktop, and Cowork, with component
support varying by surface; see [Use plugins in Claude](https://support.claude.com/en/articles/13837440-use-plugins-in-claude).
This package's instructions target Claude Code. Its authentication flow,
uploads, and result presentation have not been validated on the other
surfaces. Do not assume Claude Code commands such as `/mcp` apply there; use
the target host's native plugin and connector controls.

## China service policies and data flow

- [Privacy policy](https://klingai.com/docs/privacy-policy)
- [User service agreement and support contacts](https://klingai.com/docs/user-policy)

The plugin connects to the declared China MCP server at `https://klingai.com/mcp/plugin`. Prompts and reference media are sent to Kling AI for processing. When the server returns an upload ticket, the host sends the selected file and ticket directly to the provider-supplied upload URL; this is an additional HTTP upload step outside the MCP transport. Do not log the ticket or signed URL.

Image and video generation consumes Kling AI credits. The bundled skills do not implement checkout, payment, or credit purchases. Refer to the China service policies for account, content storage, deletion, and paid-service terms. The privacy policy does not specify a fixed retention period for data received from Claude.
