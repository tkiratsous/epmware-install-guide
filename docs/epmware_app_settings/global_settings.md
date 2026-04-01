# Global Settings

This section will guide us to configure Global settings for EPMware applications such as,
email settings, application settings etc.

To navigate to Global settings, navigate to Configuration → Misc → Global.



## Email Settings

The EPMware application server hosts the main EPMware application and requires the following specifications:


| Setting Type              | Value                  | Comments                                                                 | Required  |
|--------------------------|------------------------|--------------------------------------------------------------------------|-----------|
| ADMIN User Email Address | Email Address          | Email address for Admin user. Used to notify user for EPMware errors     | Required  |
| Append Fixed Value for Email Subject (Prefix)     | Environment name (e.g., PROD/TEST) | Used as prefix in email subject based on environment                     | Optional  |
| Email Domain Name        | Email Domain Name      | Domain name used for email configuration                                 | Optional  |
| From Email Address       | Sender’s Email Address | Email address used as sender                                             | Required  |
| Override Email Address   | Recipient Email Address| All emails will be redirected to this address (useful in TEST environment) | Optional  |



## Next Steps

After confirming your environment meets these requirements:

[Planning Classic - Generate Password File](planning_classic.md)


---
