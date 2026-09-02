# Fathom Analytics Go API

Complete reference of every operation, grouped by resource. See [the README](./README.md) for usage and configuration.

## Contents

- [`Account`](#account)
  - [Get account](#get-account)
  - [Get token](#get-token)
- [`Sites`](#sites)
  - [List sites](#list-sites)
  - [Create site](#create-site)
  - [Get site](#get-site)
  - [Update site](#update-site)
  - [Delete site](#delete-site)
  - [Wipe site](#wipe-site)
- [`Events`](#events)
  - [List events](#list-events)
  - [Delete event](#delete-event)
  - [Create event](#create-event)
  - [Set event currency](#set-event-currency)
  - [Get event](#get-event)
  - [Update event](#update-event)
  - [Delete event](#delete-event-1)
  - [Wipe event](#wipe-event)
- [`Milestones`](#milestones)
  - [List milestones](#list-milestones)
  - [Create milestone](#create-milestone)
  - [Get milestone](#get-milestone)
  - [Update milestone](#update-milestone)
  - [Delete milestone](#delete-milestone)
- [`Reports`](#reports)
  - [Aggregation](#aggregation)
  - [Current visitors](#current-visitors)

## Setup

```go
import (
	"context"
	"fmt"

	sdk "github.com/marclave/fathom-go"
)

client := sdk.NewClient()
```

## `Account`

Information about the account and token behind your API key.

### Get account

Retrieve information about the account that owns the API key.

**Permissions:** Requires a token with full account access (the `*` scope).

**Returns:** An account object.

```go
err := client.Account.List(context.Background())
if err != nil {
	panic(err)
}
```

### Get token

Retrieve metadata about the API token used to make the request, including its name, permissions (abilities), token-format version and timestamps. Your secret token value is never returned.

**Permissions:** Any valid API token.

**Returns:** A token object.

```go
err := client.Account.ListToken(context.Background())
if err != nil {
	panic(err)
}
```

## `Sites`

Create and manage the sites in your Fathom account.

### List sites

Return a list of all sites this API key owns. Sites are sorted by `created_at` ascending to allow you to paginate with ease.

**Permissions:** Requires read access to all sites (`all-sites-readonly`) or full account access.

**Returns:** A list of site objects.

| Direction | Type |
| --- | --- |
| Request | [`SiteListParams`](./site.go) |

```go
err := client.Sites.List(context.Background(), sdk.SiteListParams{
	Limit: sdk.F[int64](10),
})
if err != nil {
	panic(err)
}
```

### Create site

Create a site.

**Permissions:** Requires full account access (`*`).

**Returns:** A site object.

| Direction | Type |
| --- | --- |
| Request | [`SiteNewParams`](./site.go) |

```go
err := client.Sites.New(context.Background(), sdk.SiteNewParams{
	Name: sdk.F[string]("Bugs Bunny Portfolio"),
})
if err != nil {
	panic(err)
}
```

### Get site

Return a single site.

**Permissions:** Requires read access to the site (`all-sites-readonly`, `read:{site_id}` or `manage:{site_id}`).

**Returns:** A site object.

```go
err := client.Sites.Get(context.Background(), "CDBUGS")
if err != nil {
	panic(err)
}
```

### Update site

Update a site. Send only the fields you want to change.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

**Returns:** A site object.

| Direction | Type |
| --- | --- |
| Request | [`SiteUpdateParams`](./site.go) |

```go
err := client.Sites.Update(context.Background(), "CDBUGS", sdk.SiteUpdateParams{})
if err != nil {
	panic(err)
}
```

### Delete site

Delete a site. Careful: you can't undo this, and neither can we.

**Permissions:** Requires full account access (`*`).

**Returns:** Returns a deleted object on success. Otherwise, this call returns an error.

```go
err := client.Sites.Delete(context.Background(), "CDBUGS")
if err != nil {
	panic(err)
}
```

### Wipe site

Previously wiped all pageviews and event completions from a website. This endpoint is no longer available.

```go
err := client.Sites.Wipe(context.Background(), "CDBUGS")
if err != nil {
	panic(err)
}
```

## `Events`

Manage events for a site.

### List events

Return a list of all events this site owns. Events are sorted by `created_at` ascending to allow you to paginate with ease.

**Permissions:** Requires read access to the site (`all-sites-readonly`, `read:{site_id}` or `manage:{site_id}`).

**Returns:** A list of event objects.

> **The id field is going away:** Each event still returns an `id` (the old goal code). We are removing that field on 24 September 2026. Identify events by `name` instead.

> The `currency` field is returned as `null` on list responses. Set it with [Set event currency](#set-event-currency).

| Direction | Type |
| --- | --- |
| Request | [`EventListParams`](./event.go) |

```go
err := client.Events.List(context.Background(), "CDBUGS", sdk.EventListParams{
	Limit: sdk.F[int64](10),
})
if err != nil {
	panic(err)
}
```

### Delete event

Delete an event by its name. If more than one event row shares the name, they are treated as one event and every matching row is deleted.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

**Returns:** Returns a deleted object on success. Otherwise, this call returns an error.

| Direction | Type |
| --- | --- |
| Request | [`EventDeleteByNameParams`](./event.go) |

```go
err := client.Events.DeleteByName(context.Background(), "CDBUGS", sdk.EventDeleteByNameParams{
	Name: sdk.F[string]("Purchase early access"),
})
if err != nil {
	panic(err)
}
```

### Create event

Previously created an event. This endpoint is no longer available.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

| Direction | Type |
| --- | --- |
| Request | [`EventNewParams`](./event.go) |

```go
err := client.Events.New(context.Background(), "CDBUGS", sdk.EventNewParams{
	Name: sdk.F[string]("Purchase early access"),
})
if err != nil {
	panic(err)
}
```

### Set event currency

Set the currency of an event by its name. Use this instead of updating an event by its goal code. If more than one event row shares the name, they are treated as one event and every matching row is updated.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

**Returns:** Returns an updated object on success. Otherwise, this call returns an error.

| Direction | Type |
| --- | --- |
| Request | [`EventSetCurrencyParams`](./event.go) |

```go
err := client.Events.SetCurrency(context.Background(), "CDBUGS", sdk.EventSetCurrencyParams{
	Name: sdk.F[string]("Purchase early access"),
})
if err != nil {
	panic(err)
}
```

### Get event

Previously returned a single event by its goal code. This endpoint is no longer available.

**Permissions:** Requires read access to the site (`all-sites-readonly`, `read:{site_id}` or `manage:{site_id}`).

```go
err := client.Events.Get(context.Background(), "CDBUGS", "ABCDEFGH")
if err != nil {
	panic(err)
}
```

### Update event

Previously updated an event by its goal code. This endpoint is no longer available.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

| Direction | Type |
| --- | --- |
| Request | [`EventUpdateParams`](./event.go) |

```go
err := client.Events.Update(context.Background(), "CDBUGS", "ABCDEFGH", sdk.EventUpdateParams{})
if err != nil {
	panic(err)
}
```

### Delete event

Previously deleted an event by its goal code. This endpoint is no longer available.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

```go
err := client.Events.Delete(context.Background(), "CDBUGS", "ABCDEFGH")
if err != nil {
	panic(err)
}
```

### Wipe event

Previously wiped all completion data belonging to an event. This endpoint is no longer available.

```go
err := client.Events.Wipe(context.Background(), "CDBUGS", "ABCDEFGH")
if err != nil {
	panic(err)
}
```

## `Milestones`

Annotate your reports with important dates.

### List milestones

Return a list of all milestones this site owns. Milestones are sorted by `created_at` ascending to allow you to paginate with ease.

**Permissions:** Requires read access to the site (`all-sites-readonly`, `read:{site_id}` or `manage:{site_id}`).

**Returns:** A list of milestone objects.

| Direction | Type |
| --- | --- |
| Request | [`MilestoneListParams`](./milestone.go) |

```go
err := client.Milestones.List(context.Background(), "CDBUGS", sdk.MilestoneListParams{
	Limit: sdk.F[int64](10),
})
if err != nil {
	panic(err)
}
```

### Create milestone

Create a milestone. Returns HTTP `201 Created` on success.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

**Returns:** A milestone object.

| Direction | Type |
| --- | --- |
| Request | [`MilestoneNewParams`](./milestone.go) |

```go
err := client.Milestones.New(context.Background(), "CDBUGS", sdk.MilestoneNewParams{
	MilestoneDate: sdk.F[string]("2024-01-15"),
	Name:          sdk.F[string]("Website Redesign Launch"),
})
if err != nil {
	panic(err)
}
```

### Get milestone

Return a single milestone.

**Permissions:** Requires read access to the site (`all-sites-readonly`, `read:{site_id}` or `manage:{site_id}`).

**Returns:** A milestone object.

```go
err := client.Milestones.Get(context.Background(), "CDBUGS", "ddc9cdff-ab83-41fa-96c6-dfb276a862e7")
if err != nil {
	panic(err)
}
```

### Update milestone

Update a milestone. Both `name` and `milestone_date` are required.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

**Returns:** A milestone object.

| Direction | Type |
| --- | --- |
| Request | [`MilestoneUpdateParams`](./milestone.go) |

```go
err := client.Milestones.Update(context.Background(), "CDBUGS", "ddc9cdff-ab83-41fa-96c6-dfb276a862e7", sdk.MilestoneUpdateParams{
	MilestoneDate: sdk.F[string]("2024-01-20"),
	Name:          sdk.F[string]("Website Redesign Launch v2"),
})
if err != nil {
	panic(err)
}
```

### Delete milestone

Delete a milestone. Careful: you can't undo this, and neither can we.

**Permissions:** Requires write access to the site (`manage:{site_id}`).

**Returns:** Returns a deleted object on success. Otherwise, this call returns an error.

```go
err := client.Milestones.Delete(context.Background(), "CDBUGS", "ddc9cdff-ab83-41fa-96c6-dfb276a862e7")
if err != nil {
	panic(err)
}
```

## `Reports`

Custom reports across your traffic, events and sources.

### Aggregation

Build a custom report. Group and filter on the fields you care about.

**Permissions:** Requires read access to the relevant site (`all-sites-readonly`, `read:{site_id}` or `manage:{site_id}`).

**Returns:** Returns an array of objects. The properties of each object vary based on the aggregates and groupings you've asked for. All numeric values are returned as strings.

> This API endpoint is only accurate on data from March 2021 onwards. Before then, we did not tie browser, country, pathname, etc. together, so we have no way to offer this advanced filtering on that data.

> Grouped reports (`field_grouping`) default to 500 rows unless you set `limit`. The maximum is 1000, and this endpoint has no pagination. BI tools such as Looker Studio / Data Studio that fire many aggregations at once will hit the [concurrency cap](/api/v1/rate-limits), not the row cap. Space those requests, cache one extract per period, or raise the API plan.

> **Goal codes are no longer available:** Reporting on an event by its goal code (`entity_id` with `entity=event`) is no longer available. Report on events using `site_id` and `entity_name` instead. Pageview reporting, where `entity_id` is the site `id`, is unaffected.

#### Filtering

Filters are supplied as a JSON array. Each filter is an object with a `property`, an `operator` and a string `value`. You can add as many filters as you like; see the examples in the code panel.

We support the following operators:

- `is`: exact match
- `is not`: everything except an exact match
- `is like`: contains the term (supports wildcards `*`)
- `is not like`: does not contain the term
- `matching`: matches a regular expression (regex) pattern
- `not matching`: does not match a regex pattern

**Operator availability depends on the field.** Text-style fields support all six operators; categorical fields support only `is` and `is not`:

- **All six operators:** `domain`, `hostname`, `pathname`, `entry_page`, `exit_page`, `referrer_hostname`, `referrer_pathname`, `referrer_source`, `ref`, `utm_campaign`, `utm_source`, `utm_medium`, `utm_content`, `utm_term`
- **`is` / `is not` only:** `device_type`, `operating_system`, `browser`, `country_code`, `city`, `state`, `region`

Note: `domain` can be filtered on but not grouped by, while `keyword` can be grouped by but not filtered on.

##### Entry and exit pages

`entry_page` is the pathname of the first pageview in a visit. `exit_page` is the pathname of the last pageview before the visitor leaves. Both are session-level fields. They mirror the Entry Pages and Exit Pages reports on your dashboard and work for both `field_grouping` and `filters`.

When you filter by `entry_page`, only visits that *entered* on that page are included. A visitor who lands on `/home` and later views `/pricing` is excluded by `{"property": "entry_page", "operator": "is", "value": "/pricing"}`, but included when filtering on `pathname` instead.

##### Regex examples

With `matching` / `not matching` you can build sophisticated filters:

- `^/(about|contact|pricing)$`: match only /about, /contact and /pricing
- `^/(about|contact|pricing)`: match paths starting with those
- `^/blog/\d{4}/\d{2}/`: match blog URLs like /blog/2025/07/my-post
- `^/products/[^/]+/$`: match product category pages

| Direction | Type |
| --- | --- |
| Request | [`ReportAggregationParams`](./report.go) |

```go
err := client.Reports.Aggregation(context.Background(), sdk.ReportAggregationParams{
	Aggregates: sdk.F[string]("pageviews"),
	Entity:     sdk.F[sdk.ReportAggregationParamsEntity](sdk.ReportAggregationParamsEntity("pageview")),
})
if err != nil {
	panic(err)
}
```

### Current visitors

Returns the total number of current visitors on a site. The detailed view also returns the top 150 pages and top 150 referrers.

**Permissions:** Requires read access to the site (`all-sites-readonly`, `read:{site_id}` or `manage:{site_id}`).

**Returns:** The current visitor count, with an optional detailed breakdown.

| Direction | Type |
| --- | --- |
| Request | [`ReportCurrentVisitorsParams`](./report.go) |

```go
err := client.Reports.CurrentVisitors(context.Background(), sdk.ReportCurrentVisitorsParams{
	SiteID:   sdk.F[string]("CDBUGS"),
	Detailed: sdk.F[bool](false),
})
if err != nil {
	panic(err)
}
```
