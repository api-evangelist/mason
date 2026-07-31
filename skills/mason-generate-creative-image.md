---
name: Generate a creative image from a template
description: Use Mason's Generation (Genie) API to render an image from a template and retrieve the result.
api: openapi/mason-generation-openapi.json
operations:
  - Get_Template_Mappings_api_v1_genie_template__template_id__get
  - Generation_api_v1_genie_images_post
  - Get_Image_api_v1_genie_images__uid__get
---

# Generate a creative image from a template

Mason's Generation ("Genie") API renders images from templates as **long-running
tasks**. Base host: `https://api.getmason.io`. All calls require an OAuth2 bearer
token (`Authorization: Bearer <token>`; scheme `OAuth2PasswordBearer`).

## Steps

1. **Inspect the template's fields.** Call `Get_Template_Mappings_api_v1_genie_template__template_id__get`
   (`GET /api/template/{template_id}`) to read the editable field mappings for the
   template you intend to render.
2. **Submit the generation.** Call `Generation_api_v1_genie_images_post`
   (`POST /api/images`) with a `GenerationModel` body supplying the template id and
   the field values. This returns a `GenerationResponse` with a `uid` — generation
   is asynchronous, so do not expect the image inline.
3. **Poll for the result.** Call `Get_Image_api_v1_genie_images__uid__get`
   (`GET /api/images/{uid}`) until the `GenerationStatusResponse` reports the image
   is ready, then read the asset URL.

## Rules

- Handle `401` as an auth failure (refresh the bearer token) and `500` on the
  retrieval endpoints as a transient server error — retry with backoff.
- `422` means your request body failed validation; read `detail[].loc`/`detail[].msg`
  (see errors/mason-problem-types.yml).
- Generation is long-running: poll `GET /api/images/{uid}` rather than blocking.
