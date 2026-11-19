<p align="center"><img src="assets/querya-icon.png" width="96" alt="Querya"></p>

# Querya: Google Ads MCP server

Querya is a hosted MCP server that connects AI agents to your Google Ads accounts: Claude, ChatGPT, Cursor, VS Code, Gemini CLI, Cline and any client that speaks MCP over Streamable HTTP.

Ask in plain language and your agent answers with live data: performance by campaign, ad group, keyword or search term; wasted spend and negative keyword candidates; Quality Score; impression share; budget pacing; conversion actions; change history; policy issues and Google Ads Recommendations. Keyword research uses Google Keyword Planner: ideas, metrics and forecasts by location and language.

**Changes are safe by design.** Creating campaigns, ads, keywords, negatives, assets and audiences, or editing budgets, bids, targeting and status, never applies directly. Querya first returns a preview validated by Google Ads, with the risk level and the daily spend impact. Nothing changes until you confirm. New campaigns are always created paused. Per-account guardrails cap budgets and bids and block removals by default. Every change is logged and can be undone.

- **Server URL:** `https://mcp.querya.io/mcp` (Streamable HTTP)
- **Auth:** OAuth 2.1 with PKCE. Dynamic client registration and Client ID Metadata Documents are supported, so there is no key to paste. You sign in with Google and choose exactly which Google Ads accounts the agent can reach.
- **Plans:** Free: 1 account, read-only. Pro and Max: more accounts, manager (MCC) accounts and changes. See [querya.io](https://querya.io).

This repository holds the public listing files (MCP Registry `server.json`, Gemini CLI extension). The server itself is hosted by Querya; there is nothing to install or run.

## Connect

### Claude (claude.ai and Claude Desktop)

Settings → Connectors → **Add custom connector** → URL `https://mcp.querya.io/mcp`.

### Claude Code

```bash
claude mcp add --transport http querya https://mcp.querya.io/mcp
```

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "querya": { "url": "https://mcp.querya.io/mcp" }
  }
}
```

### VS Code (GitHub Copilot)

`.vscode/mcp.json`:

```json
{
  "servers": {
    "querya": { "type": "http", "url": "https://mcp.querya.io/mcp" }
  }
}
```

### Gemini CLI

```bash
gemini extensions install https://github.com/extractify/querya-mcp
```

### Cline, Windsurf and other clients

Add a remote MCP server with the URL `https://mcp.querya.io/mcp`. The client opens the Querya sign-in in your browser the first time.

### ChatGPT

Settings → Apps & Connectors → developer mode → **Create** → URL `https://mcp.querya.io/mcp`, authentication OAuth.

## Example prompts

- "How did my campaigns do last week compared with the week before?"
- "Which search terms spent money without converting in the last 30 days? Prepare negatives for the worst ones."
- "Find keyword ideas for 'curso de inglês online' in Brazil and forecast clicks and cost for R$50/day."
- "Raise the budget of the Brand campaign to R$80/day." (shows a preview; nothing changes until you confirm)
- "Who changed what in this account this week?"

## Tools (59)

Read tools have `readOnlyHint`. Write tools only prepare a validated plan; `confirm_change_plan` (`destructiveHint`) is the one that changes the account, after your explicit confirmation.

| Tool | What it does |
|---|---|
| `list_accounts` | List the Google Ads accounts the user has enabled for Querya, with currency, time zone, whether each one is a manager (MCC) account, and whether write tools are allowed on it. |
| `get_account_hierarchy` | Show the manager (MCC) tree: every Google Ads account Querya found for the user, its parent manager, and whether it is enabled in Querya. |
| `get_account_overview` | Quick health check of one account: status, spend, clicks, conversions, CPA and ROAS for the period, number of active campaigns, the top 5 campaigns by cost, and counts of disapproved or limited ads. |
| `set_default_account` | Choose the account used when other tools are called without customer_id. |
| `refresh_accounts` | Re-read the accounts the user's Google login can access (including MCC sub-accounts). |
| `run_gaql` | Run a custom read-only Google Ads Query Language (GAQL) query when no ready-made report fits. |
| `describe_gaql_resource` | List the selectable fields (with type, filterable/sortable and enum values), the compatible metrics and segments, and the attributed resources of a GAQL resource, straight from Google Ads metadata. |
| `get_performance_report` | Ready-made performance report by level (account, campaign, ad_group, ad, keyword, bidding_strategy) for a period: impressions, clicks, cost, conversions, value, CTR, avg CPC, CPA and ROAS, optionally split by date or device, with account totals and, if compare=true, change vs the previous period of the same length. |
| `get_search_terms` | Search terms that triggered ads, with matched keyword, cost and conversions, sorted by cost. |
| `get_asset_performance` | Performance of ad assets in the period. |
| `get_keyword_quality` | Quality Score (1–10) and its components — expected CTR, ad relevance, landing page experience — for enabled keywords with traffic in the period, sorted by cost, with the impression-weighted average. |
| `get_impression_share` | Search impression share per Search campaign and the share lost to budget and to rank (ad rank / Quality Score), plus top and absolute-top shares, in percent. |
| `get_budget_pacing` | Month-to-date spend of each enabled campaign against its budget (daily budget × days in month), with a linear projection to month end. |
| `get_change_history` | Changes made to the account, newest first: when, what resource, operation, which tool (web, API, scripts, Querya…) and who. |
| `get_change_timeline` | A readable timeline of what changed in the account and who did it, grouped by day: plain-language sentences ("raised the budget of "Brand" from $50 to $80/day"), current campaign and ad group names, similar changes grouped together, and the change_id of changes made through Querya (for undo_change). |
| `get_policy_issues` | Ads that are disapproved or limited by Google policy, with policy topics and the evidence text. |
| `get_conversion_actions` | Conversion actions of the account (status, type, category, counting, primary or secondary) with all conversions and value in the period. |
| `get_recommendations` | Google Ads Recommendations: Google's active (not dismissed) recommendations with estimated weekly impact on impressions, clicks, cost and conversions, sorted by conversion gain. |
| `generate_keyword_ideas` | Keyword ideas from Google Keyword Planner for seed keywords, a landing page URL, both, or a whole site: average monthly searches, last-month volume, 3-month and year-over-year trend, competition and the top-of-page bid range in the account currency. |
| `get_keyword_metrics` | Historical Keyword Planner metrics for a list of keywords you already have (up to 1000): average monthly searches, trend, competition, top-of-page bids and average CPC; close variants are grouped. |
| `forecast_keywords` | Forecast clicks, cost, average CPC and (when available) conversions and CPA for a NEW Search campaign with the given keywords, daily budget, locations and language, starting tomorrow for 7–90 days. |
| `search_geo_targets` | Find Google Ads location IDs by name (country, state, city, postal code), with canonical name, type and reach. |
| `search_languages` | List Google Ads language IDs and codes, optionally filtered by name or code. |
| `create_search_campaign` | Create a complete Google Search campaign in one step: budget, campaign (ALWAYS created PAUSED), locations, languages, bidding strategy, ad groups with keywords and responsive search ads, and optional campaign negative keywords. |
| `create_ad_group` | Create an ad group in an existing Search campaign, optionally with keywords and responsive search ads. |
| `create_responsive_search_ad` | Create a responsive search ad (3–15 headlines up to 30 characters, 2–4 descriptions up to 90, optional pins and paths). |
| `add_keywords` | Add keywords to an ad group, with match type and optional CPC/final URL. |
| `add_negative_keywords` | Add negative keywords to a campaign, an ad group or a shared negative list (level + target_id). |
| `create_negative_keyword_list` | Create a shared negative keyword list and attach it to campaigns. |
| `set_status` | Enable, pause or remove campaigns, ad groups, ads or keywords (batch). |
| `update_budget` | Change a campaign's daily budget. |
| `update_bidding_strategy` | Switch a campaign bidding strategy (MANUAL_CPC, MAXIMIZE_CLICKS, MAXIMIZE_CONVERSIONS, TARGET_CPA, MAXIMIZE_CONVERSION_VALUE, TARGET_ROAS) and targets, or attach the campaign to an existing portfolio strategy with portfolio_id (see create_bidding_strategy). |
| `update_bids` | Change max CPC bids of ad groups or keywords (Manual CPC). |
| `update_targeting` | Change campaign targeting: add/remove locations and languages (names or IDs), replace the ad schedule, set device bid adjustments (% from -100 to +900). |
| `update_ad` | Edit a responsive search ad: replace headlines and/or descriptions, paths or the final URL. |
| `update_keywords` | Change keyword match type (creates the new keyword and pauses the old one, since match type cannot be edited) or final URL. |
| `apply_recommendation` | Apply Google Ads Recommendations by recommendation_id (from get_recommendations). |
| `dismiss_recommendation` | Dismiss Google Ads Recommendations by recommendation_id (from get_recommendations). |
| `create_performance_max_campaign` | Create a Performance Max campaign in one step: budget, campaign (ALWAYS created PAUSED), locations, languages, bidding (MAXIMIZE_CONVERSIONS / TARGET_CPA / MAXIMIZE_CONVERSION_VALUE / TARGET_ROAS) and one asset group with texts, images, logo, final URL and optional search themes as audience signal. |
| `create_assets` | Create ad assets (extensions) and link them to the account, a campaign or an ad group: sitelinks, callouts, a structured snippet, a call asset, images and business logos (public https URLs, checked by Querya). |
| `create_conversion_action` | Create a conversion action. |
| `upload_offline_conversions` | Import conversions (e.g. |
| `create_bidding_strategy` | Create a portfolio bidding strategy shared by several campaigns: TARGET_CPA, TARGET_ROAS, MAXIMIZE_CONVERSIONS (optional target CPA), MAXIMIZE_CONVERSION_VALUE (optional target ROAS) or MAXIMIZE_CLICKS (optional CPC ceiling). |
| `bulk_mutate` | Advanced: apply a batch of raw Google Ads API mutate operations (GoogleAdsService.Mutate, REST JSON in camelCase, see https://developers.google.com/google-ads/api/docs/mutating/overview) when no specific tool fits, e.g. |
| `create_audience` | Create an audience. |
| `get_audience_status` | Size for Search/Display, match rate and upload job status of an audience list (e.g. |
| `create_rule` | Create a rule that Querya runs on a schedule (daily at an hour of the account time zone, or hourly). |
| `list_rules` | List scheduled rules with their last runs (matches, plan created, applied or failed). |
| `delete_rule` | Delete a scheduled rule (pending plans it created stay until they expire). |
| `create_alert` | Create an email alert for an account (all plans). |
| `list_alerts` | List email alerts with when they were last checked and triggered. |
| `delete_alert` | Delete an email alert. |
| `confirm_change_plan` | Apply a pending change plan AFTER the user explicitly confirmed the preview. |
| `cancel_change_plan` | Discard a pending change plan. |
| `list_change_plans` | List pending (not yet confirmed) change plans of this user. |
| `list_change_log` | Changes applied through Querya (newest first) with before/after, whether they can be undone, and the Google request id. |
| `undo_change` | Create a change plan that reverts a change made with Querya (change_id from list_change_log). |
| `get_guardrails` | Show the safety limits of an account: max daily budget (null = 2× the largest current budget), max budget increase per change, max CPC, auto-apply of low-risk changes, removal allowed, allowed/blocked tools. |
| `set_guardrails` | Change the safety limits of an account (owners and admins only). |

## Security and privacy

- Google refresh tokens are envelope-encrypted; Querya never shows them to the agent or to anyone else.
- Each tool call checks that the account is one you enabled for Querya.
- Content from the account (campaign names, ad texts, search terms) is returned as data, never as instructions.
- Querya does not store ad metrics, except a short-lived cache and, for enabled accounts, a copy of the Google Ads change history.

[Privacy policy](https://querya.io/privacy/) · [Terms of service](https://querya.io/terms/) · [Docs](https://querya.io/docs/)

## Support

suporte@extractify.com.br

Querya is a product of Extractify Tecnologia Ltda. Google Ads is a trademark of Google LLC; Querya is not affiliated with or endorsed by Google.

## License

The files in this repository are MIT licensed. The Querya service is proprietary.
