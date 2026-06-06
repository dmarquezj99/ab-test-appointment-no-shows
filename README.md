# A/B Testing: Do SMS Reminders Reduce Appointment No-Shows?

A statistical analysis of whether SMS reminders reduce patient no-shows — and a case study in why **observational data can't be read at face value**. Built in Python (`pandas`, `statsmodels`, `matplotlib`) on Google Colab.

> **Open the notebook** (`Medical_Appointment_No_Shows.ipynb`) to see the full analysis, code, and charts rendered inline.

<!-- ![No-show rate by SMS reminder](<img width="493" height="390" alt="bar chart" src="https://github.com/user-attachments/assets/7354e557-8057-4d27-8a39-5fbb134022ac" />) -->

---

## Business question

Does sending an **SMS reminder** reduce the rate at which patients miss their appointments?

- **H₀ (null):** no-show rate is the same with or without an SMS reminder.
- **H₁ (alternative):** the no-show rates differ.
- **Metric:** no-show rate (a proportion). **Significance level:** α = 0.05.

## Data

[Medical Appointment No Shows](https://www.kaggle.com/datasets/joniarroba/noshowappointments) (Kaggle) — ~110,000 real outpatient appointments in Brazil, with an SMS-reminder flag, a no-show outcome, and scheduling dates.

## Tools

`Python` · `pandas` · `statsmodels` (two-proportion z-test, power analysis) · `matplotlib` · Google Colab

---

## The analysis (and the trap)

### 1. The naive comparison gave a result that made no sense
A two-proportion z-test on the raw data showed that patients who received an SMS reminder had a **higher** no-show rate than those who didn't:

| Group | No-show rate |
|-------|--------------|
| No SMS | **16.7%** |
| SMS | **27.5%** |

z = 42.06, **p < 0.001**, 95% CI for the difference: **[+10.3, +11.4 percentage points]**.

The result is highly significant — and points the wrong way. Taken literally, it says sending reminders *causes* people to skip appointments, which is implausible. A statistically strong result in an absurd direction is a signal of **confounding**, not a finding.

### 2. The confounder: appointment lead time
SMS reminders were only sent to appointments **booked in advance** — same-day appointments (median lead time **0 days** for the no-SMS group) almost never received a text and almost never resulted in a no-show (**4.6%**). As appointments were booked further ahead, *both* the SMS rate and the no-show rate rose together:

| Lead time | No-show rate | SMS rate |
|-----------|-------------|----------|
| Same day | 4.6% | ~0% |
| 1–3 days | 22.8% | ~0% |
| 4–7 days | 25.2% | 61% |
| 8–14 days | 30.4% | 58% |
| 15–30 days | 32.5% | 61% |
| 30+ days | 33.8% | — |

Lead time drives both variables, so the naive comparison was really measuring "booked far ahead vs. same day," not the effect of the reminder.

### 3. A fairer comparison reverses the effect
Restricting to appointments booked **at least one day in advance** (so the groups are comparable), the direction flips:

| Group (booked ≥1 day ahead) | No-show rate |
|------------------------------|--------------|
| No SMS | **29.4%** |
| SMS | **27.6%** |

z = −5.53, **p < 0.001**. Controlling for a single confounder turned an apparent +11-point *harm* into a small *favorable* signal (~2 points lower).

---

## Conclusion & recommendation

- The naive "SMS increases no-shows" result is an **artifact of confounding**, not a real effect.
- After controlling for lead time, SMS reminders are associated with a small reduction in no-shows — but this is **still observational data**, with other potential confounders (age, chronic conditions, booking channel) left uncontrolled. **Causation cannot be established here.**
- **Recommendation:** don't change reminder policy based on this data. Run a **randomized controlled experiment** — randomly assign SMS vs. no-SMS among patients booking in advance — to measure the true causal effect.

### Designing that experiment (power analysis)
To reliably detect a 3-percentage-point change in no-show rate at α = 0.05 and 80% power, the experiment would need **≈ 2,626 patients per group** (~5,250 total).

---

## Methodology notes

- **Data quirks handled:** the `No-show` column is labeled inversely (`"Yes"` = the patient did *not* attend), and a small number of records had impossible negative lead times (booked after the appointment), which were removed.
- **Why this matters:** the project demonstrates that statistical significance ≠ causation. The same dataset yields opposite conclusions depending on whether confounders are controlled — the core reason randomized experiments exist.

## What this project demonstrates

Hypothesis testing (two-proportion z-test), confidence intervals, **confounding and correlation-vs-causation reasoning**, experiment design, and statistical power analysis — implemented in Python with pandas and statsmodels.<img width="493" height="390" alt="bar chart" src="https://github.com/user-attachments/assets/a86af265-de63-4d8c-959c-9f9599f08978" />
