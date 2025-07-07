# Dynamic Pricing for Urban Parking Lots

## This project is a capstone for the Summer Analytics 2025 program, hosted by the Consulting & Analytics Club × Pathway. 
## It demonstrates the implementation of a real-time, data-driven dynamic pricing engine for urban parking spaces using Python, Pathway, and various data visualization libraries.

### Table of Contents

- Project Overview
- Data Description
- Methodology: Pricing Models
  - Model 1: Enhanced Linear Model
  - Model 2: Demand-Based Sigmoid Model
- Technology Stack
- Setup and Execution
- Code Architecture
- Real-time Visualization
- Results

### Project Overview

Urban parking spaces are limited and in high demand. Static pricing often leads to inefficiencies like overcrowding or underutilization. 
This project addresses the issue by building an intelligent dynamic pricing engine that adjusts fees in real time based on several input features.


### The system:
- Ingests live data
- Engineers relevant features
- Applies one of two pricing models
- Visualizes the pricing evolution in real time

### Data Description

The project uses dataset.csv, simulating live updates from 14 parking lots over 73 days.


### Input Features:
- Location:
  - Latitude, Longitude
- Parking Lot:
  - Capacity
  - Occupancy
  - QueueLength
- Vehicle:
  - VehicleType (e.g., car, bike, truck)
- Environment:
  - TrafficConditionNearby (low, average, high)
  - IsSpecialDay (binary flag)
- Timestamp:
  - LastUpdatedDate
  - LastUpdatedTime

### Methodology: Pricing Models
Two models are implemented, controlled via a modelNumber variable in the code.
Model 1: Enhanced Linear Model

This is a reactive pricing strategy that adjusts the price based on:
- Occupancy rate
- Queue length


### Logic:
- If OccupancyRate > 50%, price increases.
- If QueueNorm > 0.5, a queue boost is added.
- If OccupancyRate < 30%, a decay discount is applied.

### Equation:
Pₜ = clip(P_base + Δ_occupancy + Boost_queue - Decay_occupancy, P_min, P_max)

Where:
- Δ_occupancy = α × (OccupancyRate − 0.5)
- Boost_queue = β if QueueNorm > 0.5, else 0
- Decay_occupancy = γ if OccupancyRate < 0.3, else 0
- Price is bounded between ₹5 and ₹20

### Model 2: Demand-Based Sigmoid Model
A non-linear, multi-feature model that ensures smooth and bounded price changes.
Step 1: Compute Demand Score
Demand = w1·OccupancyRate + w2·QueueNorm + w3·TrafficNorm + w4·SpecialDay + w5·VehicleNorm
Weights:

- w1 = 0.35
- w2 = 0.25
- w3 = 0.10
- w4 = 0.15
- w5 = 0.10

Step 2: Apply Scaled Sigmoid
Multiplier = 0.5 + 1.5 / (1 + e^(-5·(Demand − 0.5)))
Step 3: Final Price
Pₜ = clip(P_base × Multiplier, P_min, P_max)
Technology Stack

### Data Processing:
- Pathway: Real-time stream processing
- NumPy: Mathematical operations
- Pandas: Static data handling

### Visualization:
- Panel: App layout
- hvPlot: Interactive plotting
- Bokeh: Underlying engine
- Streamz: Real-time simulation

### Setup and Execution

1. Upload Files:
   - Upload CapstoneProject.ipynb and dataset.csv to Colab or local IDE.
2. Install Required Libraries:
   !pip install pathway bokeh panel streamz hvplot
3. Restart Runtime:
   - Go to Runtime → Restart
4. Run Notebook:
   - All cells will stream data and display pricing results.

### Code Architecture

Pathway Pipeline:
- Input: pw.io.csv.read() simulates real-time data feed
- Feature Engineering: UDFs like queueNorm, OccupancyRate, etc.
- Pricing Model: compute_dynamic_price() applies selected logic
- Filtering: Focuses on one lot for visualization (e.g. BHMBCCMKT01)
- Output: pw.io.subscribe() sends results to plot

### Real-time Visualization

- Streamz: Buffers stream
- hvPlot: Displays the price line chart
- update_plot(): Called on every new batch
- Panel: Shows the plot in notebook UI

### Results

The final output is a real-time, interactive line chart showing the price fluctuations for one parking lot (BHMBCCMKT01). 
This chart reflects the selected pricing model’s behavior under live data.
