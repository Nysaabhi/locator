<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>India Public Places Locator</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/MarkerCluster.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/MarkerCluster.Default.css" />
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" />
    <style>
        :root {
            --primary-color: #FF6B6B;
            --secondary-color: #4ECDC4;
            --dark-color: #2C3E50;
            --light-color: #ECF0F1;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: var(--light-color);
        }

        #app-container {
            display: flex;
            height: 100vh;
        }

        #sidebar {
            width: 400px;
            background: white;
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
            z-index: 1000;
            display: flex;
            flex-direction: column;
        }

        #map {
            flex: 1;
            height: 100%;
        }

        .header {
            padding: 20px;
            background: var(--dark-color);
            color: white;
        }

        .header h1 {
            font-size: 1.5rem;
            margin-bottom: 10px;
        }

        .search-container {
            padding: 20px;
            border-bottom: 1px solid #eee;
        }

        .search-box {
            position: relative;
            margin-bottom: 15px;
        }

        .search-box input {
            width: 100%;
            padding: 12px 40px 12px 15px;
            border: 2px solid #eee;
            border-radius: 8px;
            font-size: 1rem;
            transition: all 0.3s;
        }

        .search-box input:focus {
            border-color: var(--primary-color);
            outline: none;
        }

        .search-box i {
            position: absolute;
            right: 15px;
            top: 50%;
            transform: translateY(-50%);
            color: #999;
        }

        .filters {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        .filter-btn {
            padding: 8px 15px;
            border: none;
            border-radius: 20px;
            background: #eee;
            color: var(--dark-color);
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 5px;
        }

        .filter-btn.active {
            background: var(--primary-color);
            color: white;
        }

        .results {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
        }

        .place-card {
            background: white;
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 15px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            cursor: pointer;
            transition: all 0.3s;
        }

        .place-card:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.15);
        }

        .place-type {
            font-size: 0.8rem;
            color: var(--primary-color);
            text-transform: uppercase;
            margin-bottom: 5px;
        }

        .place-name {
            font-size: 1.1rem;
            font-weight: 600;
            margin-bottom: 5px;
        }

        .place-address {
            font-size: 0.9rem;
            color: #666;
            margin-bottom: 10px;
        }

        .place-meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 0.8rem;
            color: #999;
        }

        .custom-marker {
            width: 30px;
            height: 30px;
            border-radius: 50%;
            background: white;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }

        .marker-railway { color: #3498db; }
        .marker-bus { color: #2ecc71; }
        .marker-police { color: #e74c3c; }
        .marker-public { color: #f1c40f; }

        .location-btn {
            position: fixed;
            bottom: 20px;
            right: 20px;
            width: 50px;
            height: 50px;
            border-radius: 50%;
            background: var(--primary-color);
            color: white;
            border: none;
            box-shadow: 0 2px 10px rgba(0,0,0,0.2);
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            transition: all 0.3s;
            z-index: 1000;
        }

        .location-btn:hover {
            transform: scale(1.1);
        }

        .suggestions {
            position: absolute;
            top: 100%;
            left: 0;
            right: 0;
            background: white;
            border: 1px solid #eee;
            border-radius: 8px;
            margin-top: 5px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            z-index: 1000;
            max-height: 300px;
            overflow-y: auto;
        }

        .suggestion-item {
            padding: 10px 15px;
            display: flex;
            align-items: center;
            gap: 10px;
            cursor: pointer;
            transition: background 0.3s;
        }

        .suggestion-item:hover {
            background: #f5f5f5;
        }

        .suggestion-item i {
            color: var(--primary-color);
        }

        .distance-badge {
            background: var(--primary-color);
            color: white;
            padding: 2px 8px;
            border-radius: 12px;
            font-size: 0.8rem;
            margin-left: auto;
        }

        .loading {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 15px 30px;
            border-radius: 25px;
            z-index: 2000;
        }
    </style>
</head>
<body>
    <div id="app-container">
        <div id="sidebar">
            <div class="header">
                <h1>India Public Places Locator</h1>
                <p>Find nearby public facilities across India</p>
            </div>
            <div class="search-container">
                <div class="search-box">
                    <input type="text" id="search-input" placeholder="Search by city or place name...">
                    <i class="fas fa-search"></i>
                </div>
                <div class="filters">
                    <button class="filter-btn active" data-type="all">
                        <i class="fas fa-globe"></i> All
                    </button>
                    <button class="filter-btn" data-type="railway">
                        <i class="fas fa-train"></i> Railway
                    </button>
                    <button class="filter-btn" data-type="bus">
                        <i class="fas fa-bus"></i> Bus
                    </button>
                    <button class="filter-btn" data-type="police">
                        <i class="fas fa-shield-alt"></i> Police
                    </button>
                    <button class="filter-btn" data-type="public">
                        <i class="fas fa-landmark"></i> Public
                    </button>
                </div>
            </div>
            <div id="results" class="results"></div>
        </div>
        <div id="map"></div>
    </div>
    <button class="location-btn" id="location-btn" title="Get Current Location">
        <i class="fas fa-location-crosshairs"></i>
    </button>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/leaflet.markercluster.js"></script>
    <script>
        // Sample database structure for places
        const placesDB = {
            delhi: {
                railway: [
                    { id: 1, name: "New Delhi Railway Station", type: "railway", lat: 28.6419, lng: 77.2193, address: "Paharganj, New Delhi", city: "Delhi" },
                    { id: 2, name: "Old Delhi Railway Station", type: "railway", lat: 28.6577, lng: 77.2273, address: "Chandni Chowk, Delhi", city: "Delhi" }
                ],
                bus: [
                    { id: 3, name: "Kashmere Gate ISBT", type: "bus", lat: 28.6673, lng: 77.2285, address: "Kashmere Gate, Delhi", city: "Delhi" },
                    { id: 4, name: "Anand Vihar ISBT", type: "bus", lat: 28.6469, lng: 77.3164, address: "Anand Vihar, Delhi", city: "Delhi" }
                ],
                police: [
                    { id: 5, name: "Parliament Street Police Station", type: "police", lat: 28.6228, lng: 77.2147, address: "Sansad Marg, New Delhi", city: "Delhi" }
                ],
                public: [
                    { id: 6, name: "India Gate", type: "public", lat: 28.6129, lng: 77.2295, address: "Rajpath, New Delhi", city: "Delhi" }
                ]
            },
            mumbai: {
                railway: [
                    { id: 7, name: "Chhatrapati Shivaji Terminus", type: "railway", lat: 18.9398, lng: 72.8355, address: "Fort, Mumbai", city: "Mumbai" }
                ],
                bus: [
                    { id: 8, name: "Mumbai Central Bus Depot", type: "bus", lat: 18.9692, lng: 72.8195, address: "Mumbai Central, Mumbai", city: "Mumbai" }
                ],
                police: [
                    { id: 9, name: "Marine Drive Police Station", type: "police", lat: 18.9438, lng: 72.8245, address: "Marine Drive, Mumbai", city: "Mumbai" }
                ],
                public: [
                    { id: 10, name: "Gateway of India", type: "public", lat: 18.9217, lng: 72.8347, address: "Apollo Bandar, Mumbai", city: "Mumbai" }
                ]
            }
        };

        // Initialize map
        const map = L.map('map').setView([20.5937, 78.9629], 5);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '© OpenStreetMap contributors'
        }).addTo(map);

        // Initialize marker cluster group
        const markerClusterGroup = L.markerClusterGroup();
        map.addLayer(markerClusterGroup);

        let userMarker = null;
        let userLocation = null;

        // Create marker icons
        const createMarkerIcon = (type) => {
            return L.divIcon({
                className: 'custom-marker',
                html: `<i class="fas ${getMarkerIcon(type)} marker-${type}"></i>`,
                iconSize: [30, 30],
                iconAnchor: [15, 15]
            });
        };

        function getMarkerIcon(type) {
            switch(type) {
                case 'railway': return 'fa-train';
                case 'bus': return 'fa-bus';
                case 'police': return 'fa-shield-alt';
                case 'public': return 'fa-landmark';
                default: return 'fa-map-marker-alt';
            }
        }

        // Display places on map and sidebar
        function displayPlaces(places) {
            markerClusterGroup.clearLayers();
            const resultsContainer = document.getElementById('results');
            resultsContainer.innerHTML = '';

            if (userLocation) {
                places = sortPlacesByDistance(places, userLocation.lat, userLocation.lng);
            }

            places.forEach(place => {
                const marker = L.marker([place.lat, place.lng], {
                    icon: createMarkerIcon(place.type)
                }).bindPopup(`
                    <strong>${place.name}</strong><br>
                    ${place.address}<br>
                    <small>${place.city}</small>
                `);
                
                markerClusterGroup.addLayer(marker);

                const distanceHtml = userLocation ? 
                    `<span class="distance-badge">${formatDistance(calculateDistance(userLocation.lat, userLocation.lng, place.lat, place.lng))}</span>` : '';

                const card = document.createElement('div');
                card.className = 'place-card';
                card.innerHTML = `
                    <div class="place-type">${place.type}</div>
                    <div class="place-name">${place.name}</div>
                    <div class="place-address">${place.address}</div>
                    <div class="place-meta">
                        <span>${place.city}</span>
                        <span><i class="fas fa-map-marker-alt"></i> Show on map</span>
                        ${distanceHtml}
                    </div>
                `;

                card.addEventListener('click', () => {
                    map.setView([place.lat, place.lng], 16);
                    const popup = L.popup()
                        .setLatLng([place.lat, place.lng])
                        .setContent(`
                            <strong>${place.name}</strong><br>
                            ${place.address}<br>
                            <small>${place.city}</small>
                        `)
                        .openOn(map);
                });

                resultsContainer.appendChild(card);
            });

            if (places.length > 0) {
                const bounds = L.featureGroup(Array.from(markerClusterGroup.getLayers())).getBounds();
                map.fitBounds(bounds.pad(0.1));
            }
        }

        // Get all places from database
        function getAllPlaces() {
            const places = [];
            Object.values(placesDB).forEach(city => {
                Object.values(city).forEach(typePlaces => {
                    places.push(...typePlaces);
                });
            });
            return places;
        }

        // Calculate distance between points
        function calculateDistance(lat1, lon1, lat2, lon2) {
            const R = 6371; // Earth's radius in km
            const dLat = (lat2 - lat1) * Math.PI / 180;
            const dLon = (lon2 - lon1) * Math.PI / 180;
            const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                    Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
                    Math.sin(dLon/2) * Math.sin(dLon/2);
            const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
            return R * c;
        }

        // Format distance for display
        function formatDistance(km) {
            if (km < 1) {
                return `${(km * 1000).toFixed(0)}m`;
            }
            return `${km.toFixed(1)}km`;
        }

        // Sort places by distance
        function sortPlacesByDistance(places, userLat, userLng) {
            return places.sort((a, b) => {
                const distA = calculateDistance(userLat, userLng, a.lat, a.lng);
                const distB = calculateDistance(userLat, userLng, b.lat, b.lng);
                return distA - distB;
            });
        }

        // Debounce function for search
        function debounce(func, wait) {
            let timeout;
            return function executedFunction(...args) {
                const later = () => {
                    clearTimeout(timeout);
                    func(...args);
                };
                clearTimeout(timeout);
                timeout = setTimeout(later, wait);
            };
        }

        // Update user location
        function updateUserLocation(latitude, longitude) {
            userLocation = { lat: latitude, lng: longitude };
            
            if (userMarker) {
                map.removeLayer(userMarker);
            }
            
            userMarker = L.marker([latitude, longitude], {
                icon: L.divIcon({
                    className: 'custom-marker',
                    html: '<i class="fas fa-user-circle" style="color: var(--primary-color);"></i>',
                    iconSize: [30, 30],
                    iconAnchor: [15, 15]
                })
            }).addTo(map);

            // Refresh display with new distances
            const activeFilter = document.querySelector('.filter-btn.active');
            const type = activeFilter.dataset.type;
            const allPlaces = getAllPlaces();
            const filteredPlaces = type === 'all' ? allPlaces : allPlaces.filter(place => place.type === type);
            displayPlaces(filteredPlaces);
        }

        // Show loading indicator
        function showLoading() {
            const loading = document.createElement('div');
            loading.className = 'loading';
            loading.id = 'loading';
            loading.innerHTML = '<i class="fas fa-spinner fa-spin"></i> Loading...';
            document.body.appendChild(loading);
        }

        // Hide loading indicator
        function hideLoading() {
            const loading = document.getElementById('loading');
            if (loading) {
                loading.remove();
            }
        }

        // Set up event listeners
        function setupEventListeners() {
            // Search functionality
            const searchInput = document.getElementById('search-input');
            const suggestions = document.createElement('div');
            suggestions.className = 'suggestions';
            searchInput.parentNode.appendChild(suggestions);

            searchInput.addEventListener('input', debounce((e) => {
                const value = e.target.value.toLowerCase();
                suggestions.innerHTML = '';
                
                if (value.length < 2) return;

                const allPlaces = getAllPlaces();
                const matches = allPlaces
                    .filter(place => 
                        place.name.toLowerCase().includes(value) ||
                        place.city.toLowerCase().includes(value))
                    .slice(0, 5);

                if (matches.length > 0) {
                    matches.forEach(place => {
                        const distanceText = userLocation ? 
                            formatDistance(calculateDistance(userLocation.lat, userLocation.lng, place.lat, place.lng)) : '';
                        
                        const div = document.createElement('div');
                        div.className = 'suggestion-item';
                        div.innerHTML = `
                            <i class="fas ${getMarkerIcon(place.type)}"></i>
                            <div>
                                <div>${place.name}</div>
                                <small>${place.city}</small>
                            </div>
                            ${distanceText ? `<span class="distance-badge">${distanceText}</span>` : ''}
                        `;
                        
                        div.addEventListener('click', () => {
                            searchInput.value = place.name;
                            suggestions.innerHTML = '';
                            map.setView([place.lat, place.lng], 16);
                            displayPlaces([place]);
                        });
                        
                        suggestions.appendChild(div);
                    });
                }
            }, 300));

            // Filter buttons
            document.querySelectorAll('.filter-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
                    btn.classList.add('active');

                    const type = btn.dataset.type;
                    const allPlaces = getAllPlaces();
                    const filteredPlaces = type === 'all' ? 
                        allPlaces : 
                        allPlaces.filter(place => place.type === type);
                    displayPlaces(filteredPlaces);
                });
            });

            // Location button
            document.getElementById('location-btn').addEventListener('click', () => {
                if ("geolocation" in navigator) {
                    showLoading();
                    navigator.geolocation.getCurrentPosition(
                        position => {
                            const { latitude, longitude } = position.coords;
                            updateUserLocation(latitude, longitude);
                            map.setView([latitude, longitude], 13);
                            hideLoading();
                        },
                        error => {
                            hideLoading();
                            alert('Error getting your location. Please check your GPS settings.');
                        }
                    );
                } else {
                    alert('Geolocation is not supported by your browser');
                }
            });

            // Close suggestions when clicking outside
            document.addEventListener('click', (e) => {
                if (!e.target.closest('.search-box')) {
                    suggestions.innerHTML = '';
                }
            });
        }

        // Initialize application
        function initApp() {
            const allPlaces = getAllPlaces();
            displayPlaces(allPlaces);
            setupEventListeners();

            // Check for user location on startup
            if ("geolocation" in navigator) {
                navigator.geolocation.getCurrentPosition(
                    position => {
                        const { latitude, longitude } = position.coords;
                        updateUserLocation(latitude, longitude);
                    },
                    error => {
                        console.log('Error getting initial location:', error);
                    }
                );
            }
        }

        // Start the application
        initApp();
    </script>
</body>
</html>
