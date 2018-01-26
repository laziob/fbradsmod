# fbRads

This R package includes wrapper functions around the [Facebook Marketing API](https://developers.facebook.com/docs/marketing-apis) to create, read, update and delete custom audiences, images, campaigns, adsets, ads and related content.

Vignette and more detailed documentation is coming soon, until then please see the [slides](http://bit.ly/domino-webinar-fbRads) presented at [useR! 2015](http://user2015.math.aau.dk/contributed_talks#210), [EARL 2015](https://earlconf.com/2015/boston/speakers/speaker.php?s=gergely_daroczi), the [Los Angeles R Users Group](http://www.meetup.com/Los-Angeles-R-Users-Group-Data-Science/events/226717454/) and at a [Domino Webinar](https://www.dominodatalab.com/resource/optimizing_facebook_campaigns_with_r).

## Creating a Facebook App & connect with OAuth token

To be able to use this package, you will have to create a Facebook App and authorize it to manage your Facebook ads. Basic steps to create an app with Development access level letting you manage up to 5 Facebook ad accounts:

1. Create new application at https://developers.facebook.com/apps with "basic setup".
2. Fill in a unique `Display Name` (eg "app_testing_foobar_42"), set the category to eg "Business" and provide your e-mail address. Click on "Create App ID" & pass the captcha test.
3. In "Settings/Basic", click "Add Platform" add create "Website" platform with the URL of http://localhost:1410/ and "localhost" as the "App Domain". Click "Save Changes".
4. Add a new "Facebook Login" product, and then set http://localhost:1410/ as the "Valid OAuth redirect URIs". Click "Save Changes".
5. Also add and configure the "Marketing API" product.
6. Note your "App ID" and "App Secret" on your dashboard, and use those in the below R script to get a token for future authentication (with recent version of `httr`, [users reported](https://github.com/cardcorp/fbRads/issues/28) that the `Sys.setenv` line is not needed):

    ```r
    library(httr)
    app <- oauth_app('facebook', 'your app id', 'your app secret')
    Sys.setenv('HTTR_SERVER_PORT' = '1410/')
    tkn <- oauth2.0_token(
        oauth_endpoints('facebook'), app, scope = 'business_management',
        type  = 'application/x-www-form-urlencoded', cache = FALSE)
    tkn <- jsonlite::fromJSON(names(tkn$credentials))$access_token
    ```

7. Please note the above last step: we store the token as a string. Now you can save that token in a safe place and start using `fbRads`, eg:

    ```r
    fbad_init(accountid = accountid, token = tkn, version = '2.10')
    ```

8. And list all your ads along with the ad name and status or eg filter for the active ads:

    ```r
    fbad_list_ad(fields = c('name', 'effective_status'))
    fbad_list_ad(statuses = 'ACTIVE', fields = 'name')
    ```

## Development version

The package is actively maintained, but not frequently pushed to CRAN, so to use the most recent version, install from GitHub. The `master` branch is supposed to include a relatively stable version at all the time:

```r
devtools::install_github('daroczig/fbRads')
```

## Using the package

This package makes your life more convenient when it comes to interacting with the Facebook Marketing API, but unfortunately, it cannot save you the time to get familiar with the actual API endpoints. To be able to interact with Facebook, you have to learn about how the API works etc -- see the documentation at <https://developers.facebook.com/docs/marketing-apis>

---

This package was originally developed at [CARD.com](http://card.com), licensed under [AGPL-3](LICENSE).
