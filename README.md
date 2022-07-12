# newsapp-frontend

News Articles is a web application that displays the latest news articles from https://newsapi.org/. The application enables you to search articles using their title, displays articles based on category and sort articles based on their published time. newsapp-frontend is the frontend side of this platform. It uses nuxt.js framework for building a very fast web app.

# Directory Description

- The 'config' directory contains the configuration settings for interacting with the API server.
    Note: configuration isn't directly saved inside the 'config' directory, the system uses environmental variables for setting this important values. Therefore in order to send request to the backend or api server refer the config file and set the environmental variables accordingly.