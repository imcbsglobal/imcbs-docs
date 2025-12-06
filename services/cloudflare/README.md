# Cloudflare Services Guide

This directory contains documentation for Cloudflare services, configuration, and integration.

---

# 🚀 Django + Cloudflare R2 Storage Setup

Complete step-by-step guide to integrate Cloudflare R2 object storage with Django.

## 📋 Prerequisites

- Django project setup
- Cloudflare account with R2 enabled
- Python 3.8+

---

## 1️⃣ Create Cloudflare R2 Bucket

### Step 1: Create a New Bucket
1. Log in to your [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. Navigate to **R2 Object Storage** in the sidebar
3. Click **Create Bucket**
4. Enter your bucket name (e.g., `alfa`)
5. Select your preferred location
6. Click **Create Bucket**

### Step 2: Enable Public Access
1. Go to your bucket settings
2. Click on **Settings** tab
3. Under **Public Access**, click **Allow Access**
4. Copy the **Public Bucket URL** (e.g., `pub-cbda444627e344d8b99d3d52bc197128.r2.dev`)
5. Save this URL - you'll need it for your `.env` file

---

## 2️⃣ Install Required Packages

```bash
pip install boto3 django-storages python-dotenv
```

**Package Descriptions:**
- `boto3`: AWS SDK for Python (works with Cloudflare R2)
- `django-storages`: Custom storage backends for Django
- `python-dotenv`: Loads environment variables from `.env` file

---

## 3️⃣ Setup Environment Variables

### Step 1: Create `.env` File
Create a `.env` file in your Django project root (same directory as `manage.py`):

```bash
# For Windows
type nul > .env

# For Linux/Mac
touch .env
```

### Step 2: Add Environment Variables
Add the following to your `.env` file:

```env
# Enable/Disable Cloudflare R2 Storage
CLOUDFLARE_R2_ENABLED=true

# 📝 UPDATE THIS: Your bucket name from Step 1
CLOUDFLARE_R2_BUCKET=bucket name

# 📝 UPDATE THIS: Your public bucket URL from Step 1 (Step 2) - WITHOUT https://
CLOUDFLARE_R2_PUBLIC_URL=pub-...

# No Change
CLOUDFLARE_R2_BUCKET_ENDPOINT=https://5543538795e940846a189901d1be5a3b.r2.cloudflarestorage.com

# No change
CLOUDFLARE_R2_ACCESS_KEY=426c641a3ebb4cf080535e8e5af4b161

# No change
CLOUDFLARE_R2_SECRET_KEY=09a6866c56df5f3e1257577f6a732c3cf98599565672ab3829a74bae086c7706

# No Change
CLOUDFLARE_R2_ACCOUNT_ID=5543538795e940846a189901d1be5a3b
```

**What to Update:**
- ✅ `CLOUDFLARE_R2_BUCKET`: Your bucket name (e.g., `alfa`)
- ✅ `CLOUDFLARE_R2_PUBLIC_URL`: Your public bucket URL **without** `https://` (e.g., `pub-xxx.r2.dev`)


**Important Notes:**
- ⚠️ Public URL should be **domain only** (no `https://` prefix)
- ⚠️ All credentials come from Cloudflare R2 dashboard

### Step 3: Load Environment Variables
Add this at the **top** of your `settings.py` file:

```python
import os
from pathlib import Path
from dotenv import load_dotenv


# Load environment variables from .env file
load_dotenv()
```

---

## 4️⃣ Update `INSTALLED_APPS`

Add `storages` to your `INSTALLED_APPS` in `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    
    # Third-party apps
    'storages',  # Add this line
    
    # Your apps
    # ...
]
```

---

## 5️⃣ Replace Media Configuration in `settings.py`

Find and **replace** your existing media configuration with this:

```python
# Cloudflare R2 Storage Configuration
CLOUDFLARE_R2_ENABLED = os.getenv('CLOUDFLARE_R2_ENABLED', 'false').lower() == 'true'

if CLOUDFLARE_R2_ENABLED:
    # Load R2 credentials from environment
    AWS_ACCESS_KEY_ID = os.getenv('CLOUDFLARE_R2_ACCESS_KEY')
    AWS_SECRET_ACCESS_KEY = os.getenv('CLOUDFLARE_R2_SECRET_KEY')
    AWS_STORAGE_BUCKET_NAME = os.getenv('CLOUDFLARE_R2_BUCKET')
    AWS_S3_ENDPOINT_URL = os.getenv('CLOUDFLARE_R2_BUCKET_ENDPOINT')
    AWS_S3_REGION_NAME = 'auto'  # Cloudflare R2 uses 'auto' region
    AWS_S3_SIGNATURE_VERSION = 's3v4'  # Required for R2
    AWS_S3_CUSTOM_DOMAIN = os.getenv('CLOUDFLARE_R2_PUBLIC_URL')

    # Configure Django to use R2 for media files (using built-in S3 storage)
    STORAGES = {
        "default": {
            "BACKEND": "storages.backends.s3boto3.S3Boto3Storage",
        },
        "staticfiles": {
            "BACKEND": "django.contrib.staticfiles.storage.StaticFilesStorage",
        },
    }

    # Set media URL (use public URL if available)
    if AWS_S3_CUSTOM_DOMAIN:
        MEDIA_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/'
else:
    # Fallback to local file storage
    STORAGES = {
        "default": {
            "BACKEND": "django.core.files.storage.FileSystemStorage",
        },
        "staticfiles": {
            "BACKEND": "django.contrib.staticfiles.storage.StaticFilesStorage",
        },
    }
    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

**Configuration Explained:**
- When `CLOUDFLARE_R2_ENABLED=true`: Uses R2 for media storage
- When `CLOUDFLARE_R2_ENABLED=false`: Uses local filesystem
- `AWS_STORAGE_BUCKET_NAME`: Your R2 bucket name
- `AWS_S3_ENDPOINT_URL`: R2 API endpoint URL
- `AWS_S3_REGION_NAME`: Set to `'auto'` for Cloudflare R2
- `AWS_S3_SIGNATURE_VERSION`: Set to `'s3v4'` (required by R2)
- `AWS_S3_CUSTOM_DOMAIN`: Your public R2 domain (without `https://`)
- `STORAGES["default"]`: Uses built-in S3Boto3Storage (no custom backend needed)
- `STORAGES["staticfiles"]`: Handles static files (CSS, JS, images)

---

## 6️⃣ No Custom Storage Backend Needed! ✅

**Good news!** With the configuration above, you **don't need to create a custom storage backend**. 

The built-in `storages.backends.s3boto3.S3Boto3Storage` works perfectly with Cloudflare R2 when you set:
- `AWS_S3_ENDPOINT_URL`: Points to R2
- `AWS_S3_REGION_NAME = 'auto'`: Required for R2
- `AWS_S3_SIGNATURE_VERSION = 's3v4'`: R2's signature method
- `AWS_S3_CUSTOM_DOMAIN`: Your public R2 URL

**Why this works:**
- Cloudflare R2 is **S3-compatible**
- django-storages' S3Boto3Storage supports custom endpoints
- No need for custom code when using standard AWS variable names


<hr>


## 🔧 Troubleshooting

### Issue 1: "NoCredentialsError"
**Error:** `Unable to locate credentials`

**Solution:**
- Verify `.env` file exists in project root
- Check `load_dotenv()` is called in `settings.py`
- Ensure environment variables are loaded correctly:
  ```python
  print(os.getenv('CLOUDFLARE_R2_ACCESS_KEY'))  # Should print your key
  ```

### Issue 2: "EndpointConnectionError"
**Error:** `Could not connect to the endpoint URL`

**Solution:**
- Verify `CLOUDFLARE_R2_BUCKET_ENDPOINT` is correct
- Check your account ID in the endpoint URL
- Ensure your API tokens have correct permissions

### Issue 3: "Access Denied" / 403 Error
**Error:** `An error occurred (403) when calling the PutObject operation`

**Solution:**
- Verify API token has **Object Read & Write** permissions
- Check bucket name matches exactly
- Ensure public access is enabled in R2 settings

### Issue 4: Files Upload But URLs Don't Work
**Error:** Files show in R2 but URLs return 404

**Solution:**
- Verify `CLOUDFLARE_R2_PUBLIC_URL` is correct
- Ensure public access is enabled for the bucket
- Check the public domain settings in R2
- Use `https://` prefix in `MEDIA_URL`

### Issue 5: Local Development Works But Production Fails
**Error:** Works locally but fails on server

**Solution:**
- Verify `.env` file is deployed to production
- Check production environment variables are set
- Ensure `python-dotenv` is installed in production
- Verify firewall allows outbound connections to R2

---

## 📊 Verify Configuration

Run this diagnostic script to check your setup:

```python
# check_r2_config.py
import os
from django.conf import settings

print("=" * 50)
print("Cloudflare R2 Configuration Check")
print("=" * 50)

print(f"\n✓ R2 Enabled: {getattr(settings, 'CLOUDFLARE_R2_ENABLED', False)}")
print(f"✓ Bucket Name: {getattr(settings, 'AWS_STORAGE_BUCKET_NAME', 'NOT SET')}")
print(f"✓ Public URL: {getattr(settings, 'AWS_S3_CUSTOM_DOMAIN', 'NOT SET')}")
print(f"✓ Endpoint: {getattr(settings, 'AWS_S3_ENDPOINT_URL', 'NOT SET')}")
print(f"✓ Region: {getattr(settings, 'AWS_S3_REGION_NAME', 'NOT SET')}")
print(f"✓ Signature: {getattr(settings, 'AWS_S3_SIGNATURE_VERSION', 'NOT SET')}")
print(f"✓ Access Key: {'SET' if getattr(settings, 'AWS_ACCESS_KEY_ID', None) else 'NOT SET'}")
print(f"✓ Secret Key: {'SET' if getattr(settings, 'AWS_SECRET_ACCESS_KEY', None) else 'NOT SET'}")
print(f"✓ Media URL: {settings.MEDIA_URL}")
print(f"✓ Storage Backend: {settings.STORAGES['default']['BACKEND']}")

print("\n" + "=" * 50)
```

Run with:
```bash
python manage.py shell < check_r2_config.py
```

---

## 🚀 Production Deployment Tips

### 1. Security Best Practices
- ✅ Never commit `.env` file to version control
- ✅ Add `.env` to `.gitignore`
- ✅ Use environment variables in production (not `.env` file)
- ✅ Rotate API keys regularly
- ✅ Use separate buckets for dev/staging/production

### 2. Performance Optimization
```python
# In settings.py - Add caching
AWS_S3_OBJECT_PARAMETERS = {
    'CacheControl': 'max-age=86400',  # Cache for 1 day
}

# Enable connection pooling
AWS_S3_MAX_POOL_CONNECTIONS = 50
```

### 3. Backup Strategy
- Set up R2 bucket lifecycle rules
- Enable versioning in R2 settings
- Regular backup exports to another location

### 4. Monitoring
- Monitor R2 usage in Cloudflare dashboard
- Set up alerts for storage quota
- Track API request counts and costs

---

