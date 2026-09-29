# Jordan Neidlinger’s personal homepage

A small static website for **https://neidlinger.org**, hosted on GitHub Pages. Plain HTML and CSS, with no build tools or dependencies.

## Edit and preview

- Update the homepage text and links in `index.html`.
- Change the design in `assets/style.css`.
- Replace `assets/portrait.png` to update the photo. The current photo and contact links were recovered from the old `gh-pages` branch.
- Preview locally with `python3 -m http.server 8000` and open http://localhost:8000.

The old Hugo output remains on `gh-pages`. Its source is in [jneidlinger-hugo](https://github.com/jneidlinger/jneidlinger-hugo). Neither needs to build or deploy this homepage.

## Activate GitHub Pages

After this change is merged, open [Settings → Pages](https://github.com/jneidlinger/jneidlinger.github.io/settings/pages):

1. Set **Source** to **Deploy from a branch**.
2. Select **main** and **/ (root)**, then save.
3. Set **Custom domain** to **neidlinger.org**, then save. Do this before changing DNS.
4. Apply the Cloudflare records below.
5. Wait for GitHub’s DNS check and certificate provisioning to finish, then enable **Enforce HTTPS**.

The `CNAME` file matches the custom domain. `.nojekyll` bypasses Jekyll processing. Commits to `main` publish automatically when Pages is configured as above.

## Repair the domain in Cloudflare

Public DNS checked on September 29, 2026 showed:

- Nameservers: `karina.ns.cloudflare.com`, `quentin.ns.cloudflare.com`.
- `neidlinger.org` and `www.neidlinger.org` both pointed to `206.189.191.222`.
- No AAAA records or `www` CNAME were returned.
- GitHub Pages was enabled, publishing `/` from `gh-pages`, with `www.neidlinger.org` as its custom domain and HTTPS enforcement enabled. Its last successful build was January 28, 2022.

Namecheap is the registrar, but **Cloudflare manages the active DNS**. Keep the existing nameservers at Namecheap.

In Cloudflare, select **neidlinger.org → DNS → Records**. Change the existing website records so that the final set is:

| Type | Name | Content | Proxy status | TTL |
| --- | --- | --- | --- | --- |
| A | @ | 185.199.108.153 | DNS only | Auto |
| A | @ | 185.199.109.153 | DNS only | Auto |
| A | @ | 185.199.110.153 | DNS only | Auto |
| A | @ | 185.199.111.153 | DNS only | Auto |
| CNAME | www | jneidlinger.github.io | DNS only | Auto |

Replace the apex A record targeting `206.189.191.222` with the first GitHub address, then add the other three. Replace the existing `www` A record with the CNAME above. If Cloudflare requires removing the `www` A record before adding a CNAME, record its original value first.

Only change the website records for `@` and `www`. Preserve email (MX/TXT), verification records, and unrelated subdomains. Use DNS only during GitHub DNS validation and HTTPS provisioning. GitHub redirects `www.neidlinger.org` to `neidlinger.org` when both records and the Pages custom domain are configured correctly.

Optionally verify domain ownership in [GitHub account Settings → Pages](https://github.com/settings/pages), using the unique TXT record GitHub supplies. Do not guess that record’s value.

DNS updates can take up to 24 hours, though the current A records have a 300-second TTL. Check both hostnames over HTTPS after GitHub reports that DNS and the certificate are ready.

## References

- [GitHub: managing a custom domain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
- [GitHub: HTTPS for Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/securing-your-github-pages-site-with-https)
- [Cloudflare: managing DNS records](https://developers.cloudflare.com/dns/manage-dns-records/how-to/create-dns-records/)
