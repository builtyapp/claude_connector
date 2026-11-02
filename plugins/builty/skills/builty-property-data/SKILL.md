---
name: builty-property-data
description: Look up US building permit history and permit-derived property characteristics (building, roof, electrical, plumbing, water heater, HVAC ages) with the Builty MCP tools. Use when the user asks about permits, renovations, roof or system age, construction activity, or property condition for a US address, a list of addresses, or an area, e.g. for insurance underwriting, real estate due diligence, or contractor lead lists.
---

# Builty property data

The `builty` MCP server serves one record per US building: its address, `property_type`, permit-derived
`property_characteristics`, and its `permits` (newest first).

## Tools

| Tool | Use for | Cost |
| --- | --- | --- |
| `lookup_addresses` | 1–100 addresses → matched properties, in the order sent | metered |
| `search_near_point` | everything within a radius (≤ 50 km) of a lat/lon, nearest first | metered |
| `search_area` | everything in a `bbox` [west, south, east, north] or a `polygon` of [lon, lat] | metered |
| `get_properties` | re-fetch by `location_id` (e.g. all permits with `max_permits: 0`) | metered |
| `suggest_addresses` | autocomplete a street or city within a state | free |
| `list_property_types` | the `property_type` labels for filters | free |

## Credits

- A returned property costs its `permit_count` in credits, however many permits are shown.
- `count_only: true` is free. It returns `count` (properties) and `permit_count` (the credits a full fetch would cost).
- Before any area search, or an address batch that may be large, run it with `count_only: true` first. Tell the user the cost before fetching when it is large (over ~500 credits).
- Keep `limit` small (the default is 10). Page with `offset` / `next_offset`.
- A 402 error names the page's cost and the balance left. Report it, and suggest a narrower search or a lower `limit`.
- An area matching more than 10,000 properties is refused. Narrow it or add filters.

## Inputs

- Addresses must start with the house number (`"1827 N Main St"`) and need a two-letter `state`.
- `city` and `zipcode` improve ranking but do not filter.
- Unit or suite text is fine. Ranges such as `"113-117 Elm St"` are expanded.
- Unmatched addresses come back with `property: null`. Say which ones did not match; don't invent data.
- Use `matches_per_address` > 1 only when the user wants to choose between candidates. Each result carries `similarity` and `matched_on`.
- Filters work with every locator:
  - `property_type`: a parent label such as `"Residential"` covers its children.
  - `building_age` and `roof_age`: `{min, max}` in years.
- `location_id` is a string. Pass it back unchanged.

## Reading results

- `property_characteristics` holds:
  - `permit_count`, `first_permit_date`, `last_permit_date`
  - `building_age`, plus `roof_`, `electrical_`, `plumbing_`, `water_heater_` and `hvac_` `age`, `age_min` and `age_basis`
  - `*_detail` objects
- Ages are in years and derived from permits. `age_basis` says how each one was derived.
- `age_min` is a lower bound, given when no exact age is known.
- A null age means no permit evidence. It does not mean "new".
- Each permit has `date_submitted`, `date_issued`, `date_finaled`, `status`, `record_type`, `description`, `project_value`, `total_fees`, `contacts`, `inspections`, and `projects` (`work_type`, `project`, `attributes`).
- Results show the newest 10 permits by default (`permits_omitted` gives the rest). Long text is trimmed. Use `max_permits` to change this.
- Lead with what the user asked: dates, system ages, contractors, and project values. Cite the permit dates you relied on.

## Errors

- **401**: the API key is missing or invalid. Ask the user to set it with `/plugin` → builty → configure. Keys come from https://builtydata.com.
- **429**: wait and retry once.
