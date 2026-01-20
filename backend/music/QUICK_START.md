# Quick Start Guide - Deploy Backend to Render

## Prerequisites
- Git repository with your code pushed
- Render account (free tier available)
- MySQL database (external service recommended)

## Quick Deployment Steps

### 1. Prepare Your Database

Choose one of these MySQL options:
- **PlanetScale** (Recommended - Free tier): https://planetscale.com
- **Railway** (Free tier): https://railway.app  
- **AWS RDS** (Paid)
- **Any other MySQL hosting service**

After creating your database, note down:
- Host/URL
- Port (usually 3306)
- Database name
- Username
- Password

### 2. Deploy to Render

1. **Go to Render Dashboard**: https://dashboard.render.com
2. **Click "New +" → "Web Service"**
3. **Connect your Git repository**
4. **Configure the service**:
   - **Name**: `ssga-backend` (or your choice)
   - **Root Directory**: `backend/music` (if repo root is project root)
   - **Environment**: `Java`
   - **Build Command**: `chmod +x ./mvnw && ./mvnw clean package -DskipTests`
   - **Start Command**: `java -jar target/music-0.0.1-SNAPSHOT.jar`

5. **Set Environment Variables**:
   ```
   JAVA_VERSION=21
   DATABASE_URL=jdbc:mysql://your-host:3306/your-database?useSSL=true&serverTimezone=UTC
   DATABASE_USERNAME=your-username
   DATABASE_PASSWORD=your-password
   SPRING_PROFILES_ACTIVE=production
   LOG_LEVEL=INFO
   ```

6. **Click "Create Web Service"**
7. **Wait for deployment** (5-10 minutes)

### 3. Get Your Backend URL

After deployment, Render will provide a URL like:
```
https://ssga-backend.onrender.com
```

### 4. Update Frontend (Vercel)

1. **Open** `frontend/index.html`
2. **Find** the API configuration script (near the end, before `</body>`)
3. **Update** the API_BASE_URL:
   ```javascript
   window.API_BASE_URL = 'https://ssga-backend.onrender.com';
   ```
4. **Commit and push** to trigger Vercel redeployment

### 5. Test Your Deployment

1. **Backend Test**: Visit `https://your-backend-url.onrender.com/api/enquiries/test`
   - Should return: "API is working! Backend is connected."

2. **Frontend Test**: Submit a test enquiry from your Vercel frontend
   - Should successfully submit to Render backend

## Troubleshooting

### Build Fails
- Ensure Maven wrapper (`mvnw`) exists in `backend/music/`
- Check that Java 21 is available
- Review build logs in Render dashboard

### Database Connection Fails
- Verify database allows external connections
- Check firewall rules allow Render's IPs
- Ensure SSL is enabled if required
- Verify connection string format

### CORS Errors
- Backend is configured to allow all origins (`*`)
- If issues persist, check browser console for specific errors

### Application Won't Start
- Check environment variables are set correctly
- Review application logs in Render dashboard
- Verify PORT is not manually set (Render sets it automatically)

## Important Notes

⚠️ **Free Tier Limitations**:
- Render free tier spins down after 15 minutes of inactivity
- First request after spin-down takes 30-60 seconds
- Consider upgrading for production use

💡 **Database Tips**:
- Use SSL connections in production (`useSSL=true`)
- Keep database credentials secure (use Render's environment variables)
- Consider connection pooling for better performance

## Next Steps

- Set up database migrations if needed
- Configure custom domain (Render paid plans)
- Set up monitoring and alerts
- Configure automatic deployments from Git


