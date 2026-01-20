# Render Deployment Guide for SSGA Backend

This guide will help you deploy your Spring Boot backend to Render.

## Prerequisites

1. A Render account (sign up at https://render.com)
2. Your code pushed to a Git repository (GitHub, GitLab, or Bitbucket)
3. A MySQL database (you can create one on Render)

## Step-by-Step Deployment

### Option 1: Using render.yaml (Recommended)

1. **Push your code to Git**
   - Make sure all your code is committed and pushed to your repository

2. **Create a New Web Service on Render**
   - Go to https://dashboard.render.com
   - Click "New +" → "Web Service"
   - Connect your Git repository
   - Render will automatically detect the `render.yaml` file

3. **Configure Environment Variables**
   - In the Render dashboard, go to your service settings
   - Add the following environment variables:
     - `DATABASE_URL`: Your MySQL connection URL (will be provided when you create a database)
     - `DATABASE_USERNAME`: Your database username
     - `DATABASE_PASSWORD`: Your database password
     - `SPRING_PROFILES_ACTIVE`: Set to `production`

4. **Create a MySQL Database**
   - In Render dashboard, click "New +" → "PostgreSQL" (or use external MySQL)
   - Note: Render offers PostgreSQL by default. If you need MySQL, you can:
     - Use Render's PostgreSQL and update your dependencies
     - Use an external MySQL service (like PlanetScale, AWS RDS, or Railway)
   - Copy the connection details

5. **Update Database URL Format**
   - If using Render's PostgreSQL, update `application.properties` to use PostgreSQL driver
   - If using external MySQL, use format: `jdbc:mysql://host:port/database?useSSL=true&serverTimezone=UTC`

6. **Deploy**
   - Click "Create Web Service"
   - Render will build and deploy your application
   - Your backend will be available at: `https://your-service-name.onrender.com`

### Option 2: Manual Configuration

1. **Create a New Web Service**
   - Go to Render dashboard → "New +" → "Web Service"
   - Connect your Git repository
   - Select the repository and branch

2. **Configure Build Settings**
   - **Name**: `ssga-backend` (or your preferred name)
   - **Environment**: `Java`
   - **Build Command**: `./mvnw clean package -DskipTests`
   - **Start Command**: `java -jar target/music-0.0.1-SNAPSHOT.jar`
   - **Root Directory**: `backend/music` (if your repo root is the project root)

3. **Set Environment Variables**
   - `JAVA_VERSION`: `21`
   - `PORT`: (Leave empty - Render sets this automatically)
   - `DATABASE_URL`: Your MySQL connection string
   - `DATABASE_USERNAME`: Your database username
   - `DATABASE_PASSWORD`: Your database password
   - `SPRING_PROFILES_ACTIVE`: `production`
   - `LOG_LEVEL`: `INFO`

4. **Create Database**
   - Create a MySQL database (external service recommended)
   - Copy connection details

5. **Deploy**
   - Click "Create Web Service"
   - Wait for build to complete

## Database Setup

### Using External MySQL (Recommended)

1. **PlanetScale** (Free tier available)
   - Sign up at https://planetscale.com
   - Create a database
   - Copy the connection string
   - Format: `jdbc:mysql://host:port/database?sslMode=REQUIRED`

2. **Railway** (Free tier available)
   - Sign up at https://railway.app
   - Create a MySQL service
   - Copy connection details

3. **AWS RDS** (Paid)
   - Create MySQL instance
   - Get connection details

### Using Render's PostgreSQL (Requires Code Changes)

If you want to use Render's PostgreSQL instead:

1. Update `pom.xml` to use PostgreSQL driver:
   ```xml
   <dependency>
       <groupId>org.postgresql</groupId>
       <artifactId>postgresql</artifactId>
       <scope>runtime</scope>
   </dependency>
   ```

2. Update `application.properties`:
   ```properties
   spring.datasource.driver-class-name=org.postgresql.Driver
   spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
   ```

## Updating Frontend to Use Render Backend

After deployment, update your frontend (on Vercel) to point to your Render backend URL:

1. Find your API endpoint in `frontend/script.js` or wherever you make API calls
2. Update the base URL to: `https://your-service-name.onrender.com/api`

Example:
```javascript
const API_BASE_URL = 'https://your-service-name.onrender.com/api';
```

## Troubleshooting

### Build Fails
- Check that Maven wrapper (`mvnw`) is executable
- Verify Java 21 is available
- Check build logs in Render dashboard

### Application Won't Start
- Verify all environment variables are set
- Check that database is accessible from Render
- Review application logs in Render dashboard

### Database Connection Issues
- Ensure database allows connections from Render's IPs
- Verify connection string format
- Check SSL requirements

### CORS Issues
- The backend is configured to allow all origins (`*`)
- If issues persist, update `SecurityConfig.java` to include your Vercel frontend URL

## Environment Variables Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `PORT` | Server port (auto-set by Render) | - |
| `DATABASE_URL` | MySQL connection URL | `jdbc:mysql://host:3306/music?useSSL=true` |
| `DATABASE_USERNAME` | Database username | `admin` |
| `DATABASE_PASSWORD` | Database password | `your-password` |
| `SPRING_PROFILES_ACTIVE` | Spring profile | `production` |
| `LOG_LEVEL` | Logging level | `INFO` |

## Notes

- Render's free tier spins down after 15 minutes of inactivity
- First request after spin-down may take 30-60 seconds
- Consider upgrading to a paid plan for production use
- Database migrations run automatically via `spring.jpa.hibernate.ddl-auto=update`


