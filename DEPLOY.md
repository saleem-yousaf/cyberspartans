# CyberSpartans: new Projects page with downloadable docs

A new Projects page where visitors can download reference docs. First project is
AI Foundry and Agent 365, populated with the AI agent security pack.

## Files
| File | Repo path | Action |
|------|-----------|--------|
| projects.html | projects.html | NEW |
| securing-ai-agents-briefing.pdf | docs/ai-foundry/securing-ai-agents-briefing.pdf | NEW |
| ai-agent-365-security-pattern.pdf | docs/ai-foundry/ai-agent-365-security-pattern.pdf | NEW |
| ai-agent-kill-switch-factfile.pdf | docs/ai-foundry/ai-agent-kill-switch-factfile.pdf | NEW |
| soc-enablement-pack-foundry-agent365.pdf | docs/ai-foundry/soc-enablement-pack-foundry-agent365.pdf | NEW |
| index.html | index.html | REPLACE (Projects added to nav) |
| capabilities.html | capabilities.html | REPLACE (Projects added to nav) |
| services.html | services.html | REPLACE (Projects added to nav) |
| sitemap.xml | sitemap.xml | REPLACE (projects.html added) |

## Upload note
Create the folder docs/ai-foundry/ and put the four PDFs in it. Do not drag a
folder that nests them deeper. The download links point at
docs/ai-foundry/<file>.pdf, so the files must sit exactly there.

## What the page does
- Matches the site: same dark theme, green accent, Syne / Inter / DM Mono, nav and
  footer. Projects is now a nav item on every page and is in the sitemap.
- Built to hold many projects. The first, "AI Foundry & Agent 365", shows four
  download cards. Add a second project later by copying the proj-block in
  projects.html.

## Decision for you: which docs are public
I included the four that work as public, capability-demonstrating downloads and
carry no client specifics. I deliberately left out two from the pack:
- the kill-switch PRESENTER SCRIPT (internal speaker notes, not a public doc), and
- the mermaid diagrams .md (a supporting source file, not a standalone doc).
Also worth a deliberate choice: the briefing and the pattern were written to be
reusable and unbranded. Publishing them publicly shares that reusable material. If
you would rather keep either private, tell me and I will remove its card and file.

## After deploy
Open /projects.html, confirm it is in the nav on every page, and click each
Download button to confirm the PDF opens. Re-submit sitemap.xml in Search Console.


## Live folder browse button (new)
projects.html now has a "Browse the docs folder (live)" button under the AI Foundry
download cards. On click it reads docs/ai-foundry straight from the GitHub repo
(owner saleem-yousaf, repo cyberspartans) and lists whatever is actually in the
folder, with a download link for each. It is read-only and download-only: it only
reads the folder and offers downloads, it cannot change anything.
- It auto-reflects the folder, so a PDF you add later appears without editing the
  page.
- It fires on click only, so it stays well within GitHub's unauthenticated API
  limit (60 requests per hour per visitor IP).
- It needs the repository to be public (GitHub Pages sites usually are). If the
  repo is private the live listing will not load, and the button shows a fallback
  message while the four cards above keep working.
- If you ever rename the repo or move it to an organisation, update the owner and
  repo values in the small script at the bottom of projects.html.
