# Medical Appointment No-Show Analysis

An exploratory data analysis of ~110,000 medical appointments in Brazil, investigating what factors predict whether a patient will miss their scheduled appointment. The Analysis focuses on two questions primarily, but could be extended to examine other factors and explore the relationship between variables.

## Questions Explored

1. **Does scheduling lead time affect no-show rates?** — Does booking further in advance make a patient more likely to miss their appointment?
2. **Do SMS reminders affect no-show rates?** — Are patients who receive a reminder more likely to show up?

## Dataset

**Source:** [Kaggle — Medical Appointment No Shows](https://www.kaggle.com/datasets/joniarroba/noshowappointments)

The dataset contains 110,527 appointment records from Brazilian public health clinics, collected in 2016. Each record includes:

| Column | Description |
|---|---|
| `PatientId` | Unique patient identifier |
| `AppointmentId` | Unique appointment identifier |
| `Gender` | Patient gender (M/F) |
| `ScheduledDay` | Date and time the appointment was booked |
| `AppointmentDay` | Date of the actual appointment |
| `Age` | Patient age |
| `Neighbourhood` | Clinic location |
| `Scholarship` | Whether the patient is enrolled in the Bolsa Família welfare program |
| `Hypertension` | Hypertension diagnosis flag (0/1) |
| `Diabetes` | Diabetes diagnosis flag (0/1) |
| `Alcoholism` | Alcoholism flag (0/1) |
| `Handicap` | Disability flag (0/1) |
| `SMS_received` | Whether a reminder SMS was sent (0/1) |
| `No_Show` | Whether the patient missed the appointment (1 = no-show, 0 = showed) |

## Key Findings

**Lead Time and No-Show Rate**

Same-day appointments had the lowest no-show rate at **6.65%**. The rate rose sharply to 25% for appointments booked 1–7 days out, and continued climbing to a peak of **34.08%** for appointments scheduled 31–60 days in advance. The pattern is consistent: the further out an appointment is booked, the less likely a patient is to show.

| Lead Time | No-Show Rate |
|---|---|
| Same Day | 6.65% |
| 1–7 Days | 25.01% |
| 8–14 Days | ~28% |
| 15–30 Days | ~30% |
| 31–60 Days | 34.08% |

**SMS Reminders and No-Show Rate**

Counterintuitively, patients who received an SMS reminder had a *higher* no-show rate (27.57%) than those who did not (16.70%). This result should not be interpreted as evidence that SMS reminders cause no-shows. A more plausible explanation is selection bias — SMS reminders may have been preferentially sent to patients already identified as at-risk or to those with appointments booked further in advance (where no-show rates are already higher). The dataset lacks SMS send timestamps, which limits further analysis.

**Gender**

Both male and female patients had no-show rates close to 20%, suggesting gender alone is not a meaningful predictor.

## Methodology

**Data Wrangling**
- Renamed columns to fix typos (`Hipertension` → `Hypertension`, `Handcap` → `Handicap`, `No-show` → `No_Show`)
- Cast `PatientId` from float to `Int64`
- Removed records with negative patient ages
- Collapsed `Handicap` values > 1 to 0 (treating anything other than a confirmed `1` as `0`)
- Mapped `No_Show` from `Yes`/`No` strings to `1`/`0` integer flags

**Lead Time Engineering**
- Computed `LeadTime` as `(AppointmentDay - ScheduledDay).dt.days`
- Records with negative lead times (appointments logged with a scheduled date after the appointment date) were clipped to 0, treating them as same-day bookings rather than dropped — this preserved ~1/3 of the dataset that would otherwise be lost
- Binned lead time into: Same Day, 1–7 Days, 8–14 Days, 15–30 Days, 31–60 Days, 60+ Days

**EDA**
- Univariate analysis of appointment outcomes and lead time distribution
- Bivariate analysis of no-show rate by lead time bin, SMS receipt, and gender
- Reusable `plot_noshowrate()` helper function for consistent bar chart generation

## Limitations

- **No distance data:** Patient proximity to the clinic is unknown. Neighborhood-level no-show rates could be explored but would be speculative without travel or distance context.
- **No SMS timing data:** The dataset only records whether an SMS was sent, not when. Without send timestamps it's impossible to test whether reminder timing affects show rates.
- **Confounding variables not explored:** Health condition flags (hypertension, diabetes, alcoholism) and scholarship status were not incorporated into this analysis. These may interact with both lead time and no-show behavior.

## Tools & Libraries

- Python 3
- pandas
- NumPy
- matplotlib
- Jupyter Notebook

## Running the Notebook

```bash
git clone https://github.com/Westcornbread/no-show-appointments.git
cd no-show-appointments
pip install pandas numpy matplotlib jupyter
jupyter notebook Investigate_a_Dataset.ipynb
```

The dataset (`noshowappointments-kagglev2-may-2016.csv`) should be placed in a `Database_No_show_appointments/` subdirectory, or the file path in the notebook updated to match your local setup.
