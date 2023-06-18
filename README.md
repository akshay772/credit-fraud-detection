Implementing a solution for fraud detection

### Data Preprocessing

1. **Data Merge**: Merge the "cc_info" and "transactions" tables based on the "credit_card" column.

2. **Feature Extraction**: Identify relevant features from the merged table. The primary features would be "transaction_dollar_amount", "credit_card_limit", and "date". It might also be beneficial to engineer new features such as "transaction_amount_relative_to_limit" (transaction_dollar_amount divided by credit_card_limit) or "transaction_frequency" (the number of transactions made in a certain time period). Geographic location could be useful too, as sudden transactions from distant locations may indicate fraudulent activity.
   1. 'transaction_hour': The hour when the transaction occurred.
   2. 'transaction_day_of_week': The day of the week when the transaction occurred (where Monday is 0 and Sunday is 6).
   3. 'transaction_amount_relative_to_limit': The ratio of the transaction dollar amount to the credit card limit.
   4. 'transaction_frequency': The number of transactions made by each credit card in the last 24 hours.
   5. 'avg_transaction_amount_24h': The average amount of transactions made by each credit card in the last 24 hours.
   6. Extracting features from longitude and latitude can be a bit tricky because these are geographical data. There are multiple ways to handle this data depending on the specific requirements of project :
      1. DBSCAN algorithm to cluster transactions based on their geographical location, calculates the centroid of the largest cluster (the most common location), and then calculates the distance of each transaction from this centroid. The distance is stored in a new 'distance_from_common_location' column in the DataFrame.

### Model Selection
1. **Autoencoder**: autoencoder with one hidden layer (the "encoder" layer) and an output layer (the "decoder" layer). The autoencoder is then trained using the scaled data as both the input and the target. After training, the autoencoder is used to reconstruct the data, and the mean squared error (MSE) between the original data and the reconstructed data is computed. An anomaly is defined as an instance where the MSE exceeds a certain threshold. Here it is assumed that 5% of the transactions are fraudulent.
2. **IsolationForest**: used anomaly detection algorithm is the Isolation Forest. The transactions labeled as -1 are considered potential frauds. The contamination parameter in the IsolationForest model is an estimate of the proportion of anomalies in the data. Here, it is set to 0.01, indicating that we expect about 1% of the transactions to be fraudulent.