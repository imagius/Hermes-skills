# Hermes skills — publishable set

Two skills from my Hermes setup, cleaned of anything machine-specific and ready for a public
registry. MIT licensed.

## spec-driven

A project **constitution** (testable articles that constrain every change) plus a **converge** loop
that audits a codebase against that constitution and the plan, classifies every gap as
missing / partial / contradicts / unrequested, and appends tasks until the work actually matches the
spec. Comes from stealing the two genuinely useful ideas out of Spec Kit without its ceremony.

- `spec-driven/SKILL.md`
- `spec-driven/references/constitution-template.md`

## short-form-video

Renders 1080×1920 vertical shorts (Shorts / Reels / TikTok) from a text spec, with burned-in
captions, optional screenshots and per-scene voice-over takes — Pillow for frames, ffmpeg for the
assembly. Written from the failures: the concat demuxer drifts on stills, ffmpeg 9 dropped `-vsync`,
and text cards mean an episode is watchable before a single screenshot exists.

- `short-form-video/SKILL.md`

## Install

From a file path (local checkout):

```
hermes skills install imagius/Hermes-skills/spec-driven
```

From a raw URL to the SKILL.md:

```
hermes skills install https://raw.githubusercontent.com/imagius/Hermes-skills/main/skills/short-form-video/SKILL.md --name short-form-video
```

Inspect before installing — these come from a registry, so treat them as third-party code:

```
hermes skills inspect imagius/Hermes-skills/spec-driven
```

## Publishing (maintainer notes)

```
# new skills go in skills/<name>/ and are committed directly (the publish
# command forks the target repo, which GitHub refuses on your own repo)
git add skills && git commit -m 'add skill: <name>' && git push
```

Neither the skill nor its examples contain credentials, personal paths or private hostnames;
keep it that way when editing. Anything that encodes *your* infrastructure belongs in a private
skill, not this repo.

## License

MIT — see `LICENSE`.
