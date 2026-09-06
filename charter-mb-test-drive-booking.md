# Session-Based Test Charter

## Charter
Explore the Mercedes-Benz online Test Drive Booking workflow's **input validation, slot-booking integrity, and dealer-data accuracy**, to assess whether invalid or conflicting customer input, and mismatched dealer data, can compromise a real booking outcome (unreachable customer, overbooked slot, or a customer arriving at a closed dealership).

- **Application:** Test Drive Booking (mercedes-benz.co.uk)
- **URL under test:** https://www.mercedes-benz.co.uk/passengercars/mercedes-benz-cars/online-testdrive.html
- **Tester:** Sadgi Nayak
- **Date:** 06.09.2026
- **Time-box:** 45 min
- **Environment:** Production, Chrome, Windows

## Areas Explored
- [x] Personal Details form field validation (First Name, Last Name, Email, Phone)
- [x] Booking submission with duplicate slot (same dealer, date, time)
- [x] Dealer search with an out-of-region/foreign postal code
- [x] Bookable time-slot range vs. dealer's actual listed operating hours (checked across multiple dealers)
- [ ] Consent checkbox behavior (Consent/Skip on Submit) — deprioritized due to time-box; recommended as next session's charter
- [ ] Back-navigation persistence after reaching Personal Details — deprioritized due to time-box; recommended as next session's charter

## Test Notes (chronological)
1. Entered German postal code `86154` (Augsburg) into the UK dealer-search location field → proceeded normally and returned UK dealer results (no rejection, no "no results found"). Proceed button is disabled only when the field is fully empty.
2. Checked a dealer's Sunday hours via Google (listed as closing 16:00) → booking system still offered an 18:00 Sunday slot, and the booking was accepted.
3. Repeated hours check across additional dealers with different listed operating hours → all dealers offered the same generic time-slot range up to 18:00, regardless of individual listed hours.
2. Entered email `s@g.c` (structurally invalid , single-character TLD) → accepted, booking submitted.
2. Entered phone `0000000000` → accepted, booking submitted.
3. Entered First Name `12` → accepted, booking submitted.
4. Entered Last Name `12` → accepted, booking submitted.
5. Repeated the exact same dealer + date + time slot 3 times with invalid data → all 3 bookings submitted with no conflict/duplicate warning.


## Findings

| # | Finding | Severity | Priority | Reproduction |
|---|---------|----------|----------|--------------|
| 1 | Bookable time slots do not reflect individual dealer operating hours, same generic slot range offered across dealers regardless of listed hours | High | High | Compare listed hours (external source) vs. offered slots across ≥2 dealers |
| 2 | No duplicate/conflict check — identical dealer+date+time slot bookable multiple times | High | High | Book the same slot 3x in a row |
| 3 | Email field accepts structurally invalid addresses | Medium | Medium | Submit with email `s@g.c` |
| 4 | Phone field accepts implausible input (e.g., all-zero) with no format/pattern validation | Medium | Medium | Submit with phone `0000000000` |
| 5 | Dealer-search location field accepts a non-UK postal code format and still returns results, instead of rejecting or showing "no results" | Medium-High | Medium | Enter a German postcode (e.g., `86154`) in UK dealer search |
| 6 | First Name field accepts numeric-only input | Low | Low | Submit with First Name `12` |
| 7 | Last Name field accepts numeric-only input | Low | Low | Submit with Last Name `12` |


## Open Questions / Risks for Follow-Up
- Is slot availability enforced anywhere server-side, or is the "confirmation" purely a lead sent to the dealer with no real calendar lock?
- Are time slots generated from a fixed template independent of any dealer-hours data source, or is there a per-dealer hours feed that isn't being applied correctly?
- Does the dealer's CRM re-validate contact info before acting on the lead, or does invalid data reach the dealer as-is?
- Is there a rate limit preventing automated mass-submission of fake bookings (spam/abuse risk)?
- Does the location/postcode field attempts to look for valid location, or does it fall back to a default/nearest-match result set when it can't parse the input?

## Session Outcome
Mission achieved — identified 7 reportable findings requiring investigation (2 High-severity systemic issues: dealer-hours mismatch and duplicate booking) within the 45-minute time-box. Two planned areas (consent checkbox, back-navigation persistence) were not reached.
