---
title: '[R] Setting Up an Interactive R Shiny Plotting App'
author: Zhenguo Zhang
date: '2025-10-08'
slug: r-setting-up-an-interactive-r-shiny-plotting-app
categories:
  - R
  - Software
tags:
  - R
  - Shiny
  - Programming
description: ''
featured_image: ''
images: []
comment: yes
---

I have an ieda of creating a shiny app to create all kinds of plots interactively,
so that users can choose their color, symbols, etc, which is very useful for data exploration and make a publication-quality figure.

Today, I make it and published it at [https://fortune9.shinyapps.io/interactive_plot/](https://fortune9.shinyapps.io/interactive_plot/). The source code is at github https://github.com/fortune9/interactive_plot.


This guide focuses on the infrastructure setup for creating and deploying an interactive plotting application using R Shiny. For detailed features and functionality, please refer to the [README.md](https://github.com/fortune9/interactive_plot/blob/main/README.md) in the GitHub repository.

## Overview

The application allows users to create customizable plots (scatter, bar, box) from both uploaded data and built-in R datasets, with extensive customization options and export capabilities. All source code is available at [https://github.com/fortune9/interactive_plot](https://github.com/fortune9/interactive_plot).

## Project Setup

1. Create the project structure:
```
interactive_plot/
├── app.R
├── ui.R
├── server.R
├── www/
└── data/
```

2. Install required packages:
```R
install.packages(c("shiny", "ggplot2", "tidyverse"))
```

3. Create the main app file (`app.R`):
```R
library(shiny)
library(ggplot2)
library(tidyverse)

source("ui.R")
source("server.R")

shinyApp(ui = ui, server = server)
```

## Deployment

### 1. Prepare for Deployment

1. Install the rsconnect package:

```R
install.packages("rsconnect")
```

2. Create a shinyapps.io account at https://www.shinyapps.io

3. Get your account tokens from:
   - Dashboard → Account → Tokens

4. Set up authentication in R:

```R
rsconnect::setAccountInfo(
  name='your-account-name',
  token='your-token',
  secret='your-secret'
)
```

### 2. Deploy Using GitHub Actions

1. Add GitHub Secrets:
   - SHINYAPPS_USER
   - SHINYAPPS_TOKEN
   - SHINYAPPS_SECRET

2. Create GitHub Actions workflow (`.github/workflows/deploy.yml`):
```yaml
name: Deploy to shinyapps.io
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: r-lib/actions/setup-r@v2
      - name: Deploy to shinyapps.io
        run: |
          Rscript -e 'install.packages(c("shiny", "rsconnect"))'
          Rscript -e 'rsconnect::deployApp()'
        env:
          SHINYAPPS_USER: ${{ secrets.SHINYAPPS_USER }}
          SHINYAPPS_TOKEN: ${{ secrets.SHINYAPPS_TOKEN }}
          SHINYAPPS_SECRET: ${{ secrets.SHINYAPPS_SECRET }}
```

## Development Tips

1. **Local Testing**
   - Run the app locally using `shiny::runApp()`
   - Test with different browsers and screen sizes
   - Verify all features before deployment

2. **Deployment Checks**
   - Ensure all required packages are listed in the deployment workflow
   - Test deployment locally using rsconnect before pushing to GitHub
   - Monitor GitHub Actions logs for deployment issues

3. **Maintenance**
   - Keep your shinyapps.io tokens secure
   - Update package versions regularly
   - Monitor app usage on shinyapps.io dashboard

## Conclusion

This guide covers the essential setup and deployment steps for your Shiny app. For implementation details and features, refer to the source code and documentation in the GitHub repository at [https://github.com/fortune9/interactive_plot](https://github.com/fortune9/interactive_plot).

For questions or improvements, please open an issue in the GitHub repository.

Do you know? Actually most of the code was written using Github Copilot, to which
I feed the file [development plan.md](https://github.com/fortune9/interactive_plot/blob/main/development_plan.md) to start. Very cool :smile:!
