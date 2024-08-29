# Weather-Data-Analyzer
pip install requests matplotlib
import requests
import matplotlib.pyplot as plt
import datetime

API_KEY = 'din_openweathermap_api_key'

def get_weather_data(city, start_date, end_date):
    url = f"http://api.openweathermap.org/data/2.5/onecall/timemachine"
    lat, lon = get_city_coordinates(city)
    data = []
    for date in date_range(start_date, end_date):
        params = {
            'lat': lat,
            'lon': lon,
            'dt': int(date.timestamp()),
            'appid': API_KEY,
            'units': 'metric'
        }
        response = requests.get(url, params=params)
        data.append(response.json())
    return data

def get_city_coordinates(city):
    geo_url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}"
    response = requests.get(geo_url).json()
    return response['coord']['lat'], response['coord']['lon']

def date_range(start_date, end_date):
    for n in range(int((end_date - start_date).days) + 1):
        yield start_date + datetime.timedelta(n)

def analyze_weather_data(data):
    temperatures = []
    dates = []
    for day_data in data:
        for hour in day_data['hourly']:
            temperatures.append(hour['temp'])
            dates.append(datetime.datetime.fromtimestamp(hour['dt']))
    return dates, temperatures

def plot_temperature(dates, temperatures, city):
    plt.figure(figsize=(10, 5))
    plt.plot(dates, temperatures, label='Temperature')
    plt.xlabel('Date')
    plt.ylabel('Temperature (°C)')
    plt.title(f'Temperature Over Time in {city}')
    plt.legend()
    plt.show()

def main():
    city = input("Enter the city: ")
    start_date = datetime.datetime.strptime(input("Enter start date (YYYY-MM-DD): "), '%Y-%m-%d')
    end_date = datetime.datetime.strptime(input("Enter end date (YYYY-MM-DD): "), '%Y-%m-%d')

    print(f"Fetching weather data for {city} from {start_date} to {end_date}...")
    data = get_weather_data(city, start_date, end_date)
    dates, temperatures = analyze_weather_data(data)
    
    print("Plotting data...")
    plot_temperature(dates, temperatures, city)

if __name__ == "__main__":
    main()
#README.md (eksempel):

## Beskrivelse
Weather Data Analyzer er et Python-prosjekt som henter, analyserer og visualiserer historiske værdata for en spesifisert by. Prosjektet bruker OpenWeatherMap API for å hente værdata.

## Funksjoner
- Henter historiske værdata for en spesifisert by og tidsperiode.
- Analyserer temperaturtrender over tid.
- Visualiserer dataene med grafikk.

## Installasjon
```bash
pip install requests matplotlib
