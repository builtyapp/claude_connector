---
description: Show the building permit history and system ages of a US address from Builty
argument-hint: <street address, city, state ZIP>
---

Look up this US address with the Builty `lookup_addresses` tool: $ARGUMENTS

1. Parse it into `address` (starting with the house number), `city`, `state` (two letters) and `zipcode`. If the state is missing, ask for it.
2. Call `lookup_addresses` with that one address.
3. If nothing matches, say so. Offer `suggest_addresses` with the street prefix to find the address Builty knows.
4. Otherwise report:
   - The matched address, `property_type` and `location_id`.
   - A short table of the system ages: building, roof, electrical, plumbing, water heater and HVAC. Mark a lower bound (`age_min`) as "≥ N years" and a missing age as "no permit evidence".
   - The permits, newest first: date, status, work type and description, project value, and contractor.
   - How many permits were left out, if any. Offer to fetch them all (`get_properties` with `max_permits: 0`).
