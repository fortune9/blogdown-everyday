---
title: '[R] Automatic email sending with *blastula*'
author: Zhenguo Zhang
date: '2021-09-06'
slug: r-automatic-email-sending-with-blastula
categories:
  - R
  - Software
tags:
  - R
  - Programming
description: ''
featured_image: ''
images: []
comment: yes
---

[blastula](https://cran.r-project.org/web/packages/blastula/)
is an R package that can be used to send emails automatically.
In addition, it can render Rmarkdown documents into emails,
which can be sent later. The main challenge is to setup smtp
server. In this post, I will show how to setup smtp server and
send emails afterwards.

One can find more on `blastula` at [github](https://github.com/rstudio/blastula).

## Install `blastula`

```r
install.packages("blastula")
```

## Setup smtp server

Basically, one needs change settings in his email accounts
to allow app-triggered actions, and this sometimes involves
decreasing security levels, so may not be ideal. Here, I will
describe settings for office365 and gmail smtp servers, and
the credentials are based on App passwords, so it will not
compromise email account securities overall.

### office365 smtp server

Here I will use the email account 'example@live.com'
as an example. And the steps are as follows:

1. enable 2-step verification by logging into the email
  account under security settings. One can follow this
  [link](https://support.microsoft.com/en-us/account-billing/turning-two-step-verification-on-or-off-for-your-microsoft-account-b1a56fc2-caf3-a5a1-f7e3-4309e99987ca) for more details.

2. create app password. After enabling 2-step verification,
  one will see 'App passwords' under 'Advanced security options'.
  Simply click 'Create a new app password' to get an app
  password. Record this password or keep the page open because
  we will use this password in next step. More details can
  be found at [here](https://support.microsoft.com/en-us/account-billing/using-app-passwords-with-apps-that-don-t-support-two-step-verification-5896ed9b-4263-e681-128a-a6f2979a7944).

3. store SMTP credentials in system's key-value store.

    Run the following R code to store the credentials
    
    ```r
    create_smtp_creds_key(
      id = "my.office365",
      user = "example@live.com",
      host="smtp.office365.com",
      port=587,
      use_ssl = TRUE,
      overwrite=T
    )
    ```
    
    The above command will prompt to input password, just
    copy and paste the 'app password' from the prior step
    into the field, and click submit.
    
    Note that the 'id' argument provides a unique name for
    using the smtp server when sending emails (see next section).
    
    You may also use `create_smtp_creds_file()` to store
    credentials in a file, but it is not secure.

### Gmail smtp server

Similar to office365 server setting, the setting for Gmail
smtp server also includes the steps of enabling 2-step
verification, generating app password, and storing the
credentials.

Here, I use the gmail account 'example@gmail.com' as
an example.

1. enable 2-step verification in gmail account. One can
  do so by visiting this [page](https://myaccount.google.com/security) and under
  'Signing in to Google'. Follow instructions to finish
  it.

2. generate app password. After prior step, one can create
  app password by visiting google security page again. More
  details can be found [here](https://support.google.com/accounts/answer/185833).
  
3. store smtp server credentials, using the following R code
    
    ```r
    create_smtp_creds_key(
      id = "my.gmail",
      user = "example@gmail.com",
      host="smtp.gmail.com",
      port=465,
      use_ssl = TRUE,
      overwrite=T
    )
    ```

    Here `my.gmail` is the id of the gmail server and can be
    called by using the function `creds_key()`.

## Prepare email

There are multiple ways to create emails. Here are
a list of functions:

Function | Input
--- | :---
render_email | Rmarkdown document with output blastula::blastula_email.
compose_email | use arguments `header`, `body`, and `footer` to provide email content.

Here I will use `compose_email` to create an email object.

```r
header_text<-"This is an email header"

body_text<-"Hello,

This is body text.
"

footer_text<-"Footer is here"

email<-compose_email(body=body_text, header=header_text, footer=footer_text)
```

## Send email

```r
# use office365 smtp server
email %>%
  smtp_send(
    to = "jesus@example.com",
    from = "example@live.com",
    subject = "An automatic email from office365 server",
    credentials = creds_key("my.office365")
  )

# or use gmail smtp server
email %>%
  smtp_send(
    to = "jesus@example.com",
    from = "example@gmail.com",
    subject = "An automatic email from gmail server",
    credentials = creds_key("my.gmail")
  )

```

Note that the email accounts from school or work may not
allow settings like above, so the methods described here
only work for accouts that allow one to change account
security settings.

That is it. Happy R programming :smile:.

## References

1. blastula github page: https://github.com/rstudio/blastula

2. blastula vignette: https://cran.r-project.org/web/packages/blastula/vignettes/sending_using_smtp.html
