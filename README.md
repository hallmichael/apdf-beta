# APDF beta downloads

Public download page and release host for APDF: Anomalous Phenomena Defence Force. This repo holds the static website and the beta builds only. The game source lives in a private repo.

Site: https://hallmichael.github.io/apdf-beta/
Releases: https://github.com/hallmichael/apdf-beta/releases
Issues: https://github.com/hallmichael/apdf-beta/issues

## How it fits together

- `index.html`, `style.css`, `assets/` are the whole site. No build step. GitHub Pages serves the `main` branch root.
- The download buttons call the public GitHub API (`/repos/hallmichael/apdf-beta/releases/latest`) and fill in the version, date and file sizes. Until the first release exists they show "No release yet" and link to the releases page.
- Builds are not made here. The private game repo's `build` workflow exports Windows and macOS on a `v*` tag and publishes the release to this repo using the `BETA_RELEASE_TOKEN` secret stored on the private repo. Asset names are `APDF-windows-<tag>.zip` and `APDF-macos-<tag>.zip`; the page matches on `windows` and `macos` in the file name.

## Updating the site

Edit `index.html` or `style.css`, commit, push to `main`. Pages redeploys in about a minute.

Screenshots in `assets/shots/` are 1600 px wide JPEG at quality 85, made with `sips -s format jpeg -s formatOptions 85 -Z 1600 in.png --out out.jpg`.

## Custom domain

Pick the domain, then do the three steps below. Until then the site is only at the github.io address and `CNAME` stays empty.

### 1. CNAME file

Put the bare hostname on one line in `CNAME` at the repo root, with no scheme and no trailing slash, and push. Use the `www` host if you want both apex and `www` to work; GitHub redirects the other one.

```
www.example.com
```

### 2. DNS records

Apex (`example.com`), four A records and four AAAA records pointing at GitHub Pages:

```
example.com.  A     185.199.108.153
example.com.  A     185.199.109.153
example.com.  A     185.199.110.153
example.com.  A     185.199.111.153
example.com.  AAAA  2606:50c0:8000::153
example.com.  AAAA  2606:50c0:8001::153
example.com.  AAAA  2606:50c0:8002::153
example.com.  AAAA  2606:50c0:8003::153
```

`www`, one CNAME record:

```
www.example.com.  CNAME  hallmichael.github.io.
```

If the domain is on Cloudflare, set these records to DNS only (grey cloud) until the certificate is issued, otherwise GitHub cannot verify the domain.

### 3. Repo settings

Settings, Pages, Custom domain: enter the same hostname as the `CNAME` file and save. Wait for the DNS check to pass (minutes to an hour), then tick Enforce HTTPS. The domain can also be set from the terminal:

```bash
gh api -X PUT repos/hallmichael/apdf-beta/pages -f cname=www.example.com
```

## Release token on the private repo

The private repo publishes releases here with a secret called `BETA_RELEASE_TOKEN`. It was first set from a personal `gh` session token, which is broader than it needs to be. Replace it with a fine grained personal access token when you have a minute:

1. GitHub, Settings, Developer settings, Personal access tokens, Fine-grained tokens, Generate new token.
2. Resource owner `hallmichael`, repository access: only `hallmichael/apdf-beta`.
3. Repository permissions: Contents, Read and write. Nothing else.
4. Expiry as you like; a one year token with a calendar reminder is fine.
5. Store it on the private repo without echoing it:

```bash
pbpaste | gh secret set BETA_RELEASE_TOKEN --repo hallmichael/earth-defence
```

Clear the clipboard afterwards.
