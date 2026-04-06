# Travel Cost Analysis

Generate travel expense analytics, charts, and cost comparison visualizations from Ramp data. Use this section whenever Bryce asks to visualize travel spending, compare LBB vs DFW costs, create charts of trip volume or expense categories, build a business case for relocation, analyze flight/hotel/car rental expenses, or produce any data visualization related to work travel patterns. Also trigger when Bryce mentions travel costs, Ramp expenses, trip frequency, parish travel analytics, relocation savings, or wants to present travel data to Josh or leadership. Even if Bryce just says "update my travel charts" or "pull my latest travel data", use this section.

---

## Overview

This section produces polished, presentation-ready travel analytics from Bryce's Ramp expense data. It covers three core chart types plus a geographic travel map, all designed to support the business case for relocating from Lubbock (LBB) to Dallas-Fort Worth (DFW).

### When to use

- Bryce asks for travel charts, dashboards, or visualizations
- Bryce wants to update his relocation business case with fresh data
- Bryce needs to present travel cost data to Josh or leadership
- Bryce asks about travel spend trends, trip frequency, or expense breakdowns
- Bryce wants to compare LBB vs DFW costs for flights, hotels, or car rentals

---

## Workflow

### Step 1: Pull fresh data from Ramp

Always pull live data — never use hardcoded values. Use these three Ramp MCP tools:

1. **`ramp_get_bookings`** — Flights and hotel bookings (set `include_flights: true`, `include_hotels: true`, `limit: 50`)
2. **`ramp_get_user_trips`** — All trips with destinations, dates, and status (set `page_size: 50`)
3. **`ramp_get_reimbursements`** — Out-of-pocket expenses including mileage, car rentals, meals, hotels paid personally (set `page_size: 50`)

### Step 2: Classify expenses into categories

Parse reimbursements and bookings into these four categories based on the GL account codes and merchant names:

| Category | Identification |
|---|---|
| **Flights** | Merchant = airline name, or GL code `62202 - Travel - Transportation`, or `62301` with airline merchant |
| **Lodging** | GL code `62303 - Travel - Partner Support Lodging`, or merchant contains "hotel", "inn", "hampton", "marriott", etc. |
| **Transportation** | GL code `62301 - Travel - Partner Support Transportation`, mileage reimbursements (memo mentions "mile trip"), car rental merchants |
| **Meals** | GL code `62304 - Travel - Partner Support Meals`, or food/restaurant merchants during travel |

When a reimbursement is a bundled "hotel and flight package" (e.g. Capital One Travel), split it proportionally: estimate 60% lodging / 40% flights unless the memo gives more detail.

### Step 3: Build the charts

Use the chart templates in the sections below. The four core visualizations are:

1. **Travel spend by category** — Doughnut chart breaking down flights, lodging, transportation, meals
2. **Monthly trip volume** — Vertical bar chart showing trips per month
3. **LBB vs DFW cost comparison** — Horizontal grouped bar chart comparing estimated round-trip costs per destination
4. **Travel footprint map** — D3 US map showing trip routes from LBB with DFW as the proposed alternative hub

### Step 4: Present with context

Always include:

- **Summary metric cards** at the top: total trips, total reimbursements, avg trip frequency, unique destinations
- **Date range** of the data being shown
- **Narrative framing** around the charts explaining what the data means for the relocation case

---

## Key context

- Bryce lives in Plains/Lubbock, TX. Home airport is LBB (Lubbock Preston Smith International)
- LBB is a small regional airport — nearly all flights connect through DFW, DAL, or another hub
- The proposed relocation is to the DFW metro area (Grapevine, Las Colinas, Coppell, Irving)
- DFW Airport is a major American Airlines hub with direct flights to most destinations
- Bryce's manager is Josh (head of parish partnerships)
- Bryce travels 2-4 times per month for parish events and missions across the US
- The mileage reimbursement rate is approximately $0.67/mile (IRS standard rate)

---

## Color palette for charts

Use these consistent colors across all travel charts:

| Category | Hex | Usage |
|---|---|---|
| Flights | `#3266ad` | Blue — air travel |
| Lodging | `#1D9E75` | Teal — hotels and stays |
| Transportation | `#D85A30` | Coral — car rentals, mileage, rideshares |
| Meals | `#888780` | Gray — food during travel |
| LBB (current) | `#E24B4A` | Red — current situation costs |
| DFW (proposed) | `#1D9E75` | Teal — proposed savings |
| Trip volume bars | `#534AB7` | Purple — monthly count |

---

## Chart rendering rules

- Use the `show_widget` visualizer tool for inline charts (not file creation)
- Always use Chart.js 4.4.1 via CDN: `https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js`
- For the map: use D3 7.8.5 + TopoJSON 3.0.2 via CDN with `us-atlas@3/states-10m.json`
- Support dark mode: detect with `matchMedia('(prefers-color-scheme: dark)').matches`
- Use CSS variables for text and backgrounds, hardcoded hex for chart data colors
- Metric cards use `var(--color-background-secondary)` background
- Savings cards use teal palette: `#E1F5EE` background, `#0F6E56` / `#085041` text
- All numbers must go through `Math.round()`, `.toFixed()`, or `toLocaleString()`
- Disable default Chart.js legends — build custom HTML legends with small colored squares
- Wrap canvas in a div with explicit height and `position: relative`

---

## Chart templates

### Summary metric cards

Always place these at the very top of the dashboard. Use a 4-column grid.

```html
<div style="display: grid; grid-template-columns: repeat(4, minmax(0, 1fr)); gap: 12px; margin-bottom: 2rem;">
  <div style="background: var(--color-background-secondary); border-radius: var(--border-radius-md); padding: 1rem;">
    <div style="font-size: 13px; color: var(--color-text-secondary);">Total trips ({date range})</div>
    <div style="font-size: 24px; font-weight: 500;">{count}</div>
  </div>
  <!-- Repeat for: Travel reimbursements ($total), Avg. trip frequency (X/mo), Unique destinations (count) -->
</div>
```

Compute values from the Ramp data:

- **Total trips**: Count of trips from `ramp_get_user_trips` within the date range
- **Travel reimbursements**: Sum of `amount` from travel-related reimbursements (exclude non-travel items like Hallow subscription)
- **Avg. trip frequency**: Total trips ÷ number of months in the date range, round to 1 decimal
- **Unique destinations**: Count of distinct destination cities from trip location data

---

### Travel spend by category (doughnut chart)

Doughnut chart showing proportional spend across flights, lodging, transportation, and meals.

**Custom legend** (place ABOVE the chart):

```html
<div style="font-size: 16px; font-weight: 500; margin-bottom: 8px;">Travel spend by category</div>
<div style="display: flex; flex-wrap: wrap; gap: 16px; margin-bottom: 8px; font-size: 12px; color: var(--color-text-secondary);">
  <span style="display: flex; align-items: center; gap: 4px;">
    <span style="width: 10px; height: 10px; border-radius: 2px; background: #3266ad;"></span>
    Flights ${amount}
  </span>
  <span style="display: flex; align-items: center; gap: 4px;">
    <span style="width: 10px; height: 10px; border-radius: 2px; background: #1D9E75;"></span>
    Lodging ${amount}
  </span>
  <span style="display: flex; align-items: center; gap: 4px;">
    <span style="width: 10px; height: 10px; border-radius: 2px; background: #D85A30;"></span>
    Transportation ${amount}
  </span>
  <span style="display: flex; align-items: center; gap: 4px;">
    <span style="width: 10px; height: 10px; border-radius: 2px; background: #888780;"></span>
    Meals ${amount}
  </span>
</div>
```

**Chart.js config:**

```javascript
new Chart(document.getElementById('spendChart'), {
  type: 'doughnut',
  data: {
    labels: ['Flights', 'Lodging', 'Transportation', 'Meals'],
    datasets: [{
      data: [flightsTotal, lodgingTotal, transportTotal, mealsTotal],
      backgroundColor: ['#3266ad', '#1D9E75', '#D85A30', '#888780'],
      borderWidth: 0
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    cutout: '60%',
    plugins: {
      legend: { display: false },
      tooltip: {
        callbacks: {
          label: ctx => ' $' + ctx.parsed.toLocaleString()
        }
      }
    }
  }
});
```

Canvas wrapper: `<div style="position: relative; width: 100%; height: 280px;"><canvas id="spendChart"></canvas></div>`

---

### Monthly trip volume (bar chart)

Vertical bar chart. One bar per month showing number of trips.

**Chart.js config:**

```javascript
const isDark = matchMedia('(prefers-color-scheme: dark)').matches;
const textColor = isDark ? 'rgba(255,255,255,0.7)' : 'rgba(0,0,0,0.5)';
const gridColor = isDark ? 'rgba(255,255,255,0.08)' : 'rgba(0,0,0,0.06)';

new Chart(document.getElementById('volumeChart'), {
  type: 'bar',
  data: {
    labels: monthLabels,  // e.g. ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun']
    datasets: [{
      label: 'Trips',
      data: tripCounts,   // e.g. [2, 3, 4, 2, 0, 1]
      backgroundColor: '#534AB7',
      borderRadius: 4,
      barThickness: 32
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    scales: {
      x: { grid: { display: false }, ticks: { color: textColor } },
      y: {
        beginAtZero: true,
        ticks: { color: textColor, stepSize: 1 },
        grid: { color: gridColor }
      }
    },
    plugins: { legend: { display: false } }
  }
});
```

Section heading: `<div style="margin-top: 2.5rem; font-size: 16px; font-weight: 500; margin-bottom: 8px;">Monthly trip volume ({year})</div>`

Canvas wrapper: `<div style="position: relative; width: 100%; height: 240px;"><canvas id="volumeChart"></canvas></div>`

---

### LBB vs DFW cost comparison (horizontal bar chart)

Horizontal grouped bar chart. Shows estimated round-trip flight cost from LBB (red) vs DFW (teal) for each destination actually visited.

**Legend** (above chart):

```html
<div style="display: flex; flex-wrap: wrap; gap: 16px; margin-bottom: 8px; font-size: 12px; color: var(--color-text-secondary);">
  <span style="display: flex; align-items: center; gap: 4px;">
    <span style="width: 10px; height: 10px; border-radius: 2px; background: #E24B4A;"></span>
    From LBB (connecting)
  </span>
  <span style="display: flex; align-items: center; gap: 4px;">
    <span style="width: 10px; height: 10px; border-radius: 2px; background: #1D9E75;"></span>
    From DFW (direct)
  </span>
</div>
```

**Chart.js config:**

```javascript
new Chart(document.getElementById('comparisonChart'), {
  type: 'bar',
  data: {
    labels: destinationLabels,  // e.g. ['Boston\n(BOS)', 'Newark\n(EWR)', ...]
    datasets: [
      {
        label: 'From LBB',
        data: lbbCosts,
        backgroundColor: '#E24B4A',
        borderRadius: 4,
        barThickness: 20
      },
      {
        label: 'From DFW',
        data: dfwCosts,
        backgroundColor: '#1D9E75',
        borderRadius: 4,
        barThickness: 20
      }
    ]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    indexAxis: 'y',
    scales: {
      x: {
        ticks: { color: textColor, callback: v => '$' + v },
        grid: { color: gridColor }
      },
      y: {
        ticks: { color: textColor, font: { size: 11 } },
        grid: { display: false }
      }
    },
    plugins: {
      legend: { display: false },
      tooltip: {
        callbacks: { label: ctx => ctx.dataset.label + ': $' + ctx.parsed.x }
      }
    }
  }
});
```

Include context paragraph above the chart:

```html
<p style="font-size: 13px; color: var(--color-text-secondary); margin: 0 0 12px;">
  LBB flights require connections through DFW or other hubs, adding $80–$200+ per round trip.
  DFW offers direct flights to nearly every destination visited.
</p>
```

Canvas wrapper height: 320px (or calculate as `(numDestinations * 50) + 80` px minimum).

---

### Travel footprint map (D3)

US map showing trip routes from Lubbock with DFW as proposed alternative.

**Required libraries:**

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/7.8.5/d3.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/topojson/3.0.2/topojson.min.js"></script>
```

**D3 config:**

```javascript
const projection = d3.geoAlbersUsa().scale(1100).translate([450, 280]);
const path = d3.geoPath(projection);
const svg = d3.select('#map').append('svg').attr('viewBox', '0 0 900 560').attr('width', '100%');

d3.json('https://cdn.jsdelivr.net/npm/us-atlas@3/states-10m.json').then(us => {
  // Draw state boundaries
  svg.selectAll('path.state')
    .data(topojson.feature(us, us.objects.states).features)
    .join('path')
    .attr('d', path)
    .attr('fill', isDark ? '#2C2C2A' : '#F1EFE8')
    .attr('stroke', isDark ? 'rgba(255,255,255,0.12)' : '#fff')
    .attr('stroke-width', 0.5);

  // Draw dashed route lines from LBB to each destination (red, opacity 0.35)
  // Draw solid line from LBB to DFW (teal, width 2.5, opacity 0.7)
  // Draw destination dots (coral, radius 4, white stroke)
  // Draw DFW dot (teal, radius 7, white stroke, with label)
  // Draw LBB dot (red, radius 7, white stroke, with label)
});
```

Key coordinates:

- Lubbock, TX: `lat: 33.58, lng: -101.85`
- DFW Airport: `lat: 32.90, lng: -97.04`
- Extract all other destination coordinates from `ramp_get_user_trips` location data

**Visual encoding:**

| Element | Color | Size | Style |
|---|---|---|---|
| LBB (home) | `#E24B4A` | radius 7 | Solid dot + "LBB (current)" label |
| DFW (proposed) | `#1D9E75` | radius 7 | Solid dot + "DFW (proposed)" label |
| Destination cities | `#D85A30` | radius 4 | Solid dot, white 1px stroke |
| Routes from LBB | `#E24B4A` | width 1 | Dashed `4,3`, opacity 0.35 |
| LBB→DFW line | `#1D9E75` | width 2.5 | Solid, opacity 0.7 |

---

### Projected annual savings cards

Green metric cards summarizing the financial case. Place after the comparison chart.

```html
<div style="display: grid; grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 12px; margin-bottom: 1.5rem;">
  <div style="background: #E1F5EE; border-radius: var(--border-radius-md); padding: 1rem;">
    <div style="font-size: 13px; color: #0F6E56;">Flight savings (est.)</div>
    <div style="font-size: 24px; font-weight: 500; color: #085041;">${amount}/yr</div>
  </div>
  <div style="background: #E1F5EE; border-radius: var(--border-radius-md); padding: 1rem;">
    <div style="font-size: 13px; color: #0F6E56;">Drive-to-airport time saved</div>
    <div style="font-size: 24px; font-weight: 500; color: #085041;">{hours}+ hrs/yr</div>
  </div>
  <div style="background: #E1F5EE; border-radius: var(--border-radius-md); padding: 1rem;">
    <div style="font-size: 13px; color: #0F6E56;">LBB→DFW mileage eliminated</div>
    <div style="font-size: 24px; font-weight: 500; color: #085041;">{miles} mi/yr</div>
  </div>
</div>
```

**Savings calculations:**

- **Flight savings**: `(avg LBB cost - avg DFW cost) × projected annual trips`. Use estimates from the flight cost estimates section below.
- **Time saved**: Each trip from LBB involves ~2-3 hours of connection time through DFW. Use `projected annual trips × 10` hours as a conservative estimate.
- **Mileage eliminated**: `300 miles × 2 (round trip to LBB airport from Plains) × projected annual trips`. Alternatively, calculate from actual mileage reimbursements.
- **Projected annual trips**: `(total_trips / months_in_data) × 12`

---

## Flight cost estimates: LBB vs DFW

Estimated round-trip economy flight costs from Lubbock (LBB) vs Dallas-Fort Worth (DFW) to common parish destinations. Approximate averages based on typical booking patterns (1-3 weeks advance, economy class, American Airlines preferred).

### Why LBB costs more

- **No direct flights**: LBB has no direct service to most destinations. Nearly all flights connect through DFW, DAL (Love Field), or another hub.
- **Hub premium**: Originating from a spoke airport adds $80–$200+ per round trip vs originating from the hub itself.
- **Fewer options**: Limited carrier competition at LBB means less price flexibility.
- **Connection time**: Average 2-3 hour layover per connection adds ~4-6 hours total travel time per round trip.

### Cost estimates by destination

These should be updated periodically by searching current flight prices. The estimates below reflect typical 2025-2026 pricing patterns.

| Destination | Airport | LBB Round Trip (est.) | DFW Round Trip (est.) | Savings | DFW Direct? |
|---|---|---|---|---|---|
| Boston, MA | BOS | $400–$500 | $200–$300 | ~$150–$200 | Yes |
| Newark, NJ | EWR | $350–$430 | $180–$230 | ~$150–$200 | Yes |
| Washington, DC | DCA/IAD | $320–$400 | $150–$220 | ~$140–$180 | Yes |
| St. Louis, MO | STL | $280–$360 | $140–$180 | ~$140–$180 | Yes |
| Grand Rapids, MI | GRR | $370–$450 | $200–$260 | ~$150–$200 | Yes (AA) |
| Dayton, OH | DAY | $330–$400 | $160–$220 | ~$150–$180 | Yes (AA) |
| Fort Wayne, IN | FWA | $350–$420 | $180–$230 | ~$150–$190 | Limited |
| Albany, NY | ALB | $380–$470 | $220–$280 | ~$150–$200 | Yes (AA) |
| Minneapolis, MN | MSP | $320–$400 | $150–$200 | ~$150–$200 | Yes |
| Nashville, TN | BNA | $280–$350 | $120–$170 | ~$140–$180 | Yes |
| Columbus, GA | CSG | $400–$500 | $250–$320 | ~$130–$180 | Via ATL |

### Averaging for projections

When calculating projected annual savings:

1. Take the midpoint of each destination's savings range
2. Weight by frequency of travel to each destination (from trip history)
3. If destination frequency isn't clear, use unweighted average across all destinations visited

**Conservative estimate**: Use $120/trip savings (low end of ranges)
**Moderate estimate**: Use $150/trip savings (midpoint)
**Optimistic estimate**: Use $180/trip savings (high end)

For the business case, use the **moderate estimate** as the primary figure and mention the range.

### Additional cost factors beyond flights

**Costs that DECREASE with DFW relocation:**

- Flight prices (as above)
- Mileage reimbursements for driving to LBB airport (~60-70 miles each way from Plains)
- Connection layover time (productivity lost)
- Occasional need for overnight hotel at LBB before early flights
- Car rental frequency (currently needed at many destinations because LBB routing makes timing tight)

**Costs that INCREASE with DFW relocation:**

- Rent differential (DFW metro is higher than Lubbock/Plains area)
- Cost of living adjustment (moderate — DFW is affordable compared to other major metros)
- Airport parking at DFW (offset by proximity — can rideshare or use DART)

**Costs that stay NEUTRAL:**

- Hotel costs at destination (same regardless of origin)
- Meals during travel (same regardless of origin)
- Car rental at destination (may decrease slightly with better flight timing)

### Mileage calculations

Current mileage reimbursement from Plains/Lubbock to LBB:

- Plains to LBB airport: ~60 miles one way
- Round trip per flight: ~120 miles
- At $0.67/mile IRS rate: ~$80 per flight trip
- Projected 24 trips/year: ~$1,920/year in mileage just getting to the airport

From DFW metro (e.g., Grapevine) to DFW Airport:

- ~5-10 miles one way
- Round trip: ~10-20 miles
- At $0.67/mile: ~$7-$13 per flight trip
- Projected 24 trips/year: ~$170-$320/year

**Mileage savings: ~$1,600–$1,750/year**
