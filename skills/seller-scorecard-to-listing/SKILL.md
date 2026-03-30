# seller-scorecard-to-listing

category: automation
trust: local

## description

Runs an end-to-end seller funnel for a Southern California real estate agent: lead enters via a “Ready to Sell?” scorecard, receives a tailored follow-up sequence, and is guided to a listing appointment where the Grand Slam listing offer and negotiation scripts are used.

## instructions

When this skill is active, you:

- Assume the following Hermes skills are available and can be used for reasoning and content:
  - negotiation
  - hundred-million-offers
  - one-page-marketing
  - scorecard-marketing
  - storybrand-messaging

- Your job is to create concrete assets and an execution checklist for this pipeline:

  1) Lead entry
     - Exact landing page offer and copy for the scorecard.
     - Where this link lives (website, email, SMS, QR on postcards, etc.).

  2) Scorecard + results
     - Summary of the scorecard concept and result bands (you can lean on scorecard-marketing).
     - Plain-language explanation of what each band means for the seller.

  3) Follow-up sequence
     - 5–7 touchpoints over the next 14–21 days.
     - For each touchpoint: channel (email/SMS/call), timing, subject/angle, and brief copy.
     - Clear CTA progression: from “low friction” to “book a strategy session.”

  4) Listing appointment
     - Use hundred-million-offers + negotiation to:
       - Frame the offer for this specific lead segment.
       - Provide a short script to open the appointment.
       - Provide a script to handle likely objections.

- Output format:
  - Step-by-step checklist the agent or VA can execute.
  - Copy blocks and scripts ready to paste into CRM/automation tools.
  - No meta-talk about SKILL.md or internals.

## examples

User: “Design the full system so I can give it to my VA and have them set it up in my CRM and email tools.”

Assistant:
- Returns:
  - Landing page copy.
  - Scorecard framing.
  - Full follow-up schedule.
  - Listing appointment scripts.
