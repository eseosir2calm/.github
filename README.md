// ... Import statements
import React, { useState, useEffect } from 'react';
import axios from 'axios';
import L from 'leaflet';
import { GoogleMap, LoadScript, Marker } from '@react-google-maps/api';

const AppWithMap = () => {
  const [weather, setWeather] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const openWeatherMapApiKey = process.env.REACT_APP_OPENWEATHERMAP_API_KEY;
    const googleMapsApiKey = process.env.REACT_APP_GOOGLE_MAPS_API_KEY;

    const initializeMap = () => {
      const map = new window.google.maps.Map(document.getElementById('map'), {
        center: { lat: 51.5074, lng: -0.1276 },
        zoom: 13,
      });

      // Add a marker for the starting point
      new window.google.maps.Marker({
        position: { lat: 51.5074, lng: -0.1276 },
        map,
        title: 'Starting Point',
      });
    };

    const fetchWeatherData = async () => {
      const start = [51.5074, -0.1276];
      try {
        const response = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${start[0]}&lon=${start[1]}&appid=${openWeatherMapApiKey}`
        );
        setWeather(response.data);
      } catch (error) {
        setError(error.message);
      }
    };

    const fetchData = async () => {
      try {
        await Promise.all([fetchWeatherData()]);
      } catch (error) {
        setError(error.message);
      } finally {
        setLoading(false);
      }
    };

    initializeMap();
    fetchData();
  }, []);

  const renderWeather = () => {
    return (
      <div>
        <h2>Current Weather</h2>
        <p>Temperature: {weather.main.temp} K</p>
        <p>Weather: {weather.weather[0].description}</p>
      </div>
    );
  };

  return (
    <div>
      <h1>Weather Map App</h1>
      {loading && <p>Loading weather data...</p>}
      {error && <p>Error: {error}</p>}
      {weather && renderWeather()}
      <div id="map" style={{ height: '400px' }}></div>
    </div>
  );
};

export default AppWithMap;
