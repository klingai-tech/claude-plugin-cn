---
name: kling-ai-generate-image
description: Write or refine Kling AI image prompts, including prompt-only requests, or generate and edit images through the Kling AI MCP in Claude Code. Use for posters, product photography, portraits, covers, advertising stills, visual concepts, and controlled variants.
---

# Kling AI image generation

For prompt-only work, use the relevant creative references below and return the
requested prompt. Preserve composition requirements such as left-side copy space.
Leave out account queries, uploads, unsolicited submission settings, and invitations
to generate.

Use the workflow and quality defaults only when preparing or submitting generation.
If a required image is missing, request it and explain its role before starting
discovery. Follow the [core Kling Skill](../kling-ai/SKILL.md) for OAuth, billing,
submission, and results.

Read [prompt construction](references/prompt-construction.md) for reference images, exact copy, branded assets, or controlled variants. Read [scene patterns](references/scene-patterns.md) for product, advertising, portrait, poster, or cover work. For Element reuse or local media, also read the core [asset workflows](../kling-ai/references/asset-workflows.md).

## Workflow

For a request to continue after a top-up, start with [resuming a paused generation](../kling-ai/references/tool-workflows.md#resuming-a-paused-generation). Query a known task for the original submission before checking credits or preparing another request.

1. Use `text_to_image` for a new image. Use `image_to_image` when source media controls identity, product structure, composition, editable content, or style. An attachment alone does not determine the mode.
2. Ask only for missing information that materially changes the result: subject, destination and aspect ratio, required copy, reference roles, protected facts, or output count.
3. Create or reuse the UUIDv7 `taskTraceId` for this objective, follow [capability discovery](../kling-ai/references/capability-discovery.md), and select a compatible tool/model pair from this account's `who_am_i` response. Apply [model parameters](../kling-ai/references/model-parameters.md) before constructing arguments or reference bindings.
4. Prepare the reference images. Reuse valid original references on continuation and refresh expired ones. Bind the exact returned URL to each live required slot, including `image_1` when declared, and use the schema's exact `inputType` literal. Never silently degrade to text-to-image after an upload failure.
5. Build one prompt covering subject, action, environment, composition, lighting, color, materials, camera language, protected facts, and necessary exclusions.
6. Show the final mode, model, prompt summary, resolution, aspect ratio, output count, and reference roles. Obtain explicit approval under the core Skill's billing rule; honor an existing explicit authorization for the final settings.
7. Follow the [submission workflow](../kling-ai/references/tool-workflows.md#generation). Check the top-level model and required arguments and inputs, then call `query_membership_and_credits` on the same connection immediately before `text_to_image` or `image_to_image`. If the balance is unavailable, nonpositive, or explicitly insufficient, preserve the request and pause submission.
8. Once approved and the balance check permits it, submit once and follow the core Skill's [generation and results](../kling-ai/SKILL.md#generation-and-results). If image-to-image returns a resource error, follow [upload troubleshooting](../kling-ai/references/troubleshooting.md#upload-or-media-reference-fails) and keep the source image task ID separate from the current submission's task ID.

## Mode rules

- Element support follows the selected model's declared resource types and binding argument, not the entry point name.
- Image-to-image may use one or more images with explicit roles. State the purpose of every image when multiple references are present.
- Before using an Element subject, follow the core asset workflow, call `element_get`, and choose a model that explicitly supports the resource type and binding argument. Check individual and combined reference caps, and bind the exact ID with the model's declared prompt and structured syntax.
- For a variant, lock every fact that the user did not ask to change. Change one major dimension per confirmed generation.
- A status request uses only `query_tasks`; it does not create an image.

## Quality defaults

- Prefer live-supported `2k` for normal delivery, `4k` for commercial work, advertising, fine materials, or crop-heavy post-production, and `1k` only for drafts, speed, or credit savings. Never lower a higher live default.
- Infer ratio from destination: `1:1` for square social or product media, `4:5` for portrait feeds, `9:16` for stories or vertical covers, and `16:9` for banners or thumbnails.
- Generate one image unless the user requests more.
- Unless the user explicitly requests text inside the generated image, prefer a clean image with intentional copy-safe space.
- Treat brand names, logos, faces, product structure, and user-provided copy as protected facts. Never invent performance, price, certification, ingredients, awards, effects, or statistics.

Before submission, internally check focal subject, visual hierarchy, safe area, lighting, reference roles, and conflicting instructions. Claim visual QA only when the result was actually inspected.
