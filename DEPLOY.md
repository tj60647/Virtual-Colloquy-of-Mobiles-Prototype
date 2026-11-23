# Where this version is served

This branch, `vercel/2024-as-is`, is the frozen October 2024 version of the
Virtual Colloquy of Mobiles demonstration apps (the same files as
`reference/oct-16-2024`, commit 2e22d80). Vercel serves it as static files from
the project `virtual-colloquy-2024` in the `aroughidea` team. `server.js` and the
`Procfile` are kept as the record of how Heroku served it; Vercel does not run them.

`vercel.json` reproduces the Express mapping in `server.js` with no server:

- `apps/public/` is the site root (the home page).
- `apps/<demo>/public/` is served at `/<demo>/`.
- `lib/` is served at `/lib/` and `assets/` at `/assets/`.
- `/<demo>` redirects to `/<demo>/`, as Express did.
- Nothing else in the repository is served (`docs/`, `server.js`, `package.json`).
  Any other path answers 404 "Page not found", as Express did.

Deploy from a checkout with LF line endings (`git config core.autocrlf false`
before checking out on Windows), so the files served are byte-for-byte the
ones in Git.

## To redeploy

From a checkout of this branch:

```sh
vercel link --yes --scope aroughidea --project virtual-colloquy-2024
vercel deploy --prod
```

There is no install step and no build beyond the file copy in `vercel.json`.
