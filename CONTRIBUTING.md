# Contributing — Adding Tools & Skills

This dashboard is data-driven. Adding a new Pika tool or skill = one object in the `TOOLS` array inside `index.html`. No other code changes needed.

---

## Adding a Tool

Find the `TOOLS` array near the top of the `<script>` block in `index.html` and add an entry:

```js
{
  id: 'tool_your_tool',        // unique, snake_case
  badge: 'tool',
  category: 'Generation',       // must match an existing category (see list below)
  name: 'Your Tool Name',
  cmd: 'pika__your_tool_name',  // MCP tool ID (underscores, not hyphens)
  desc: 'One-line description of what this tool does.',
  params: [
    { name: 'prompt', label: 'Prompt', type: 'textarea', req: true },
    { name: 'duration', label: 'Duration (seconds)', type: 'number' }
  ]
}
```

**Available categories:** `Generation` · `Image & Music` · `Edit & Effects` · `Voice & Audio` · `Identity` · `Utilities`

**Field types:** `textarea` · `text` · `number` · `select` (add `options: ['a','b']`) · `checkbox`

---

## Adding a Skill

Skills have a richer schema — explainer block, optional params, image-attach note, and a custom command builder function.

```js
{
  id: 'skill_your_skill',       // unique, snake_case
  badge: 'skill',
  cmd: '/pika:your-skill',       // exact slash command
  name: 'Your Skill Name',
  desc: 'Short description shown on the skill card.',

  // Shown at the top of the modal — explain what Pika handles automatically
  explainer: '<strong>Just give Pika a topic or URL.</strong> It handles the rest — script, visuals, audio, edit. Describe what the workflow produces and how long it takes.',

  // Required inputs (shown first in the modal form)
  params: [
    { name: 'input', label: 'Topic or URL', type: 'textarea', req: true }
  ],

  // Optional inputs (shown in a secondary section)
  optionalParams: [
    { name: 'bg_img',       label: 'Background Image URL', type: 'text' },
    { name: 'aspect_ratio', label: 'Aspect Ratio',         type: 'select', options: ['16:9','9:16','1:1'] },
    { name: 'no_captions',  label: 'Disable captions',     type: 'checkbox' }
  ],

  // Shown below the form — image attachment guidance
  attachNote: 'Want a custom image? Paste a hosted URL above, or drag the file into your Claude terminal after running the command.',

  // Builds the exact command string from form values
  cmdFn: function(a) {
    let cmd = '/pika:your-skill input="' + a.input + '"';
    if (a.aspect_ratio) cmd += ' aspect_ratio="' + a.aspect_ratio + '"';
    if (a.no_captions)  cmd += ' --no-captions';
    return cmd;
  }
}
```

---

## Workflow

1. Edit `index.html` — add your entry to the `TOOLS` array
2. Preview locally: `python3 -m http.server 8080`
3. Click the new card → verify modal fields and generated command
4. Commit and push → GitHub Pages deploys automatically

```bash
git add index.html
git commit -m "Add [tool/skill name]"
git push origin main
```

---

## Reference — existing skill

```js
{
  id: 'skill_podcast',
  badge: 'skill',
  cmd: '/pika:podcast',
  name: 'Podcast',
  desc: 'Turn any topic or URL into a two-host dialogue video (~1 min).',
  explainer: '<strong>Just give Pika a topic or URL.</strong> It writes the script, generates two AI hosts, voices them, and edits a full conversation video — roughly one minute long. You only need the topic.',
  params: [
    { name: 'input', label: 'Topic or URL', type: 'textarea', req: true }
  ],
  optionalParams: [
    { name: 'bg_img',       label: 'Studio Background Image URL', type: 'text' },
    { name: 'host_a_img',   label: 'Host A Portrait URL',         type: 'text' },
    { name: 'host_b_img',   label: 'Host B Portrait URL',         type: 'text' },
    { name: 'aspect_ratio', label: 'Aspect Ratio',                type: 'select', options: ['16:9','9:16','1:1'] }
  ],
  attachNote: 'Want custom host portraits? Drag images into your Claude terminal after running the command, or paste a hosted image URL above.',
  cmdFn: function(a) {
    let cmd = '/pika:podcast input="' + a.input + '"';
    if (a.bg_img)       cmd += ' bg_img="' + a.bg_img + '"';
    if (a.host_a_img)   cmd += ' host_a_img="' + a.host_a_img + '"';
    if (a.host_b_img)   cmd += ' host_b_img="' + a.host_b_img + '"';
    if (a.aspect_ratio) cmd += ' aspect_ratio="' + a.aspect_ratio + '"';
    return cmd;
  }
}
```
