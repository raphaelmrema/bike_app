# Bike-Bus-Bike Route Planner

A multimodal transportation route planner that combines bicycling, public transit, and real-time GTFS data to provide comprehensive routing options.

## Features

- 🚴 Google Maps Bicycling API for accurate bike routing
- 🚌 Google Transit API for real-time bus schedules
- 🗺️ GeoPandas-based bike infrastructure analysis
- 🛡️ Safety scoring based on bike facility types
- 📊 Real-time GTFS data integration
- 🎯 Smart transit fallback for short bike segments

## Deployment to Railway

### Prerequisites

1. GitHub account
2. Railway account (sign up at [railway.app](https://railway.app))
3. Google Maps API key with the following APIs enabled:
   - Maps JavaScript API
   - Directions API
   - Geocoding API

### Step 1: Prepare Your Repository

1. Create a new GitHub repository
2. Clone it to your local machine:
   ```bash
   git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
   cd YOUR_REPO_NAME
   ```

3. Copy these files to your repository:
   - `GOOGLE_API6.py` (your main application file)
   - `requirements.txt`
   - `runtime.txt`
   - `Procfile`
   - `railway.json`
   - `.gitignore`
   - `README.md`

### Step 2: Configure Environment Variables

Before deploying, you need to set up your configuration. There are two approaches:

#### Option A: Use Environment Variables (Recommended for Production)

1. Modify `GOOGLE_API6.py` to read from environment variables:
   ```python
   import os
   
   # Replace these lines in the configuration section:
   GOOGLE_API_KEY = os.environ.get('GOOGLE_API_KEY', 'YOUR_KEY_HERE')
   ROADS_SHAPEFILE = os.environ.get('ROADS_SHAPEFILE', '/app/data/roads.shp')
   TRANSIT_STOPS_SHAPEFILE = os.environ.get('TRANSIT_STOPS_SHAPEFILE', '/app/data/transit_stops.shp')
   ```

2. In Railway, set these environment variables:
   - `GOOGLE_API_KEY` = your actual API key
   - `PORT` = 8000 (Railway will override this)

#### Option B: Hardcode Values (Quick Start)

Simply update the values in `GOOGLE_API6.py`:
```python
GOOGLE_API_KEY = "your_actual_api_key_here"
ROADS_SHAPEFILE = "/app/data/roads.shp"  # If using shapefiles
TRANSIT_STOPS_SHAPEFILE = "/app/data/transit_stops.shp"  # If using shapefiles
```

### Step 3: Handle Shapefiles

Since shapefiles are large, you have several options:

#### Option 1: Use a Cloud Storage Service
Store your shapefiles on Google Cloud Storage, AWS S3, or similar, and download them at runtime.

#### Option 2: Include in Repository
If files are small enough (<100MB), add them to your repo:
```bash
mkdir data
cp /path/to/roads.shp data/
cp /path/to/roads.shx data/
cp /path/to/roads.dbf data/
cp /path/to/transit_stops.* data/
```

#### Option 3: Remove Shapefile Dependencies
If you don't need the bike infrastructure analysis, comment out or modify the shapefile-related code.

### Step 4: Update Server Configuration

Modify the server initialization in `GOOGLE_API6.py` to use Railway's PORT:

```python
def run_bike_bus_bike_google_server():
    try:
        # Get port from environment or use default
        PORT = int(os.environ.get('PORT', 8000))
        
        # Change binding from localhost to 0.0.0.0 for Railway
        server = socketserver.TCPServer(("0.0.0.0", PORT), handler)
        
        print(f"\nServer running on port {PORT}")
        
        # Don't auto-open browser in production
        # Comment out or remove this line:
        # threading.Timer(1.5, lambda: webbrowser.open(f'http://localhost:{PORT}')).start()
        
        server.serve_forever()
```

### Step 5: Push to GitHub

```bash
git add .
git commit -m "Initial commit for Railway deployment"
git push origin main
```

### Step 6: Deploy on Railway

1. Go to [railway.app](https://railway.app) and sign in
2. Click "New Project"
3. Select "Deploy from GitHub repo"
4. Choose your repository
5. Railway will auto-detect the Python project and deploy

### Step 7: Configure Railway Environment Variables

1. In your Railway project dashboard, go to "Variables"
2. Add your environment variables:
   - `GOOGLE_API_KEY` = your API key
   - Any other necessary configuration

### Step 8: Access Your Application

1. Once deployed, Railway will provide a URL (e.g., `https://your-app.railway.app`)
2. Click the URL to access your bike-bus-bike route planner

## Configuration Options

### API Keys

You need a Google Maps API key with these APIs enabled:
- Directions API
- Geocoding API
- Maps JavaScript API (if using embedded maps)

### Bike Speed

Adjust the average bike speed in the configuration:
```python
BIKE_SPEED_MPH = 11  # Average bicycle speed
```

### GTFS Data Sources

The application attempts to load GTFS data from multiple sources. You can customize these in the `EnhancedGTFSManager.load_gtfs_data()` method.

## Troubleshooting

### Port Binding Issues
Railway provides the PORT environment variable. Make sure your app uses it:
```python
PORT = int(os.environ.get('PORT', 8000))
server = socketserver.TCPServer(("0.0.0.0", PORT), handler)
```

### Build Failures
Check Railway logs for specific errors:
- Missing dependencies → Update `requirements.txt`
- Python version issues → Update `runtime.txt`
- Import errors → Check all imports are in `requirements.txt`

### Shapefile Issues
If shapefiles aren't loading:
- Verify file paths are correct
- Check file permissions
- Consider using cloud storage for large files

### API Issues
If routing fails:
- Verify API key is correct
- Check API quotas in Google Cloud Console
- Ensure required APIs are enabled

## Local Development

To run locally:

```bash
# Install dependencies
pip install -r requirements.txt

# Run the application
python GOOGLE_API6.py
```

Then open `http://localhost:8000` in your browser.

## Technologies Used

- **Python 3.11**
- **GeoPandas** - Spatial data analysis
- **Shapely** - Geometric operations
- **Pandas** - Data manipulation
- **Google Maps APIs** - Routing and geocoding
- **Leaflet.js** - Interactive mapping
- **GTFS** - Real-time transit data

## License

This project is open source and available under the MIT License.

## Support

For issues and questions:
- Check the Railway logs for deployment issues
- Review Google Maps API documentation
- Check GTFS feed validity

## Roadmap

- [ ] Add support for multiple cities
- [ ] Implement route preferences (safest, fastest, etc.)
- [ ] Add elevation profile for bike routes
- [ ] Mobile responsive design improvements
- [ ] User accounts and saved routes
- [ ] Integration with more transit agencies

---

Built with ❤️ for sustainable urban mobility
