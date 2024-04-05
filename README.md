# Internship-Task-Assessment-ThePortfolyo.com

## 1.Create S3 Buckets:
- Create S3 buckets for each of your React apps (web1, web2, web3).
- Upload the built React app files (including index.html, css, js, etc.) to their respective buckets.

## 2.Configure Static Website Hosting:
- Enable static website hosting for each S3 bucket.
- Set the index document to index.html.

## 3.Set Up CloudFront Distribution:
- Create a CloudFront distribution.
- Configure the distribution to use the S3 buckets as origins.
- Set the default behavior to redirect HTTP to HTTPS if desired.

## 4.Set Up Lambda@Edge Function:
- Create a Lambda function in the AWS region where you want to deploy your CloudFront distribution.
- Write a Lambda function that examines the incoming request's host header (subdomain) and rewrites the request path accordingly to route to the appropriate S3 bucket.
- Here's a basic example of how the Lambda function might look in Node.js:

Javascript
```Javascript
exports.handler = async (event) => {
    const request = event.Records[0].cf.request;
    const host = request.headers.host[0].value;

    let subdomain = host.split('.')[0];

    // Map subdomain to S3 bucket name
    let bucketName;
    switch (subdomain) {
        case 'username1':
            bucketName = 'web1-bucket';
            break;
        case 'username2':
            bucketName = 'web2-bucket';
            break;
        case 'username3':
            bucketName = 'web3-bucket';
            break;
        // Add more cases for additional subdomains if needed
        default:
            bucketName = 'default-bucket'; // Default bucket if subdomain not found
    }

    // Rewrite the request path to point to the appropriate S3 bucket
    request.origin = {
        s3: {
            domainName: `${bucketName}.s3.amazonaws.com`,
            region: 'us-east-1', // Change to your bucket's region
        }
    };

    return request;
};
```
## 1.Associate Lambda Function with CloudFront:
- Associate the Lambda@Edge function with the appropriate CloudFront event (e.g., Viewer Request)
- Deploy the Lambda function.

## 2.Set Up DNS:
- Configure DNS records for your domains (web1.example.com, web2.example.com, web3.example.com) to point to the CloudFront distribution.

## 3.React App Adjustments:
- To address the "index.html loading correctly but CSS/JS from root" issue, configure your React applications to use relative paths for loading assets (CSS, JS). This ensures they load from the correct folder within the S3 bucket regardless of the accessed URL. You can achieve this using techniques like:
- Dynamic base path configuration based on environment variables. Libraries like react-router-dom can help with this.
- Using a subpath in your asset URLs within the React code (e.g., ./assets/style.css instead of just style.css).

## 4.Testing and Deployment:
- Test the setup to ensure that accessing different subdomains correctly routes to the corresponding React apps.
- Deploy any necessary adjustments to your React app code to ensure compatibility with the S3 hosting setup.

This setup will use CloudFront and Lambda@Edge to dynamically route requests based on subdomains, allowing you to host multiple React apps in a single S3 bucket while enabling different URLs to point to each app. Additionally, it addresses common issues like CSS and JS loading from the correct paths.

