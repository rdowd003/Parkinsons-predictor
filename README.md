# Predicting The Progression of Parkinson's Disease Scores With Voice Processing Metrics

The goal of this analysis is to use a number of telemonitoring metrics to
predict the _Unified Parkinson's Disease Rating Scale (UPDRS) score_
in patients suffering from early stage Parkinson's Disease.

## What is Parkinson's Disease and how is severity scored?

**Parkinson's disease** is a neuro-degenerative condition that manifests
symptoms predominantly through the loss of functionality in motor-cognitive
regions of the brain. There is still no "cure" and disease progression is often
swift. While onset generally begins later in life, it can onset in early
adulthood, in which case it is referred to as "early onset".

Progression is tracked in accordance with a UPDRS score that is measured each
time a PD patient is seen by a neurology clinician. This score is a
combination of rated symptoms (0-4) that fall under four general categories with max ranges:
  1. Cognition, behavior and mood (0-16)
  2. Affected daily activities (0-56)
  3. **Motor skills** (0-108)
  4. Complications/responses under therapy (0-23)


The bulk of this analysis focuses on measures of speech impairments
(motor-category) and the use of vocal perturbance measures to predict scores
in several patients. Several studies have reported prevalence rates of vocal
impairment symptoms as high as 70-90% after onset in Parkinson's disease
patient, and may be on of the earliest indicators of disease onset[1].

Recent literature has shown promising results illustrating the ability of the
vocal processing metrics (used in this analysis) to distinguish healthy patients
from those diagnosed with Parkinson's Disease [1,2,8].The benefit of developing
an accurate model using these metrics is two fold:

1. Improve the clinician's ability to *diagnose disease onset
earlier* than later in order to extend the presence of a higher quality of life.
2. Develop a reliable *remote disease-progression tracking
mechanism* that patients can use at home.


##### Focus is: 1, how scores are distributed at onset and 2, how scores change
##over time


## Data
The *Parkinsons Telemonitoring Data Set* used in this analysis was obtained from
the UCI Machine Learning Repository[1].

The data was collected over a 6 month period from a total of 42 patients
diagnosed (within previous 5 years) with early-onset Parkinson's Disease.
During the 6 month period, these patients were unmedicated and did not receive
any therapeutic intervention.

Each of the 42 patients recorded nearly 200 sessions over the 6 month period
during which they were instructed to record sustained phonations ("ahhh") for 30
seconds. From this data, 16 classic measures of vocal signal processing methods
were implemented on all sessions.

## EDA Plots
Population/scores:
1. Distribution of scores over all; scores at beginning?
2. Change in scores over time
3. Individual change in scores over time


## Features
The initial dataset included the following metrics (16):

**Demographics**

subject # - unique patient identifier
age - subject age; range 36-85
sex - male/female
test_time - number of days since recruitment

**Periodicity**

Jitter(%) - Difference between length of signal periods in consecutive cycles divided by average
Jitter(Abs) - absolute of average
Jitter:RAP - average of difference between a peak and the difference between
it and it's two closes neighbors
Jitter:PPQ5 - 5 closes neighbors
Jitter:DDP - average of differences between consecutive differences (Jitter(%))

**Amplitude**

Shimmer - difference in amplitude between consecutive peaks
Shimmer(dB) - base-10 log of difference in consecutive amplitudes
Shimmer:APQ3 - average of difference between amplitude of a peak and the average
amplitude of three closest neighbors
Shimmer:APQ5 - 5 closes neighbors
Shimmer:APQ11 - 11 closest neighbors
Shimmer:DDA - average of differences between consecutive differences (shimmer)

**Noise**

NHR - ratio of noise : tonal components in voice; noise-harmony ratio
HNR - ratio of noise : tonal components in voice; harmonics-noise ratio
DFA - Detended Flucuation Analysis: quantifies self-similarity of noise

**Energy**

RPDE - Recurrence Period Density Entropy : measure of vocal cords ability to
sustain stable vibrations
PPE - Pitch Period Entropy: measure of impairment in pitch stability


## Feature Engineering

Not a whole lot at the beginning, more just removing subject# and test time
as these are not related

Features scatters:
1. Scatter matrix of overall components
2,3,4,5. Scatter Matrices of 4 components: Jitter, Shimmer, energy, noise

## Modeling

1. Generating train & test splits

  i)   Reserving last fourth of patients - better for 'new patient' model, worse predictive ability (lower R^2)

  ii)  Randomizing train/test subsets - not useful for a 'new patient' model

 iii) Training model with top 75% of data for each patient - 'Established patient model'

2.  Model Results

  i) Split by patient

| Model Result  | Linear Model  | Ridge Model  | Lasso Model  |
| --------------- |:---------------:|:---------------:|---------------:|
| R^2 | -1.66 | -1.58 | -1.35 |
| Final MSE  |  1.99  | 1.77  | 1.99  |
| Alpha  | N/A   | 24.24   | 0.01  |

*Insert plot of ridge alpha, lasso alpha vs MSE,*
*All 3 prediction plots?*

| Parameter    | Linear  | Ridge  | Lasso  |
| --------------- |:---------------:|
| Age |  0.33  |  0.317519 | 0.32 |
| Sex  |   -0.12   | -0.09 | 0.32 |
| Jitter(%)  | 0.04   | 0.00 | -0.09 |
| Jitter(Abs)  | -0.14  | - 0.00 | 0.00 |
| Jitter:RAP | 0.99   | 0.12 | 0.12 |
| Jitter:PPQ5 | -0.22   |  0.00 |  0.00 |
| Jitter:DDP | -0.55  | 0.00 | 0.00 |
| Shimmer | -0.13 | -0.00 | -0.00 |
| Shimmer(dB)| -0.08 | -0.00 | -0.00 |
| Shimmer:APQ3  | -84.01 | -0.05 | -0.05 |
| Shimmer:APQ5 |   0.26  | -0.00  | -0.00 |
| Shimmer:APQ11  | -0.002  | -0.00 | 0.00 |
| Shimmer:DDA  | 83.87  | -0.00 | -0.00 |
| NHR | -0.22  | -0.15 | -0.15 |
| HNR | -0.20 | -0.16 | -0.16 |
| RPDE | 0.16  | 0.14 | 0.13 |
| DFA | -0.32 | -0.29 | -0.29 |
| PPA| 0.09 | 0.03 | 0.03 |



  ii. Randomized  - maybe just show coefficient path?



Why is it predicting so poorly when literature supports the contrary?


| Parameter    | Variable Inflation Factors |
| --------------- |:---------------:|
| Age |  5.14e+01  |    
| Sex  |  1.95e+00   |
| Jitter(%)  | 1.94e+02   |
| Jitter(Abs)  | 1.93e+01  |
| Jitter:RAP | **2.53e+06**   |
| JItter:PPQ5 |  5.49e+01  |    
| Jitter:DDP | **2.53e+06**   |
| Shimmer | 4.72e+02  |
| Shimmer(dB)| 2.17e+02 |
| Shimmer:APQ3  | **6.43e+07** |
| Shimmer:APQ5 |   1.29e+02  |    
| Shimmer:APQ11  | 4.42e+01  |
| Shimmer:DDA  | **6.43e+07**  |
| NHR | 1.03e+01  |
| HNR | 5.17e+01 |
| RPDE | 4.39e+01  |    
| DFA | 9.23e+01 |
| PPA| 2.86e+01 |


## Future Work:
First and foremost, future work would include the further development of this
model, taking both interactions and the possibility of non-linearity into
account, in order to match the classification evidence and the clinical
observations linking the included features and UPDRS scores.

Ultimately, it would be interesting to investigate whether the (future, more
accurately predicting) model could predict changes in scores over time, or
more simply, disease progression. Prognosis is an incredibly useful tool,
however complicated diseases like Parkinson's can be hard to predict. A model
that can accurately predict a patient's increase in scores over time would
be indispensable.   

*include graphs here related to changes over time*
-scores over time
-Individal patient scores change (show variation, how hard it is to predict)

### References:

1. Athanasios Tsanas, Max A. Little, Patrick E. McSharry, Lorraine O. Ramig (2009),
'Accurate telemonitoring of Parkinson’s disease progression by non-invasive speech tests',
IEEE Transactions on Biomedical Engineering (to appear).

2. Erdogdu Sakar B, Serbes G, Sakar CO (2017) Analyzing the effectiveness of vocal features in early telediagnosis of Parkinson’s disease. PLoS ONE 12(8): e0182428.

3. "UPDRS activity of daily living score as a marker of Parkinson's disease progression"

4. "Assessment of Parkinson Disease Manifestations"

5. Sakar BE, Isenkul ME, Sakar CO, Sertbas A, Gurgen F, Delil S, Apaydin H, Kursun O. Collection and analysis of a Parkinson speech dataset with multiple types of sound recordings. IEEE Journal of Bio- medical and Health Informatics. 2013 Jul; 17(4):828–34. https://doi.org/10.1109/JBHI.2013.2245674 PMID: 25055311

6. Tsanas A, Little MA, McSharry PE, Ramig LO. Nonlinear speech analysis algorithms mapped to a stan-dard metric achieve clinically useful quantification of average Parkinson’s disease symptom severity. Journal of the Royal Society Interface. 2011 Jun 6; 8(59):842–55.

7. Movement Disorder Society Task Force on Rating Scales for Parkinson’s Disease. The Unified Parkin- son’s Disease Rating Scale (UPDRS): status and recommendations. Movement disorders: official jour- nal of the Movement Disorder Society. 2003 Jul; 18(7):738

8. Little MA, McSharry PE, Hunter EJ, Spielman J, Ramig LO. Suitability of dysphonia measurements for telemonitoring of Parkinson’s disease. IEEE transactions on biomedical engineering. 2009 Apr; 56 (4):1015–22. https://doi.org/10.1109/TBME.2008.2005954 PMID: 21399744

9. Sakar BE, Sakar CO, Serbes G, Kursun O. Determination of the optimal threshold value that can be dis- criminated by dysphonia measurements for unified Parkinson’s Disease rating scale. In Bioinformatics and Bioengineering (BIBE), 2015 IEEE 15th International Conference on 2015 Nov 2 (pp. 1–4). IEEE.
#