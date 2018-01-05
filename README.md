# standard-redirects-for-cloudfront

A Lambda@Edge function that implements standard web server redirects:

URIs ending with a slash (e.g. "/something/") are "internally" redirected to "/something/index.html", i.e. the browser sees "/something/" but on the server-side the content is taken from "/something/index.html".

URIs without an extension (and not ending with a slash) will redirect with an HTTP status 301 (Moved Permanently) to the same URL with a slash appended.

## Examples

  / -> internal redirect -> /index.html
  /foo/bar/ -> internal redirect -> /foo/bar/index.html
  /foo -> external redirect (301) -> /foo/
  /foo.html -> no redirect
  /foo/bar.html -> no redirect
  /foo/index.html -> external redirect (301) -> /foo/

## Notes

This URL scheme is somewhat opinionated. It tries to balance SEO requirements with server-side tooling. (E.g. S3 tooling tries to infer the content-type from the file extension.)

It allows you to have very nice outward facing URLs like "/cooltopic", that internally use a file with a correct extension: "cooltopic/index.html". To have content other than index.html in a folder, you need to expose the file extension: "/cooltopic/somecontent.html"

## Installation

### Installation via the Serverless Application Repository

1. Install the application "standard-redirects-for-cloudfront".
2. Go to the Cloudformation Console
3. Select the created role and edit the trust relationship, set the policy to:

  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Service": [
            "lambda.amazonaws.com",
            "edgelambda.amazonaws.com"
          ]
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }

4. Select the Output Value, this is the ARN (including the version) for the Lambda function.
5. In CloudFront edit a *Behaviour* and add a *Lambda Function Association* of type "Event Type" and enter the Lambda function ARN from the previous step.


### Manual installation

1. Create a function called "LATE-standard-redirects-for-cloudfront" in N. Virginia (us-east-1)
2. Run "npm run deploy"

This function assumes that your CloudFront distribution handles the URL "/" directly by having the property "Default Root Object"
set to "index.html". 

TODO: IAM, SAM