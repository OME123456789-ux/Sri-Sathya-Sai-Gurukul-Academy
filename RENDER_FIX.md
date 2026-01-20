# Fix for Render Deployment Error

## The Problem
The error `lstat /opt/render/project/src/backend/music/backend: no such file or directory` occurs because Render doesn't know the correct root directory.

## Solution

You have two options:

### Option 1: Use render.yaml (Recommended)
1. **Commit and push** the `render.yaml` file I just created at the root of your repository
2. **In Render Dashboard**:
   - Go to your service settings
   - Scroll to "Infrastructure as Code"
   - Click "Apply render.yaml" or redeploy
   - Render will automatically use the `rootDir: backend/music` setting

### Option 2: Manual Configuration
If you prefer to configure manually in Render dashboard:

1. **Go to your service** in Render dashboard
2. **Click "Settings"** tab
3. **Find "Root Directory"** field
4. **Set it to**: `backend/music`
5. **Save changes**
6. **Redeploy** the service

## After Fixing

1. **Commit and push** the changes:
   ```bash
   git add render.yaml
   git commit -m "Add render.yaml with correct root directory"
   git push
   ```

2. **Redeploy** in Render (or it will auto-deploy if auto-deploy is enabled)

3. **Set Environment Variables** in Render dashboard:
   - `DATABASE_URL`: Your MySQL connection string
   - `DATABASE_USERNAME`: Your database username  
   - `DATABASE_PASSWORD`: Your database password
   - `JAVA_VERSION`: 21
   - `SPRING_PROFILES_ACTIVE`: production
   - `LOG_LEVEL`: INFO

The deployment should now work correctly!


