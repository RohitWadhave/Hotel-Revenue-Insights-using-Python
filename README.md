# AtliQ Grand Hotels Revenue Data Analysis using Python

*1. Data Import and Exploration*

**Datasets:**
- dim_date.csv
- dim_hotels.csv
- dim_rooms.csv
- fact_aggregated_bookings
- fact_bookings.csv

**Reading bookings data:**
```python
df_bookings = pd.read_csv('datasets/fact_bookings.csv')
```

**Exploring bookings data:**
```python
df_bookings.head()
df_bookings.shape
df_bookings.room_category.unique()
df_bookings.booking_platform.unique()
df_bookings.booking_platform.value_counts()
df_bookings.booking_platform.value_counts().plot(kind="bar")
df_bookings.describe()
```

**Reading other files:**
```python
df_date = pd.read_csv('datasets/dim_date.csv')
df_hotels = pd.read_csv('datasets/dim_hotels.csv')
df_rooms = pd.read_csv('datasets/dim_rooms.csv')
df_agg_bookings = pd.read_csv('datasets/fact_aggregated_bookings.csv')
```

*2. Data Cleaning*

**Cleaning invalid guests:**
```python
df_bookings = df_bookings[df_bookings.no_guests > 0]
```

**Outlier removal in revenue generated:**
```python
# Checking for negative values
df_bookings = df_bookings[df_bookings.revenue_generated > 0]
```

*3. Data Transformation*

**Creating occupancy percentage column:**
```python
df_agg_bookings['occ_pct'] = df_agg_bookings['successful_bookings'] / df_agg_bookings['capacity']
df_agg_bookings['occ_pct'] = df_agg_bookings['occ_pct'].apply(lambda x: round(x * 100, 2))
```

*4. Insights Generation*

**Average occupancy rate in each room category:**
```python
df = pd.merge(df_agg_bookings, df_rooms, left_on="room_category", right_on="room_id")
df.drop("room_id", axis=1, inplace=True)
df.groupby("room_class")["occ_pct"].mean()
```

**Average occupancy rate per city:**
```python
df.groupby("city")["occ_pct"].mean()
```

**Occupancy analysis - Weekday vs. Weekend:**
```python
df_date.groupby("day_type")["occ_pct"].mean().plot(kind="bar")
```

**Month-wise occupancy for different cities (June):**
```python
df_june_22 = df[df["mmm yy"] == "Jun 22"]
df_june_22.groupby('city')['occ_pct'].mean().round(2).sort_values(ascending=False).plot(kind="bar")
```

**Appending new data for August:**
```python
df_august = pd.read_csv("datasets/new_data_august.csv")
latest_df = pd.concat([df, df_august], ignore_index=True, axis=0)
```

*5. Business Insights*

1. **Unique Property IDs in Aggregate Bookings:**
   ```python
   df_agg_bookings.property_id.unique()
   ```

2. **Total Bookings per Property ID:**
   ```python
   df_agg_bookings.groupby("property_id")["successful_bookings"].sum()
   ```

3. **Days with Bookings Greater than Capacity:**
   ```python
   df_agg_bookings[df_agg_bookings.successful_bookings > df_agg_bookings.capacity]
   ```

4. **Properties with Highest Capacity:**
   ```python
   df_agg_bookings[df_agg_bookings.capacity == df_agg_bookings.capacity.max()]
   ```

*6. Data Cleaning Insights*

1. **Cleaning Invalid Guests:**
   - Removed records with less than zero guests.

2. **Outlier Removal in Revenue Generated:**
   - Cleaned revenue_generated values above and below certain thresholds.

*7. Data Transformation Insights*

1. **Creating Occupancy Percentage Column:**
   - Created the 'occ_pct' column representing the percentage of successful bookings to capacity.



***8. Additional Business Insights***

1. **Average Occupancy Rate in Each Room Category:**
   ```python
   df.groupby("room_class")["occ_pct"].mean()
   ```

2. **Average Occupancy Rate Per City:**
   ```python
   df.groupby("city")["occ_pct"].mean()
   ```

3. **Occupancy Analysis - Weekday vs. Weekend:**
   - Analyzed and visualized the average occupancy rates for weekdays and weekends.

4. **Month-wise Occupancy for Different Cities (June):**
   ```python
   df_june_22.groupby('city')['occ_pct'].mean().round(2).sort_values(ascending=False)
   ```

5. **Revenue Realized per City:**
   ```python
   df_bookings_all.groupby("city")["revenue_realized"].sum()
   ```

6. **Month by Month Revenue:**
   ```python
   df_date.groupby("mmm yy")["revenue_realized"].sum()
   ```

7. **Revenue Realized per Hotel
   ```python
   df_bookings_all.groupby("property_name")["revenue_realized"].sum().round(2).sort_values()
   ```

9. **Average Rating Per City:**
   ```python
   df_bookings_all.groupby("city")["ratings_given"].mean().round(2)
   ```

10. **Pie Chart of Revenue Realized per Booking Platform:**
   ```python
   df_bookings_all.groupby("booking_platform")["revenue_realized"].sum().plot(kind="pie")
   ```
