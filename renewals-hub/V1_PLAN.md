# Renewals Hub: V1 plan

A single page where a renewals manager (RM) opens their book of business and does not have to leave it. V1 covers seeing every account, its admins, usage, who else is working it, and drafting outreach into Gmail. It does not write to Salesforce.

`index.html` in this folder is a clickable prototype with made-up data. It shows the screens and flows. The real build swaps the example `accounts` array for live queries.

## Where to build it: BigBrain Studio

BigBrain Studio (Z2H) is the fastest approved route. Nothing new needs to go to IT:

| Need | How Studio covers it |
| --- | --- |
| Log in with work email | Everyone opening a Studio app is already signed in as themselves. The app knows the viewer's email. |
| Only RMs can open it | Set the app to Whitelist or Private in AI Hub > Apps. |
| Each RM sees only their accounts | Filter every query on `primary renewal owner = viewer email`. |
| Snowflake data (Salesforce + BigBrain tables) | Native Snowflake channel, with queries in backend handlers (now the Studio standard). |
| Kramer | Embedded BigLlama chat locked to Kremer, which already knows what's on the page ("add a chat with Kremer to my app"). |
| Salesforce (later) | MCP Gateway data channel. Each user connects their own Salesforce once, and every call runs with that user's own permissions. |
| Claude API key | Not needed for V1. The renewal brief is built from rules, and the Kremer chat covers free-form questions. |

The Studio global launch week is 2–5 Nov, with office hours in the London office. Support is in #ask-bigbrain-studio.

## What V1 includes

From the shadowing with Bar Schwartz and Arad Lerner (22 Oct) and the Charlotte sync (19 Oct):

1. **Book of business**, sorted by contract end date, with filters for ≤90 days, not contacted, follow-up due, high risk and wire only.
   - Replaces taking screenshots of the Salesforce report and pasting them into Claude.
   - Counts must match the Salesforce renewal report. Use the same owner field and the same filters, and check the numbers against Bar's report before go-live.
2. **Admins on every account**, pulled from BigBrain account users.
   - Copy one, or copy all at once.
   - Admins whose email isn't on the company domain are left out (Bar's existing rule).
   - When an account has fewer than three admins, the most active users are added (Arad's rule).
3. **Signals**, surfaced without opening each account:
   - cancel-on-renewal and change-plan requests from the admin portal
   - usage drops
   - seats near their limit
   - AE, AM, CSM or partner activity and open opportunities in the renewal window
4. **Account file**, with:
   - a renewal brief
   - usage: MAP, WAP, active boards, automations, AI credits, integrations
   - a "who else is on this account" timeline
   - an RBR readiness score
5. **Outreach composer**:
   - All admins go on one thread.
   - The template is picked by cadence and risk: first outreach, follow-up or high-risk.
   - The RM can edit the draft. "Open as Gmail draft" opens a pre-filled compose window in the RM's own Gmail, so the RM still clicks Send.
   - It also produces a ready-to-paste Salesforce renewal comment.

## Data mapping (to confirm with Kremer / RevOps)

| Field in the app | Likely source |
| --- | --- |
| Accounts, ARR, contract end, stage, payment type, risk tier | Salesforce renewal opportunity + contract, via Snowflake (match the RM renewal report fields) |
| Owner filter | Salesforce `primary renewal owner` user → email |
| Admins and active users | BigBrain account users / roles |
| MAP, WAP, boards, automations, AI credits | `bigbrain.l4.fact_accounts_daily` and related BigBrain usage tables |
| Cancel on renewal, change plan | BigBrain admin-portal events |
| AE / AM / partner activity | Salesforce activities + open non-renewal opportunities on the same account |
| Outreach status (sent / replied / follow-up due) | V1: a status the RM marks in the app. V2: read from Gmail threads or Salesforce activity |

Ask Kremer, for example: "Which tables give me renewal opportunities by primary renewal owner, with contract end date, ARR, payment method and risk tier, matching the RM book-of-business report?"

## Out of V1, next in line

- **Salesforce write-back**: log the outreach, move pre-renewal → renewal kickoff, close paid credit-card opportunities. Needs MCP Gateway Salesforce OAuth with write scope, or alignment with Project Oz (RevAI: Guy Shachar, Ariel Yusim, Alex Pevzner).
- **Automatic follow-ups** in the same thread that stop when someone replies (Arad's requirement that Salesloft didn't meet).
- **One-click RBR deck** from Bar's templates.
- **Partner-renewal view.** Wait for the decision on partner support next year before building it.

## Suggested Studio prompt

> Build an app called "Renewals Hub" for renewals managers. Use the attached index.html as the design and behaviour reference. Replace the example `accounts` array with backend-handler Snowflake queries filtered to renewal opportunities where the primary renewal owner's email equals the signed-in viewer's email. Pull admins and the most active users from BigBrain account users, usage from BigBrain daily account facts, cancel/plan-change requests from admin-portal events, and AE/AM/partner activity from Salesforce activities and open opportunities. Keep "Open as Gmail draft" as a Gmail compose link. Add a chat with Kremer. Set the app to Whitelist.
