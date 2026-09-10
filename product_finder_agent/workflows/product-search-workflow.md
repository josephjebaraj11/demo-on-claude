# Product Search Workflow

This defines the process the Product Finder agent follows on every request, before any searching begins. See `CLAUDE.md` for the underlying research/output rules referenced in stages 4–5.

## Stages

### 1. Intake
Capture the two required inputs:
- **Product name**
- **Location**

If either is missing, ask for it before continuing. Do not proceed to Clarify until both are present.

### 2. Clarify
Check the product name for ambiguity. Common gaps to look for:
- Brand / manufacturer unspecified (e.g. "wireless earbuds")
- Model or model number unspecified (e.g. "iPhone" without a generation)
- Size / capacity unspecified (e.g. storage size, screen size, volume)
- Color / finish unspecified
- Generation / version / year unspecified
- New vs. used/refurbished unspecified
- Single item vs. bundle/kit unspecified

If one or more of these are ambiguous or materially affect price/results, ask targeted follow-up question(s) covering only the missing specifics. Don't ask about details that don't matter for pricing (e.g. don't ask about color if the user's location or budget is the more relevant gap). Skip this stage entirely if the product name is already fully specific (e.g. "Sony WH-1000XM5 headphones, black").

### 3. Confirm
Restate the fully-resolved product and location back to the user in one line before searching, e.g.:

> Confirming: Sony WH-1000XM5 wireless headphones (black), near Bengaluru — searching now.

Proceed once the user confirms or does not correct the restatement. If the user corrects any detail, update and re-confirm before moving on.

### 4. Research
Run the existing CLAUDE.md "What to do" steps (web search, local store search, collect price/seller/link/address, rank ascending by price, dedupe, exclude invalid listings) against the confirmed product and location. No changes to those rules — this workflow only gates when they start.

### 5. Deliver
Return results using CLAUDE.md's existing "Output format" (bulleted list, best price first). Keep results conversational in chat — do not write output files unless the user separately asks to save them.

## Example

> **User:** Find me a good deal on a robot vacuum in Pune.
> **Agent (Clarify):** A few options to narrow this down — do you have a brand or budget in mind (e.g. Ecovacs, Roborock, Eufy), and do you need mopping support or just vacuuming?
> **User:** Roborock, vacuum + mop, under ₹30,000.
> **Agent (Confirm):** Confirming: Roborock robot vacuum with mop function, budget under ₹30,000, near Pune — searching now.
> *(Agent then runs Research → Deliver as normal.)*
