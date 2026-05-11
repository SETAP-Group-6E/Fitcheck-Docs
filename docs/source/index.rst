FitCheck Documentation
======================

Welcome to the FitCheck documentation.

FitCheck is a Flutter app designed to help users organise their clothes and plan outfits in a simple way. The app gives users a place to manage their wardrobe, create outfit ideas, and keep track of their personal style. It also includes account features so each user can have their own private wardrobe and settings.

The main purpose of FitCheck is to make outfit planning easier and more organised. Instead of trying to remember every item of clothing they own, users can store their clothes in the app and use them to create outfits. This can help users save time, plan better looks, and make better use of the clothes they already have.

Main Features
-------------

FitCheck includes user authentication, which allows users to register, log in, and manage their account securely. This makes the app more personal because each user can access their own wardrobe, outfits, and profile information.

Users can add clothing items to their wardrobe and organise them inside the app. This makes it easier to see what clothes are available and use them when creating outfits. The wardrobe feature is one of the main parts of the app because it connects directly to outfit creation.

The app also allows users to create and save outfits. This feature helps users combine clothing items and plan what they want to wear. It can be useful for everyday planning, special events, or simply trying different style ideas.

FitCheck also includes social and settings features. The social page gives the app a more interactive feel, while the settings pages allow users to update their profile, change their email or password, read app information, and manage their account.

Technology Used
---------------

FitCheck is built using Flutter and Dart. Flutter is used to create the app interface, while Dart is used for the app logic. This allows the app to work across different platforms while keeping the codebase organised.

Supabase is used for authentication and backend data storage. This means the app can manage user accounts and store important app data such as wardrobe items and outfits.

The project also uses a repository pattern. This helps separate the app into clear sections, making the code easier to read, test, and maintain. It also keeps the user interface separate from the data logic.

Project Structure
-----------------

The project is organised into different folders so that each part of the app has a clear purpose.

The ``Presentation`` folder contains the app screens, pages, providers, and reusable widgets. This includes pages such as login, register, home, wardrobe, social, and settings.

The ``Domain`` folder contains repository definitions. These files describe what the app needs from the data layer without depending on a specific backend service.

The ``Data`` folder contains the real repository implementations and services. This is where Supabase is used to connect the app to authentication and stored data.

The ``test`` folder contains unit tests for important parts of the app. These tests help check that the repositories and app logic work correctly.

Contents
--------

.. toctree::
   :maxdepth: 2
   :caption: Documentation

   getting_started
   features
   architecture
   testing
