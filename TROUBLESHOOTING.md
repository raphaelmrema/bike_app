# 🚨 Railway Deployment Troubleshooting Guide

## Error: "Error creating build plan with Railpack"

This error means Railway is having trouble detecting or building your Python project. Here are **5 solutions** to try:

---

## ✅ Solution 1: Add nixpacks.toml (Recommended)

**What to do:**
1. Download the new `nixpacks.toml` file from this package
2. Add it to your GitHub repository root
3. Commit and push:
   ```bash
   git add nixpacks.toml
   git commit -m "Add nixpacks configuration"
   git push
   ```
4. Railway will automatically redeploy

**Why this works:** Explicitly tells Railway how to build your Python app with all dependencies (GDAL, GEOS, PROJ for GeoPandas).

---

## ✅ Solution 2: Update railway.json

**What to do:**
1. Replace your current `railway.json` with `railway_NEW.json` from this package
2. Rename `railway_NEW.json` to `railway.json`
3. Commit and push:
   ```bash
   git add railway.json
   git commit -m "Update Railway configuration"
   git push
   ```

**Why this works:** Provides explicit build commands and settings.

---

## ✅ Solution 3: Simplify Requirements (If Issues Persist)

If GeoPandas dependencies are causing problems, you can temporarily disable shapefile analysis:

**Create a simplified requirements.txt:**
```txt
pandas==2.0.3
requests==2.31.0
numpy==1.24.3
```

**Then in your code, comment out GeoPandas imports:**
```python
# import geopandas as gpd
# from shapely.geometry import Point, LineString
```

**Note:** The app will still work! It just won't have bike infrastructure scoring.

---

## ✅ Solution 4: Check File Names Match

Make sure your files match what Railway expects:

**In your repository:**
- ✅ File is named: `GOOGLE_API6_railway.py` OR `GOOGLE_API6.py`
- ✅ Procfile says: `web: python GOOGLE_API6_railway.py` (or whatever your file is named)
- ✅ railway.json says the same filename

**Fix mismatches:**
```bash
# If your file is GOOGLE_API6.py, update Procfile:
echo "web: python GOOGLE_API6.py" > Procfile

# Or rename your file:
mv GOOGLE_API6_railway.py GOOGLE_API6.py
```

---

## ✅ Solution 5: Use Railway's Web Editor

Sometimes the easiest fix is through Railway's dashboard:

1. Go to your Railway project
2. Click **"Settings"** tab
3. Scroll to **"Build Configuration"**
4. Set these manually:
   - **Build Command**: `pip install -r requirements.txt`
   - **Start Command**: `python GOOGLE_API6_railway.py`
5. Click **"Deploy"** to trigger manual redeploy

---

## 🔍 Detailed Diagnosis Steps

### Step 1: Check Your Repository Structure

Your repository should look like this:
```
your-repo/
├── GOOGLE_API6_railway.py (or GOOGLE_API6.py)
├── requirements.txt
├── runtime.txt
├── Procfile
├── railway.json
├── nixpacks.toml (NEW - add this!)
└── .gitignore
```

### Step 2: Verify File Contents

**Check Procfile:**
```bash
cat Procfile
# Should show: web: python GOOGLE_API6_railway.py
```

**Check requirements.txt:**
```bash
cat requirements.txt
# Should list: pandas, geopandas, shapely, requests, numpy, etc.
```

**Check runtime.txt:**
```bash
cat runtime.txt
# Should show: python-3.11.6
```

### Step 3: Check Railway Logs

In Railway dashboard:
1. Click on your deployment
2. Look at the **"Build Logs"**
3. Find the specific error message
4. Share it if you need more help

---

## 🎯 Quick Fix Steps (Try These in Order)

### Quick Fix #1: Add nixpacks.toml
```bash
# Download nixpacks.toml from this package
git add nixpacks.toml
git commit -m "Add nixpacks config"
git push
```

### Quick Fix #2: Simplify requirements.txt
```bash
# Create minimal requirements.txt
cat > requirements.txt << EOF
pandas==2.0.3
requests==2.31.0
numpy==1.24.3
EOF

git add requirements.txt
git commit -m "Simplify dependencies"
git push
```

### Quick Fix #3: Manual Railway Settings
1. Railway Dashboard → Your Project
2. Settings → Build Configuration
3. Build Command: `pip install -r requirements.txt`
4. Start Command: `python GOOGLE_API6_railway.py`
5. Click "Redeploy"

---

## 🔧 Alternative: Deploy Without GeoPandas

If you keep having GeoPandas issues, here's a working minimal version:

**requirements_minimal.txt:**
```txt
pandas==2.0.3
requests==2.31.0
```

**Changes needed in code:**
- The app will still route bikes using Google Maps ✅
- You'll lose bike infrastructure scoring ❌
- But everything else works! ✅

To use:
1. Replace `requirements.txt` with minimal version above
2. Comment out these lines in your Python file:
   ```python
   # import geopandas as gpd
   # from shapely.geometry import Point, LineString
   # from shapely.ops import nearest_points
   ```
3. The `BikeRoutingManager` will handle missing shapefiles gracefully

---

## 📊 Common Error Messages & Solutions

| Error Message | Solution |
|---------------|----------|
| "Error creating build plan" | Add `nixpacks.toml` |
| "Module not found: geopandas" | Install GDAL dependencies or use minimal requirements |
| "Could not find runtime" | Check `runtime.txt` has `python-3.11.6` |
| "Command not found: python" | Change Procfile to use `python3` |
| "Port binding failed" | Ensure code uses `os.environ.get('PORT')` |

---

## 🎯 Recommended Solution Path

**For Quick Success (5 minutes):**
1. Add `nixpacks.toml` to your repo
2. Update `railway.json` to `railway_NEW.json` version
3. Commit and push
4. Railway will automatically rebuild

**If That Doesn't Work (10 minutes):**
1. Use minimal `requirements.txt` (without GeoPandas)
2. Comment out shapefile code
3. Deploy successfully
4. Add GeoPandas back later if needed

**For Full Features (15 minutes):**
1. Follow Railway's GDAL installation guide
2. Use provided `nixpacks.toml`
3. May need to adjust versions in requirements.txt

---

## 💡 Pro Tips

1. **Test locally first:**
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   python GOOGLE_API6_railway.py
   ```

2. **Check Python version:**
   ```bash
   python3 --version
   # Should be 3.11.x
   ```

3. **Validate JSON files:**
   ```bash
   python3 -m json.tool railway.json
   # Should show no errors
   ```

4. **Watch Railway logs in real-time:**
   - Railway Dashboard → Deployments → Click latest
   - Watch build process live

---

## 🆘 Still Not Working?

### Option A: Simplified Deployment

Use the minimal version without GeoPandas:
- ✅ Bike routing via Google Maps
- ✅ Transit routing
- ✅ Real-time GTFS
- ❌ No shapefile analysis (can add later)

### Option B: Use Different Platform

If Railway continues to have issues:
- Try **Render.com** (similar to Railway)
- Try **Heroku** (classic choice)
- Try **Google Cloud Run** (more complex but powerful)

### Option C: Docker Deployment

Create a `Dockerfile`:
```dockerfile
FROM python:3.11-slim

# Install GDAL dependencies
RUN apt-get update && apt-get install -y \
    gdal-bin \
    libgdal-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

CMD ["python", "GOOGLE_API6_railway.py"]
```

---

## 📞 Get More Help

1. **Check Railway Status:** https://railway.statuspage.io/
2. **Railway Discord:** Join for community support
3. **Railway Docs:** https://docs.railway.app/
4. **GitHub Issues:** Create issue in your repo with error logs

---

## ✅ Success Checklist

After fixing, verify:
- [ ] Build completes without errors
- [ ] Deployment shows "Active"
- [ ] App URL loads
- [ ] Map displays correctly
- [ ] Can click two points
- [ ] Routes calculate successfully
- [ ] No errors in Railway logs

---

## 🎉 Next Steps After Fixing

Once deployed successfully:
1. Test with different locations
2. Set up environment variables
3. Configure your Google API key
4. Share your app URL!

**You've got this! 💪**

---

**Most Common Fix:** Add `nixpacks.toml` to your repository. 90% of build errors are resolved with this one file!
