# Evaluation test set

Fill this in after every refresh, before you publish. The agent is the system under test.
You are the grader. Nothing here is automated on purpose: the point is to decide, with a
source you trust, whether the agent told you the truth.

**Ground truth sources.** Pick one and use the same one every time, so your scores are
comparable week to week:

- Prices: Google Finance (google.com/finance), Yahoo Finance, or your brokerage app.
- News: the article itself. Open the link. Check the date on the page.
- Calendar: the company's investor relations page (search "TICKER investor relations
  events"), or the Fed's own meeting calendar for FOMC dates.

Refresh date: 2026-10-28    Grader: number-checker agent

## A. Prices (one row per ticker)

Pass if the dashboard's last price is within $0.10 of the ground truth for the same day
and the same price type (close vs intraday). If the agent recorded an intraday price,
compare against the intraday price at about the same time; do not compare it to a close.

**Run 2026-10-28 (number-checker agent).** Google Finance would not load ("not supported on this device"), so Yahoo Finance was used for all rows. All five-day closes (Oct 21-27) matched exactly for every ticker. The Yahoo intraday quotes were captured at 10:36-10:37 AM EDT, 7-10 minutes after the dashboard's times, so the latest-price comparison is approximate; rows are failed strictly by the $0.10 rule.

| Ticker | Date shown | Price type shown | Dashboard price | Ground truth price | Source you used | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA | Oct 21-27 closes; latest Oct 28 10:29 AM | close; intraday | closes OK; latest 218.29 | closes match; intraday 218.08 (10:36 AM) | Yahoo https://finance.yahoo.com/quote/NVDA/history/ | Fail (latest off by 0.21) |
| JPM | Oct 21-27 closes; latest Oct 28 10:28 AM | close; intraday | closes OK; latest 337.30 | closes match; intraday 337.38 (10:37 AM) | Yahoo https://finance.yahoo.com/quote/JPM/history/ | Pass |
| ACN | Oct 21-27 closes; latest Oct 28 10:27 AM | close; intraday | closes OK; latest 244.60 | closes match; intraday 244.62 (10:37 AM) | Yahoo https://finance.yahoo.com/quote/ACN/history/ | Pass |
| HSY | Oct 21-27 closes; latest Oct 28 10:26 AM | close; intraday | closes OK; latest 163.56 | closes match; intraday 163.66 (10:36 AM) | Yahoo https://finance.yahoo.com/quote/HSY/history/ | Pass (diff exactly 0.10) |
| LLY | Oct 21-27 closes; latest Oct 28 10:27 AM | close; intraday | closes OK; latest 1290.82 | closes match; intraday 1291.21 (10:37 AM) | Yahoo https://finance.yahoo.com/quote/LLY/history/ | Fail (latest off by 0.39) |

Disagreements: NVDA latest 218.29 vs 218.08; LLY latest 1290.82 vs 1291.21. Both are intraday prices at different minutes, so this may be timing rather than agent error, but it cannot be confirmed as a Pass.

## B. Weekly change (one row per ticker)

The page computes this from the five closes. Pass if it is within 0.3 percentage points of
your own calculation: (last price minus the close five trading days earlier) divided by
that earlier close.

Run 2026-10-28. The page appends `latest` to the closes (index.html line 343), so the week is Oct 21 close to the latest price. The dashboard percentage was derived from the BRIEF numbers (the page was not rendered); ground-truth closes equal BRIEF closes. Using Yahoo's intraday numbers instead would shift results by under 0.1 points.

| Ticker | Dashboard weekly change | Your calculation | Source | Pass / Fail |
|---|---|---|---|---|
| NVDA | -1.66% | (218.29-221.97)/221.97 = -1.66% | https://finance.yahoo.com/quote/NVDA/history/ | Pass |
| JPM | +0.63% | (337.30-335.19)/335.19 = +0.63% | https://finance.yahoo.com/quote/JPM/history/ | Pass |
| ACN | +1.81% | (244.60-240.26)/240.26 = +1.81% | https://finance.yahoo.com/quote/ACN/history/ | Pass |
| HSY | -2.53% | (163.56-167.81)/167.81 = -2.53% | https://finance.yahoo.com/quote/HSY/history/ | Pass |
| LLY | +1.58% | (1290.82-1270.75)/1270.75 = +1.58% | https://finance.yahoo.com/quote/LLY/history/ | Pass |

## C. News grounding (one row per news item, three per ticker)

Open every link. Pass only if all four are true: the link opens, the headline matches the
article, the article date is within the last seven days, and the "why it matters" line is
supported by the article (not just plausible).

Run 2026-10-28 (window Oct 21-28). JPM and HSY have no news items (none to grade; noted as missing in the changelog).

| Ticker | Headline (first few words) | Link opens | Headline matches | Dated within 7 days | "Why" is supported | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA | Nvidia to invest $3B in Meta's... ([source](https://stockanalysis.com/news/nvidia-3-billion-meta-offering/)) | Yes | Yes | Yes, Oct 27 | Yes (customer stock sale, work on Meta AI models) | Pass |
| NVDA | Nvidia backs Indian startup... ([source](https://techcrunch.com/2026/10/27/nvidia-backs-indian-startup-helping-companies-run-ai-models-with-fewer-gpus/)) | Yes | Yes | Yes, Oct 27 | Mostly; article says "in some cases, with fewer GPUs" and a $14M round (small deal). Does not discuss chip-demand investor trend, which is the agent's inference | Pass (weak support on the investor-trend clause) |
| ACN | Accenture awarded $151.46M... ([source](https://www.tipranks.com/news/the-fly/accenture-awarded-151-46m-air-force-task-order-thefly-news)) | Yes | Yes | Yes, Oct 27 | Partly; completion by June 30, 2030 is stated, but "multi-year" and "steady federal work" are the agent's wording | Pass |
| ACN | Accenture price target raised to $252... ([source](https://www.tipranks.com/news/the-fly/accenture-price-target-raised-to-252-from-210-at-wells-fargo-thefly-news)) | Yes | Yes | Yes, Oct 26 | Yes (Equal Weight kept; size may complicate AI transition; hard to show "AI winner" until organic revenue accelerates) | Pass |
| ACN | Accenture downgraded to Neutral... ([source](https://www.tipranks.com/news/the-fly/accenture-downgraded-to-neutral-from-outperform-at-baird-thefly-news)) | Yes | Yes | Yes, Oct 21 (exactly 7 days before asOf) | Yes (shares "over doubled since June"; valuation "reasonable", paraphrased as "fairly valued") | Pass |
| LLY | Lilly's Retatrutide Beats Ozempic 2 mg... ([source](https://www.prnewswire.com/news-releases/lillys-retatrutide-delivered-greater-a1c-reduction-and-more-than-double-the-weight-loss-of-ozempics-highest-approved-dose-in-head-to-head-trial-302921328.html)) | Yes | Mostly; the dashboard headline is a paraphrase, the release title is "Lilly's retatrutide delivered greater A1C reduction and more than double the weight loss of Ozempic's highest approved dose in head-to-head trial" | Yes, Oct 28 | Yes (dropouts 4.8%/7.9% vs 2.7%; not approved anywhere) | Pass (headline is reworded, not quoted) |
| LLY | Patrik Jonsson to retire... ([source](https://www.prnewswire.com/news-releases/patrik-jonsson-to-retire-after-36-years-ilya-yuffa-named-president-lilly-global-markets-302921191.html)) | Yes | Yes | Yes, Oct 27 | Yes (US and international combine as Lilly Global Markets, effective Jan 1, 2027) | Pass |
| LLY | Lilly declares fourth-quarter 2026 dividend ([source](https://www.prnewswire.com/news-releases/lilly-declares-fourth-quarter-2026-dividend-302919496.html)) | Yes | Yes | Yes, Oct 26 | Yes ($1.73, payable Dec 10, record Nov 13) | Pass |

(Add rows as needed. Fifteen items is normal for five tickers.)

## D. Calendar (one row per event)

Pass if the event and its date appear on the source linked, and the date is in the future
relative to the refresh date.

Run 2026-10-28. Company IR pages were not used; the press release copies the agent cited (Finviz) were opened instead and state the dates as announced by the companies.

| Ticker | Event | Date shown | Confirmed on source | Source | Pass / Fail |
|---|---|---|---|---|---|
| JPM (market-wide) | FOMC meeting concludes, rate decision | 2026-10-28 | Yes, Oct 27-28 is listed, no asterisk or tentative note for 2026 | https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm | Fail (date equals asOf 2026-10-28, not after it; event is confirmed) |
| HSY | Q3 2026 results, 8:30 AM ET call | 2026-10-29 | Yes, announced Oct 9, 2026 as confirmed | https://finviz.com/news/400861/hershey-to-webcast-third-quarter-conference-call | Pass |
| LLY | Q3 2026 results, 10:00 AM ET call | 2026-10-29 | Yes, press release Oct 15, 2026 | https://finviz.com/news/403165/lilly-confirms-date-and-conference-call-for-third-quarter-2026-financial-results-announcement | Pass |

## E. Omissions

Did the agent miss something a human analyst would have included? List anything you found
in the ground-truth sources that the brief does not mention (a big headline, an earnings
date inside the window). Each one counts as a Fail in the score below.

- 
- 

## F. Your own test cases

Write two tests of your own that none of the sections above cover. Examples students have
used: "the as-of date on the page matches the changelog date", "no ticker shows a flat
sparkline when the real stock moved", "the page still renders if I delete one ticker's
news list in the data". Write the test, run it, record the result.

| Test | How you checked | Pass / Fail |
|---|---|---|
| | | |
| | | |

## Score

| Section | Passes | Total | Rate |
|---|---|---|---|
| A. Prices | | | |
| B. Weekly change | | | |
| C. News | | | |
| D. Calendar | | | |
| E. Omissions (fails only) | 0 | | |
| F. Your own | | | |
| **Overall** | | | |

Publish only if A and B are 100% and C is at least 80% after fixes. If a row failed, tell
Claude exactly which row and what the correct value is, let it fix only that, rerun the
`qa-reviewer` agent, and regrade that row. Keep the original Fail in this file: a record of
what the agent got wrong is more useful than a clean sheet.
