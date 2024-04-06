# Factors Contributing to Flight Delay and Cancellation
![image](https://github.com/karlarochaes/flight-delay-cancellation-USA-2023/assets/88100992/c81f7f43-aa72-4f51-8f69-98483e6a3b2d)

---

## Table of Contents
- [Introduction](#introduction)
- [Tools](#tools)
- [Data Sourcing](#data-sourcing)
- [Data Cleaning](#data-cleaning)
- [Models](#models)
- [Results](#results)
- [Visualization](#visualization)
- [Conclusions and Recommendations](#conclusions-and-recommendations)

## Introduction
Datalab, a data consulting firm with a diverse clientele across various sectors, has been tasked with a project to extract valuable insights from a dataset containing flight information for the United States in January 2023. The client's objective is to conduct a user-centric analysis aimed at generating recommendations for selecting flights with specific attributes to minimize the risk of delays or cancellations.

## Tools
- SQL (Google BigQuery)
- Python (Google Colab and Jupyter)
- Looker Studio

## Data Sourcing
Three tables:
- **flights_202301** with 538837 rows and 31 columns.
- **DOT_CODE_DICTIONARY** with 1737 rows and 2 columns.
- **AIRLINE_CODE_DICTIONARY** with 1729 rows and 2 columns.

## Data Cleaning
The following data was removed from the database:
- Flights occurred during January 12th. This day had a peak of delays and cancellations due to a technical issue. For more information, see [this Wikipedia article](https://en.wikipedia.org/wiki/2023_FAA_system_outage).

## Models
In order to try to predict whether a flight is going to be delayed or canceled, two different models were implemented: the relative risk model and a machine learning model.
### Relative Risk Model
Relative risk was calculated with the following function in Python:

```python
def RelativeRisk(datos, variable, categoria):
    contingency_table = pd.crosstab(datos[variable], datos['fl_category'])
    tasa_cancelacion_categoria = contingency_table[categoria] / 
                                  contingency_table.sum(axis=1)
    tasa_cancelacion_comparacion = (contingency_table[categoria].sum() - contingency_table[categoria]) /
                                    (contingency_table.sum().sum() - contingency_table.sum(axis=1))

    riesgo_relativo = tasa_cancelacion_categoria / tasa_cancelacion_comparacion
    scores = (riesgo_relativo > 1.4).astype(int)

    resultados_risk_ratio = pd.DataFrame({
        variable: contingency_table.index,
        variable + '_' + categoria + '_rr': riesgo_relativo.values,
        variable + '_' + categoria + '_score': scores.values
    })

    return resultados_risk_ratio
```
Although several variables had high relative risk values, using these numbers to create a score for delay or cancellation did not led to an accurate classification of flights.

### Machine Learning Model
Given that this project aimed to identify the characteristics of flights categories, we selected a random forest algorithm. Since the computer resources needed were higher compared to the relative risk model, this code was run locally using Jupyter.
Unfortunately, the performance of this model was low, so no relevant conclusions could be obtained. Following this idea, we suggest trying with another algorithm or incrementing the available data for the model (including all months and not January only).

## Results
### Delays
There is a wide variety of reasons for a flight to be delayed. Three main categories stood out: late aircraft, carrier, and National Air System.

![image](https://github.com/karlarochaes/flight-delay-cancellation-USA-2023/assets/88100992/e537c8e2-b971-4e68-9e83-51393e7b662d)

Additionally, there was a significant correlation between delay during departure and delay during arrival.

![image](https://github.com/karlarochaes/flight-delay-cancellation-USA-2023/assets/88100992/02f98cf6-69ba-46e9-a3c6-f4cb21b37540)

### Cancellations
The primary cause of flight cancellations was adverse weather conditions.

![image](https://github.com/karlarochaes/flight-delay-cancellation-USA-2023/assets/88100992/d46d09c4-25ad-4c96-aece-cc4041e20ba1)

### Airlines
Frontier Airlines Inc. was the airline with the highest numbers of delays and cancellations.

### Routes
There are routes with 100% of delay or cancellation. This happens because in these routes there is only one programmed flight. This is the case for routes like AZA-LAS, IAD-SYR, and LAS-AZA.

## Visualization
See the final dashboard in [this link](https://lookerstudio.google.com/reporting/7814ffd9-47a6-4cc1-b60f-2c6231ed4855/page/p_5to251aaed)

![Captura de pantalla (312)](https://github.com/karlarochaes/flight-delay-cancellation-USA-2023/assets/88100992/234db5d4-1304-4d55-adb0-10d26067b617)

## Conclusions and Recommendations
- If a flight is delayed during departure, it is highly probable that it will be delayed during arrival.
- If it is possible, clients should try flying on Saturdays. On average, this is the day with the fewest delayed flights.
- Clients should avoid traveling with Frontier Airlines Inc., as this was the airline with the most delays and cancellations.
- As the majority of cancellations occurred towards the end of the month and were primarily caused by climate conditions, clients should consider climate forecasting before buying their tickets.
