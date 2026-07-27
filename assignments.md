# Day 2 Assignments — Real-Time Collaborative Workshop

Find your name, and follow the branch name and task exactly as written.

| Participant | Branch | Task |
|---|---|---|
| (assign name A) | `participant-a/navbar-brand` | In `src/navbar.html`, change the brand text on **line 8** from "MyApp" to "Tech4Dev Workshop" |
| (assign name B) | `participant-b/footer-year` | In `src/footer.html`, update the copyright year on line 4 |
| (assign name C) | `participant-c/navbar-links` | In `src/navbar.html`, add a new nav link on **line 8** (same area as Participant A — yes, on purpose) |
| (assign name D) | `participant-d/readme-badge` | Add a status badge to the top of the root `README.md` |
| ... | ... | *(repeat pattern, pairing at least one more A/C-style collision per 8 participants)* |

**Why lines collide on purpose:** Participants A and C are both editing line 8 of the same
file. Whoever opens their Pull Request second will see a merge conflict against `main` once
A's PR has merged first. This is intentional — see Lab 5 and the Merge Conflict Simulation
Guide, Scenario "Feature Branch Collisions."