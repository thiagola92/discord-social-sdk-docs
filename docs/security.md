---
icon: lucide/shield-alert
---

# Security
!!! danger
    **Work-in-progress**  

    Anythin read here is **not** to take as true because I didn't confirm it. Treat it as speculation.  

## App Testers
During development, only your account and accounts added as **Application Testers** can interact with your application.  

In your [developer portal](https://discord.com/developers/home):  

- Access your application
- Go to section "App Testers"
- Add an app tester using his account username
- He will receive an email asking to accept

## Application ID
At first you may think that your Application ID is public information... But it's not!  

Reading [Discord Developer Terms of Service section 2.d](https://support-dev.discord.com/hc/en-us/articles/8562894815383-Discord-Developer-Terms-of-Service), you will find:  

> You will use any developer credentials (such as your **Application ID**, passwords, keys, tokens, and client secrets) we assign to you solely with your Application and the applicable APIs (and will not permit or enable any other Application to use them) and will **treat them as Discord confidential information**...  

This security is needed because Application ID is all that is needed to interact with the Discord Social SDK... In other words, is all that is needed to impersonate other application.  

If your game runs everything locally, that means that you need to put your Application ID in the binary and this means that someone can reverse engineer to get it (even if you [compile with PCK encryption key](https://docs.godotengine.org/en/4.4/contributing/development/compiling/compiling_with_script_encryption_key.html)).  