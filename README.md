# Real-Time Weather Data Processing System with Rollups and Aggregates

import requests
import time

# Fetch weather data from OpenWeatherMap API
def fetch_weather(city, api_key):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        print(f"Error fetching data for {city}: {response.status_code}")
        return None

# Convert temperature from Kelvin to Celsius or Fahrenheit
def convert_temp(kelvin, unit="C"):
    if unit == "C":
        return kelvin - 273.15
    elif unit == "F":
        return (kelvin - 273.15) * 9/5 + 32

# Rollup data for daily aggregates
def daily_summary(temps, conditions):
    avg_temp = sum(temps) / len(temps)
    max_temp = max(temps)
    min_temp = min(temps)
    dominant_condition = max(set(conditions), key=conditions.count)
    
return {
        "average_temperature": avg_temp,
        "maximum_temperature": max_temp,
        "minimum_temperature": min_temp,
        "dominant_condition": dominant_condition
    }

# Check if temperature exceeds a threshold
def check_alerts(temp_celsius, threshold):
    if temp_celsius > threshold:
        print(f"Alert! Temperature has exceeded {threshold}°C")
        return True
    return False

if __name__ == "__main__":
    api_key = "your_openweathermap_api_key"
    cities = ["Delhi", "Mumbai", "Chennai", "Bangalore", "Kolkata", "Hyderabad"]
    temps = []  # Store temperatures for the day
    conditions = []  # Store weather conditions for the day
    threshold = 35  # Alert threshold in Celsius
     while True:
        for city in cities:
            weather_data = fetch_weather(city, api_key)
            if weather_data:
                temp_kelvin = weather_data['main']['temp']
                temp_celsius = convert_temp(temp_kelvin, "C")
                condition = weather_data['weather'][0]['main']
                 print(f"City: {city}, Temp: {temp_celsius:.2f}°C, Condition: {condition}")
       # Store data for rollups
                temps.append(temp_celsius)
                conditions.append(condition)
                # Check for alert
                check_alerts(temp_celsius, threshold)
        # Simulate fetching data every 5 minutes
        time.sleep(300)
        # At the end of the day (simulated after the loop runs for some time)
        if len(temps) >= 24:  # Example: assuming we run this every hour for 24 updates
            summary = daily_summary(temps, conditions)
            print("Daily Summary:", summary)
        # Reset data for next day
            temps.clear()
            conditions.clear()
