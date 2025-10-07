# 🚀 Deployment Guide: Labrador Website to S3

This guide will help you deploy your Labrador website to AWS S3 with automatic deployment from GitHub.

## 📋 Prerequisites

1. **AWS Account** with S3 access
2. **GitHub Account** 
3. **AWS CLI** (optional, for manual deployment)

## 🔧 Setup Steps

### 1. Create S3 Bucket

```bash
# Create S3 bucket (replace 'your-labrador-website' with your unique bucket name)
aws s3 mb s3://your-labrador-website

# Enable static website hosting
aws s3 website s3://your-labrador-website --index-document index.html --error-document index.html
```

### 2. Configure S3 Bucket Permissions

Your S3 bucket needs public read access. The GitHub Actions workflow will handle this automatically, but you can also do it manually:

```bash
# Set bucket policy for public access
aws s3api put-bucket-policy --bucket your-labrador-website --policy '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-labrador-website/*"
    }
  ]
}'
```

### 3. Create GitHub Repository

1. Go to [GitHub](https://github.com) and create a new repository
2. Name it something like `labrador-website`
3. Don't initialize with README (we already have one)

### 4. Push Your Code to GitHub

```bash
# Add GitHub remote (replace with your repository URL)
git remote add origin https://github.com/yourusername/labrador-website.git

# Push to GitHub
git branch -M main
git push -u origin main
```

### 5. Configure GitHub Secrets

In your GitHub repository, go to **Settings** → **Secrets and variables** → **Actions** and add these secrets:

- `AWS_ACCESS_KEY_ID`: Your AWS access key ID
- `AWS_SECRET_ACCESS_KEY`: Your AWS secret access key  
- `S3_BUCKET_NAME`: Your S3 bucket name (e.g., `your-labrador-website`)
- `AWS_REGION`: Your AWS region (e.g., `us-east-1`)

### 6. Create AWS IAM User (Recommended)

For security, create a dedicated IAM user for GitHub Actions:

1. Go to AWS IAM Console
2. Create new user: `github-actions-labrador-deploy`
3. Attach this policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject",
                "s3:ListBucket",
                "s3:PutBucketPolicy",
                "s3:GetBucketWebsite",
                "s3:PutBucketWebsite"
            ],
            "Resource": [
                "arn:aws:s3:::your-labrador-website",
                "arn:aws:s3:::your-labrador-website/*"
            ]
        }
    ]
}
```

4. Generate access keys for this user
5. Use these keys in your GitHub secrets

## 🌐 Accessing Your Website

After deployment, your website will be available at:

```
https://your-labrador-website.s3-website-us-east-1.amazonaws.com
```

## 🔄 Automatic Deployment

Every time you push to the `main` branch, GitHub Actions will:

1. ✅ Checkout your code
2. ✅ Configure AWS credentials
3. ✅ Upload files to S3
4. ✅ Set proper content types
5. ✅ Configure static website hosting
6. ✅ Set public read permissions

## 🛠️ Manual Deployment (Alternative)

If you prefer manual deployment:

```bash
# Sync files to S3
aws s3 sync . s3://your-labrador-website --exclude ".*" --exclude "README.md"

# Set content type for HTML
aws s3 cp index.html s3://your-labrador-website/index.html --content-type "text/html"
```

## 🌍 Custom Domain (Optional)

To use a custom domain:

1. **Route 53**: Create hosted zone for your domain
2. **CloudFront**: Create distribution pointing to S3
3. **SSL Certificate**: Use AWS Certificate Manager
4. **DNS**: Point your domain to CloudFront distribution

## 📊 Monitoring & Analytics

Consider adding:
- **CloudWatch**: Monitor S3 access logs
- **Google Analytics**: Track website visitors
- **CloudFront**: Better performance and caching

## 🔧 Troubleshooting

### Common Issues:

1. **403 Forbidden**: Check bucket policy and public access settings
2. **404 Not Found**: Verify `index.html` is in the root of your bucket
3. **CORS Issues**: Add CORS configuration if needed
4. **GitHub Actions Failing**: Check AWS credentials and permissions

### Useful Commands:

```bash
# Check bucket contents
aws s3 ls s3://your-labrador-website --recursive

# Test website locally
python -m http.server 8000

# Validate HTML
npx html-validate index.html
```

## 🎉 Success!

Once deployed, your Labrador website will be:
- ✅ Fast and responsive
- ✅ SEO optimized
- ✅ Mobile-friendly
- ✅ Interactive with name generator
- ✅ Automatically updated on code changes

Enjoy your beautiful Labrador website! 🐕