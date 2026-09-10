---\ntitle: Power BI Previous Quarter Calculation Not Working? A Fix for Quarterly Survey Data\ndescription:Fix incorrect previous-quarter calculations in Power BI for quarterly survey data using DAX, Quarter Start, filter context and REMOVEFILTERS.\npubDate: 2026-09-10\nlabel: Power BI\n---\n\n# <p>If your Power BI survey only runs once every three months, a standard previous-quarter calculation can sometimes return the current-quarter value instead of the prior-quarter value.</p><p>The issue is usually not the count measure itself. It is the way the date filter context is being shifted.</p><h2>Why DATEADD can fail</h2><p>For quarterly survey data, the fact table may only contain dates for one survey period per quarter. A calculation such as:</p><pre><code>Previous Quarter Question Response Count =
CALCULATE(
    [Question Response Count],
    DATEADD(fx_date[Date], -1, QUARTER)
)</code></pre><p>can behave unexpectedly when the visible date context does not contain a continuous set of daily dates that maps cleanly to the previous quarter.</p><h2>A more reliable approach</h2><p>If your date table already contains a Quarter Start column, you can explicitly identify the current quarter and then move back three months.</p><pre><code>Previous Quarter Question Response Count =
VAR CurrentQuarterStart =
    MAX(fx_date[Quarter Start])
VAR PreviousQuarterStart =
    EDATE(CurrentQuarterStart, -3)
RETURN
    CALCULATE(
        [Question Response Count],
        REMOVEFILTERS(fx_date),
        fx_date[Quarter Start] = PreviousQuarterStart
    )</code></pre><h2>Why this works</h2><p>The measure first captures the current quarter start, calculates the previous quarter start, removes the existing date filter, and then applies the exact previous-quarter filter.</p><p>This pattern is especially useful for quarterly surveys, audits, reviews, inspections, or other business processes where activity only occurs at specific points in time.</p><h2>When to use this pattern</h2><p>Use this approach when your business process is quarter-based, your fact table has sparse dates, or DATEADD returns the same value for both current and previous quarter.</p><p>The key idea is simple: when the business process is periodic rather than continuous, explicit period boundaries are often more reliable than shifting the current date context.</p>\