First step of scoring the hits using statistical score -:
FACTORS INVOLVED = Normalized identity and Normalized e-value 

Normalized identity = round (df["pident"]/100 , 3)
Normalized e-value = round (np.minimum (-np.log10(df["evalue"])/50))

Statistical score = w1 * df["Norm_identity_score"] + w2 * df["Norm_evalue_score"]

We have updated the hits or we can say filtered the hits based on the confidence scoring phase 1 using statistical factors.

The statistical score was computed as a weighted sum of normalized percent identity and normalized E-value. 
Identity was given slightly higher weight (0.6) to emphasize biological similarity, while E-value (0.4) accounted for 
statistical chance exclusion. The weights sum to one to maintain score normalization and interpretability.

