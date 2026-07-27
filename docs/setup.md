# Local Setup

1. Install Git (v2.30+) — check with `git --version`.
2. Clone the repo:
   \`\`\`bash
   git clone https://github.com/tech4dev-tcla/git-masterclass-workshop.git
   cd git-masterclass-workshop
   \`\`\`
3. Configure your identity (first time only):
   \`\`\`bash
   git config user.name "Your Name"
   git config user.email "you@example.com"
   \`\`\`
4. This project has no build step — it's intentionally lightweight so it runs on modest
   hardware. Open files directly in your editor; no `npm install` required for the Git labs.

## Starting the Sample App (optional, for context only)

Run `npm run dev` to start the local server, or `npm start` if you're using an older
checkout — **this exact ambiguity is deliberate** and is what Lab 3 asks you to "fix" via
a merge conflict. Don't worry about actually running the app; the labs are about Git, not
the app itself.