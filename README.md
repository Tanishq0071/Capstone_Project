# CareLoop_Clinics - No-Show Guard
# Capstone_Project Repository Link - https://github.com/Tanishq0071/Capstone_Project


## Summary
1. Cleaning the 1,020 raw bookings removed 20 duplicates, 6 invalid dates, and 4 blank statuses, leaving 990 valid appointments with 218 no-shows (22.0%).
2. SQL analysis showed that no-shows jump sharply with booking lead time, rising from 16.2% for 1-7 days to 30.0% for 15-30 days and 60.5% for 31+ days, with high rates at Hadapsar Care Centre (27.3%) and Gomti Nagar Care Point (33.3% across 90 visits).
3. Among the models tested using 5-fold cross-validation on the training set, Logistic Regression had the strongest ROC-AUC (0.77 CV, 0.79 on test) and flagged 49 of 198 test bookings at the 0.35 threshold, catching 57.1% of actual no-shows.
4. Using Gemini to structure the first 10 call-centre notes (9 valid, 1 skipped blank), 5 of the 10 missed visits had no reminder sent, including both `forgot_or_no_reminder` cases (N001 and N007).

## Recommendation
Appointments booked more than two weeks ahead (29.9% of all bookings, with a 60.5% no-show rate at 31+ days) and bookings without a reminder sent are the most likely to be missed, while the call-centre notes point to forgotten appointments, transport/distance issues, work conflicts, fees, and illness as the main reasons patients skip. CareLoop should start by turning on automated reminders for every booking—especially those made 15+ days in advance—since sending a reminder is associated with roughly 69% lower odds of a no-show in the fitted model, and both forgotten-visit notes in the sample had no reminder sent. At the same time, the front desk should use the evening-before risk scores at the 0.35 threshold to make targeted confirmation calls, starting with high-volume clinics like Hadapsar Care Centre (27.3% no-show rate across 143 visits) and Malviya Nagar Clinic (26.8% across 142 visits).

## Model Choice and Threshold
I chose Logistic Regression (`StandardScaler` + `LogisticRegression` inside a pipeline) because it achieved the highest 5-fold cross-validated ROC-AUC on the 792 training rows (0.77, compared to 0.68 for the depth-3 Decision Tree and 0.75 for the default Random Forest) and scored 0.79 ROC-AUC on the single held-out test evaluation without overfitting. I selected a probability threshold of 0.35 instead of 0.50 based on out-of-fold training predictions, which improved F1 and nearly doubled recall. Catching missed visits early matters more here than avoiding a few extra confirmation calls, though in practice a clinic would set this cutoff based on how many calls staff can make each evening.

## Limitations
- The 10 analysed follow-up notes are only a small illustration and cannot represent the true clinic-wide breakdown of why patients miss visits.
- The medians, quartiles, and outlier caps in Part 1 were computed across all 990 rows before splitting, and the random split allows the same patient to appear in both train and test instead of testing past-to-future generalization.
- Because the booking records are observational, the lower no-show odds for reminded appointments show an association rather than proof that reminders alone cause higher attendance.
