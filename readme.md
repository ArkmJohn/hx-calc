# HYXiPOWER Solar Savings Calculator

A native HTML5, CSS3, and vanilla JavaScript solar savings and sizing calculator customized for the Philippine market under Manila Electric Company (Meralco) distribution utility pricing.

The calculator maps monthly electricity spend directly to **Property Types with defined usable rooftop area ranges (sqm)**, recommending matching **HYXI Inverter SKUs** (Residential Hybrid Inverters, Microinverters, and Commercial Energy Storage Systems) while accounting for hardware-level advantages such as 60V ultra-low voltage startup and high-current PV module pairing.

---

## 1. Property Type & Usable Rooftop Area Matrix

The calculator uses preset rooftop area ranges and consumption profiles to determine optimal system capacity:

| Property Type | Usable Rooftop Area | Typical PV Capacity (700W Modules) | Default Monthly Bill | Primary HYXI Inverter Pairing |
| :--- | :--- | :--- | :--- | :--- |
| **Small Home** | **15 - 35 sqm** | 4 to 10 panels (~2.8 kWp to 7.0 kWp) | PHP 8,000 | `H5K/6K-LS` Residential Hybrid |
| **Big Home** | **40 - 90 sqm** | 12 to 24 panels (~8.4 kWp to 16.8 kWp) | PHP 25,000 | `H6K/8K-LS` High-Density Hybrid |
| **Apartment** | **10 - 25 sqm** | 2 to 6 panels (~1.4 kWp to 4.2 kWp) | PHP 5,000 | `M800/1000-SW` Dual Microinverter |
| **Enterprise** | **100+ sqm** | 28+ panels (~19.6 kWp+) | PHP 48,000 | `H50K-125K-ET` Three-Phase / Halo ESS |

---

## 2. Philippine Energy & Sizing Benchmarks

### A. Meralco Retail Electricity Tariff
* **Benchmark Rate ($R$):** `PHP 12.00 / kWh` (blended residential/commercial benchmark reflecting generation, transmission, distribution, system loss, and VAT).

### B. Solar Irradiance & Peak Sun Hours (PSH)
* **Average Philippine Daily Irradiance ($PSH$):** `4.2 Peak Sun Hours / day`
* **Billing Period ($D$):** `30 Days / month`

### C. Location Derating Multipliers ($F_{\text{loc}}$)
* **Metro Manila (Meralco Main):** `1.00`
* **Central Luzon (Bulacan / Pampanga):** `1.03` (higher open-plain solar yield)
* **CALABARZON (Cavite / Laguna / Batangas):** `0.97`
* **North Luzon High Irradiance Area:** `1.05`

### D. HYXI Low-Voltage Generation Advantage
* **Ultra-Low 60V Startup:** Operates earlier at dawn and later into dusk, adding an estimated `+700 kWh/year` (~`58.33 kWh/month`) per inverter unit.
* **18A High PV Input Current:** Fully compatible with modern high-power 650W-700W solar modules without current clipping.
* **Extreme Heat Derating Performance:** Maintains 80% sustained output power even at 55°C ambient temperature.

---

## 3. Mathematical Models & Formulas

### Step 1: Baseline Monthly Energy Consumption ($E_{\text{cons}}$)
Determined by dividing the user's monthly Meralco bill by the benchmark tariff:

$$E_{\text{cons}} = \frac{\text{Monthly Bill (PHP)}}{R}$$

### Step 2: Target Monthly Solar Output ($E_{\text{gen}}$)
Accounts for property self-consumption capacity, environmental multipliers, and the HYXI startup advantage:

$$E_{\text{gen}} = \left( E_{\text{cons}} \times \eta_{\text{offset}} \times F_{\text{loc}} \right) + \left( E_{\text{startup}} \times N_{\text{units}} \right)$$

*Where:*
* $\eta_{\text{offset}}$ = Target daytime offset percentage (60% to 85% depending on property scale).
* $E_{\text{startup}}$ = `58.33 kWh/month` (HYXI 60V early startup gain).
* $N_{\text{units}}$ = Selected inverter count.

### Step 3: Required Peak System Sizing ($P_{\text{capacity}}$)
Calculates the DC system size in Kilowatts-peak (kWp) needed to produce the target monthly generation:

$$P_{\text{capacity}} = \frac{E_{\text{gen}} / D}{PSH} = \frac{E_{\text{gen}}}{30 \times 4.2}$$

### Step 4: Estimated Monthly Savings (PHP)
Calculated from avoided Meralco grid purchases:

$$\text{Savings} = \min\left( \text{Bill} \times 0.75, \; E_{\text{gen}} \times R \times \eta_{\text{offset}} \right)$$

*A conservative 75% cap is applied to protect against fixed distribution and meter fees.*

### Step 5: Gross Generation Financial Value (PHP)
Represents the total retail replacement value of all generated energy:

$$\text{Generation Value} = E_{\text{gen}} \times R$$

### Step 6: Module Count & Inverter Sizing
* **Module Count:** Estimated for 650W-700W modules:
  $$\text{Module Count} = \lceil P_{\text{capacity}} \times 1.5 \rceil$$
* **SKU Selection:** Dynamically mapped based on system capacity:
  * $\le 2.5\text{ kWp}$: `M800/1000-SW` Microinverter
  * $2.6 - 5.5\text{ kWp}$: `H5K/6K-LS` Residential Hybrid
  * $5.6 - 12.0\text{ kWp}$: `H6K/8K-LS` High-Density Hybrid
  * $> 12.0\text{ kWp}$: `H50K-125K-ET` Three-Phase / Halo Modular ESS

---

## 4. Architecture & Reactive Pipeline

### User Inputs
* **Property Type:** Small Home, Big Home, Apartment, Enterprise
* **Area Tag Indicator:** 15-35 sqm, 40-90 sqm, 10-25 sqm, 100+ sqm
* **Monthly Electricity Bill Slider:** PHP 2,000 to PHP 60,000
* **Location / Grid Utility Dropdown:** Regional irradiance factors
* **Inverter Device Count:** Number of parallel units

### Calculation Engine
* Evaluates consumption via Meralco benchmark (PHP 12.00/kWh)
* Factors in daily peak sun hours (4.2 PSH)
* Applies HYXI 60V low-voltage yield bonus (+58.33 kWh/month)
* Computes kWp capacity and matches hardware SKU

### UI Metric Display Cards
* **Savings Range Card (Black Card):** Estimated monthly bill savings in PHP
* **Generation Range Card (Green Card):** Total financial generation value plus startup yield note
* **System Size Card (Blue Card):** Total estimated monthly kWh yield and recommended hardware sizing
* **Preferred SKU Card:** Dynamic inverter model name, description, and feature badges

---

## 5. Deployment Notes

* **Zero Dependencies:** Pure HTML5, CSS3, and vanilla JavaScript without external libraries or build chains.
* **Embed Ready:** Easily drop into a Webflow custom code embed block, a WordPress custom HTML block, or any static hosting environment.
* **Fully Responsive:** CSS Grid layout shifts cleanly from a two-column desktop arrangement to a single-column stacked view on screens below 860px width.