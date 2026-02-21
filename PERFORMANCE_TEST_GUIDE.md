# Performance Testing Guide

## How to Test App Speed

I've added performance logging to the app. Here's how to check if data fetching is normal:

### 1. Run the App in Debug Mode

```bash
cd /Users/Gracegold/Desktop/Ace\ App/ace_mall_app
flutter run
```

### 2. Watch the Console Output

When you navigate through the app, you'll see performance logs like:

```
⚡ PERFORMANCE [API CALL] getAllStaff: 1250ms
⚡ PERFORMANCE [CACHE HIT] getAllStaff: 5ms
⚡ PERFORMANCE [API CALL] getBranches: 450ms
⚡ PERFORMANCE [CACHE HIT] getBranches: 2ms
```

### 3. Performance Benchmarks

**Normal Speed (Good):**
- ✅ **Branches/Departments/Roles**: 200-800ms (first load), <10ms (cached)
- ✅ **Staff List**: 800-2000ms (first load), <20ms (cached)
- ✅ **Dashboard**: 1000-2500ms (first load), <50ms (cached)

**Slow Speed (Needs Investigation):**
- ⚠️ **Branches/Departments/Roles**: >1500ms (first load)
- ⚠️ **Staff List**: >3000ms (first load)
- ⚠️ **Dashboard**: >4000ms (first load)

**Very Slow (Problem):**
- ❌ **Any API call**: >5000ms
- ❌ **Timeout errors**: 30000ms

### 4. What to Look For

**First Time Loading (API CALL):**
- Should see `[API CALL]` in logs
- Takes longer (network + database query)
- Normal: 500-2500ms depending on data size

**Subsequent Loads (CACHE HIT):**
- Should see `[CACHE HIT]` in logs
- Very fast (<50ms)
- If you don't see cache hits, caching isn't working

### 5. Test These Pages

1. **Staff Oversight** (CEO/HR) - Tests `getAllStaff`
2. **Manage Departments** - Tests `getDepartments` + staff loading
3. **Staff Promotion** - Tests `getRoles` + `getBranches`
4. **Dashboard** - Tests multiple API calls

### 6. Common Issues & Solutions

**Issue: All requests are slow (>3000ms)**
- **Cause**: Backend server cold start (Render free tier)
- **Solution**: Wait 30-60 seconds, server will warm up

**Issue: No cache hits, always API calls**
- **Cause**: Cache expiry too short or app restarting
- **Solution**: Normal - cache expires after 5 minutes for staff, 1 hour for others

**Issue: Timeout errors (30000ms)**
- **Cause**: Backend server down or network issues
- **Solution**: Check backend status, check internet connection

### 7. Report Format

When testing, note:
- Page visited
- First load time (ms)
- Second load time (ms)
- Cache hit or API call
- Any errors

Example:
```
Staff Oversight Page:
- First load: ⚡ PERFORMANCE [API CALL] getAllStaff: 1450ms ✅
- Second load: ⚡ PERFORMANCE [CACHE HIT] getAllStaff: 8ms ✅
- Status: NORMAL
```

### 8. Expected Results

With the caching I just added:
- **First visit**: Slower (API calls to backend)
- **Repeat visits**: Much faster (data from cache)
- **After 5 minutes**: Fresh API call again (cache expired)

This is normal and optimal behavior!
