# Windframe HTTP API Anti-Patterns

Use this checklist to avoid behavior that violates the Windframe API workflow.

---

## Choosing Style Or Color For The User

Bad:

```json
{
  "uiStyle": "[hard-coded style]",
  "primaryColor": "[hard-coded color]"
}
```

when the user did not choose those values.

Fix:

1. Call `GET /ui-styles`.
2. Present the available styles and practical color options.
3. Recommend suitable pairings.
4. Ask for missing choices only; retain valid user selections for follow-up edits. Never silently replace an unsupported color.
5. Call `POST /design-context` with the selected values.

---

## Skipping The UI Styles Fetch

Bad:

```text
Use a fixed style for a fixed UI category without checking the live API.
```

Fix:

Treat `GET /ui-styles` as authoritative. Local docs can guide recommendations, but the API output controls what can be offered.

---

## Treating Style Context As Finished Code

Bad:

```text
Here is your result: { "context": "..." }
```

Fix:

Use the returned `context` and optional `fonts` to generate or convert UI code in the user's project framework.

---

## Using The Wrong Parameter Name

Bad:

```json
{
  "theme": "[selected color]"
}
```

with `POST /design-context`.

Fix:

Use `primaryColor`:

```json
{
  "primaryColor": "[user-selected color, current, or custom hex]"
}
```

---

## Vague Prompts

Bad:

```text
A landing page for a startup.
```

Fix:

Include product, audience, goal, sections, copy direction, CTAs, content counts, interactions, and constraints:

```text
A landing page for a B2B contract management SaaS targeting legal teams. Hero headline: "Sign faster, dispute less". Sections: hero with demo CTA, 4 feature cards, security proof, 2 testimonials, 3-tier pricing, FAQ, footer.
```

---

## Overloading A Conversion

Bad:

```text
The user asked for a visual restyle, so also rewrite the product positioning, add pricing, change navigation, and create a mobile screen.
```

Fix:

Preserve content and functionality outside the requested scope. If the user explicitly requests structural or content changes alongside restyling, handle them together; ask only when a material ambiguity remains.

---

## Ignoring Plan Restrictions

If the API returns `pro_plan_required`, tell the user the feature requires Windframe Pro and provide `https://windframe.dev/pricing`.

Do not keep retrying the same call without resolving the account state.

---

## Leaving Placeholder Content

Avoid final UI with:

- `Lorem ipsum`
- `[Insert text here]`
- `{{placeholder}}`
- Generic company names when the user provided a real product name

If the user did not provide copy, write reasonable copy from their brief and state any assumptions.

---

## Framework Mismatch

Do not generate React in a Vue project, Vue in a Svelte project, or static HTML inside an existing component app unless the user requested it.

Check the package manifest, framework configuration, and existing components before writing. File extensions and folders alone do not establish the framework: `.tsx` is not exclusive to React, and `app/` or `pages/` alone does not prove Next.js.

---

## Auth Misuse

Authentication happens through the `Authorization: Bearer` or `x-api-key` header. Never hard-code API keys into project files. Never log or expose keys in output.

Check for a missing local key before calling the API. Revisit setup when the key is missing, invalid, or the user changes credentials; do not treat network and service failures as proof of a bad key. Follow [authentication.md](authentication.md) for setup and [api.md](api.md) for error handling.
