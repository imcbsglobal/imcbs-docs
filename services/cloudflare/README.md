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
4. Enter your bucket name (e.g., `icare`)
5. Select your preferred location
6. Click **Create Bucket**

### Step 2: Enable Public Access
1. Go to your bucket settings
2. Click on **Settings** tab
3. Under **Public Access**, click **Allow Access**
4. Copy the **Public Bucket URL** (e.g., `pub-cbda444627e344d8b99d3d52bc197128.r2.dev`)
5. Save this URL - you'll need it for your `.env` file

### Step 3: Generate API Tokens
1. Navigate to **R2** → **Manage R2 API Tokens**
2. Click **Create API Token**
3. Give it a name (e.g., `django-production`)
4. Set permissions to **Object Read & Write**
5. Click **Create API Token**
6. Copy the **Access Key ID** and **Secret Access Key**
7. ⚠️ **Important**: Save these credentials immediately - they won't be shown again!

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
CLOUDFLARE_R2_BUCKET=icare

# 📝 UPDATE THIS: Your public bucket URL from Step 1 (Step 2)
CLOUDFLARE_R2_PUBLIC_URL=pub-cbda444627e344d8b99d3d52bc197128.r2.dev

# ⚠️ DO NOT CHANGE: Standard R2 endpoint (use your account ID from dashboard)
CLOUDFLARE_R2_BUCKET_ENDPOINT=https://5543538795e940846a189901d1be5a3b.r2.cloudflarestorage.com

# 📝 UPDATE THIS: Access Key from Step 3
CLOUDFLARE_R2_ACCESS_KEY=426c641a3ebb4cf080535e8e5af4b161

# 📝 UPDATE THIS: Secret Key from Step 3
CLOUDFLARE_R2_SECRET_KEY=09a6866c56df5f3e1257577f6a732c3cf98599565672ab3829a74bae086c7706

# ⚠️ DO NOT CHANGE: Your Cloudflare Account ID (from R2 dashboard URL)
CLOUDFLARE_R2_ACCOUNT_ID=5543538795e940846a189901d1be5a3b
```

**What to Update:**
- ✅ `CLOUDFLARE_R2_BUCKET`: Your bucket name
- ✅ `CLOUDFLARE_R2_PUBLIC_URL`: Your public bucket URL (from Step 1, Step 2)
- ✅ `CLOUDFLARE_R2_ACCESS_KEY`: Your access key (from Step 1, Step 3)
- ✅ `CLOUDFLARE_R2_SECRET_KEY`: Your secret key (from Step 1, Step 3)

**What to Keep:**
- ❌ `CLOUDFLARE_R2_BUCKET_ENDPOINT`: Standard format, just replace account ID
- ❌ `CLOUDFLARE_R2_ACCOUNT_ID`: Keep as is (from your Cloudflare dashboard)

### Step 3: Load Environment Variables
Add this at the **top** of your `settings.py` file:

```python
import os
from pathlib import Path
from dotenv import load_dotenv

# Build paths inside the project
BASE_DIR = Path(__file__).resolve().parent.parent

# Load environment variables from .env file
load_dotenv(os.path.join(BASE_DIR, '.env'))
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
    CLOUDFLARE_R2_BUCKET_NAME = os.getenv('CLOUDFLARE_R2_BUCKET')
    CLOUDFLARE_R2_ENDPOINT = os.getenv('CLOUDFLARE_R2_BUCKET_ENDPOINT')
    CLOUDFLARE_R2_PUBLIC_URL = os.getenv('CLOUDFLARE_R2_PUBLIC_URL')
    CLOUDFLARE_R2_ACCOUNT_ID = os.getenv('CLOUDFLARE_R2_ACCOUNT_ID', '')

    # Configure Django to use R2 for media files
    STORAGES = {
        "default": {
            "BACKEND": "icare_backend.storage_backends.R2MediaStorage",
        },
        "staticfiles": {
            "BACKEND": "django.contrib.staticfiles.storage.StaticFilesStorage",
        },
    }

    # Set media URL (use public URL if available)
    if CLOUDFLARE_R2_PUBLIC_URL:
        MEDIA_URL = f'https://{CLOUDFLARE_R2_PUBLIC_URL}/media/'
    else:
        MEDIA_URL = f'{CLOUDFLARE_R2_ENDPOINT}/{CLOUDFLARE_R2_BUCKET_NAME}/media/'
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
- `STORAGES["default"]`: Handles user-uploaded media files
- `STORAGES["staticfiles"]`: Handles static files (CSS, JS, images)

---

## 6️⃣ Create Custom Storage Backend

Create a new file `storage_backends.py` in your Django app directory (e.g., `icare_backend/storage_backends.py`):

```python
"""
Custom storage backend for Cloudflare R2 object storage.
This uses the S3-compatible API provided by Cloudflare R2.
"""
from storages.backends.s3boto3 import S3Boto3Storage
from django.conf import settings


class R2MediaStorage(S3Boto3Storage):
    """
    Custom storage backend for Cloudflare R2.
    Inherits from S3Boto3Storage since R2 is S3-compatible.
    """
    bucket_name = settings.CLOUDFLARE_R2_BUCKET_NAME
    custom_domain = settings.CLOUDFLARE_R2_PUBLIC_URL or None
    endpoint_url = settings.CLOUDFLARE_R2_ENDPOINT
    access_key = settings.AWS_ACCESS_KEY_ID
    secret_key = settings.AWS_SECRET_ACCESS_KEY
    default_acl = 'public-read'  # Make uploaded files publicly accessible
    file_overwrite = False  # Don't overwrite files with the same name
    location = 'media'  # Store files in 'media' folder within bucket
```

**Storage Backend Options:**
- `bucket_name`: Your R2 bucket name
- `custom_domain`: Public URL for accessing files
- `endpoint_url`: R2 API endpoint
- `default_acl`: Make files public by default
- `file_overwrite`: Prevent accidental file overwrites
- `location`: Subfolder within bucket for media files

---

## 7️⃣ Test the Integration

### Step 1: Run Migrations
```bash
python manage.py makemigrations
python manage.py migrate
```

### Step 2: Collect Static Files (Optional)
```bash
python manage.py collectstatic
```

### Step 3: Start Development Server
```bash
python manage.py runserver
```

### Step 4: Test File Upload

**Option A: Django Admin**
1. Create a model with an `ImageField` or `FileField`
2. Upload a file through Django admin
3. Check your R2 bucket dashboard - the file should appear

**Option B: Django Shell**
```bash
python manage.py shell
```

```python
from django.core.files.base import ContentFile
from django.core.files.storage import default_storage

# Test upload
path = default_storage.save('test.txt', ContentFile(b'Hello Cloudflare R2!'))
print(f"File saved to: {path}")
print(f"File URL: {default_storage.url(path)}")

# Test file exists
exists = default_storage.exists(path)
print(f"File exists: {exists}")

# Clean up
default_storage.delete(path)
print("Test file deleted")
```

### Step 5: Verify in Cloudflare Dashboard
1. Go to your Cloudflare R2 dashboard
2. Open your bucket
3. Check the `media/` folder
4. Verify uploaded files appear with public access

---

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

print(f"\n✓ R2 Enabled: {settings.CLOUDFLARE_R2_ENABLED}")
print(f"✓ Bucket Name: {getattr(settings, 'CLOUDFLARE_R2_BUCKET_NAME', 'NOT SET')}")
print(f"✓ Public URL: {getattr(settings, 'CLOUDFLARE_R2_PUBLIC_URL', 'NOT SET')}")
print(f"✓ Endpoint: {getattr(settings, 'CLOUDFLARE_R2_ENDPOINT', 'NOT SET')}")
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

## 📚 Additional Resources

- [Cloudflare R2 Documentation](https://developers.cloudflare.com/r2/)
- [django-storages Documentation](https://django-storages.readthedocs.io/en/latest/)
- [boto3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
- [Django File Storage Documentation](https://docs.djangoproject.com/en/stable/topics/files/)

---

## 🆘 Common Questions

**Q: Can I use R2 for static files too?**
A: Yes, but it's recommended to use Cloudflare CDN or local static files for better performance.

**Q: How much does R2 cost?**
A: R2 has no egress fees. Check [Cloudflare R2 Pricing](https://www.cloudflare.com/products/r2/).

**Q: Can I migrate existing media files to R2?**
A: Yes, use Django management commands or AWS CLI to sync files.

**Q: Does this work with Django < 4.2?**
A: For older Django versions, use `DEFAULT_FILE_STORAGE` instead of `STORAGES` dict.

---

**Need Help?** Open an issue or contact the team!
