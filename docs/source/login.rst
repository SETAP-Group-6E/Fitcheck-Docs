Login Page
==========

User Manual
-----------

Overview
~~~~~~~~

The Login page is the entry point for **Fitcheck.app**. It allows registered
users to securely sign in to their account and access their personalised
wardrobe and style feed.

Accessing the Login Page
~~~~~~~~~~~~~~~~~~~~~~~~

The Login page appears automatically when the app is launched and no active
session is detected. It can also be reached from the registration screen via
the **Sign up** link.

Page Elements
~~~~~~~~~~~~~

=========== ========================================================
Element     Description
=========== ========================================================
Logo        The Fitcheck logo displayed at the top of the screen
Email field Enter your registered email address
Password    Enter your account password (hidden by default)
Login       Submits your credentials and signs you in
Google      Sign in using your Google account
Apple       Sign in using your Apple account (coming soon)
Sign up     Navigates to the registration page for new users
Close (x)   Dismisses the login screen and returns to the previous screen
=========== ========================================================

How to Log In
~~~~~~~~~~~~~

1. Open **Fitcheck.app**.
2. Enter your **email address** in the email field.
3. Enter your **password** in the password field.
4. Tap the **Login** button.
5. On success, you will be taken to the Home page automatically.

Alternatively, tap the **Google** button to sign in with your Google account.

Error Messages
~~~~~~~~~~~~~~

If login fails, a message will appear at the bottom of the screen. Common
causes include:

- Incorrect email or password
- No internet connection
- Account does not exist

New Users
~~~~~~~~~

If you do not have an account, tap **Sign up** at the bottom of the screen
to create one.

----

Technical Documentation
-----------------------

Component
~~~~~~~~~

**File:** ``lib/Presentation/App/app_pages/login_page.dart``

**Class:** ``LoginPage`` (``ConsumerWidget``)

**Framework:** Flutter with Riverpod state management

Dependencies
~~~~~~~~~~~~

.. code-block:: dart

   import 'package:flutter_riverpod/flutter_riverpod.dart';
   import 'package:google_fonts/google_fonts.dart';
   import 'package:fitcheck/Presentation/App/provider/auth_provider.dart';
   import 'package:fitcheck/Presentation/App/app_style/widgets/password_field.dart';
   import 'package:fitcheck/Presentation/App/app_style/widgets/signin_buttons.dart';
   import 'package:fitcheck/Presentation/App/app_pages/home_page.dart';

State Management
~~~~~~~~~~~~~~~~

``LoginPage`` extends ``ConsumerWidget`` and reads the ``authRepositoryProvider``
via Riverpod to perform authentication. No local state is held in the widget
itself — controllers are created directly inside ``build()``.

=========================================== =======================================
Provider                                    Purpose
=========================================== =======================================
``authRepositoryProvider``                  Provides the authentication repository
=========================================== =======================================

Controllers
~~~~~~~~~~~

Two ``TextEditingController`` instances are created inside ``build()``:

- ``emailController`` — captures the user's email input
- ``passwordController`` — captures the user's password input

Authentication Flow
~~~~~~~~~~~~~~~~~~~

On tapping **Login**, the following occurs:

.. code-block:: dart

   final authRepo = ref.read(authRepositoryProvider);
   await authRepo.signIn(
     email: emailController.text.trim(),
     password: passwordController.text.trim(),
   );

- On **success**: a ``SnackBar`` shows ``'Login successful!'`` and the user is
  navigated to ``HomePage`` using ``Navigator.pushReplacement``.
- On **failure**: a ``SnackBar`` shows ``'Login failed: <error>'``.

Sign-In Options
~~~~~~~~~~~~~~~

=========== ============================================================
Method      Implementation
=========== ============================================================
Email       ``authRepo.signIn()`` via ``authRepositoryProvider``
Google      ``GoogleSignInButton()`` widget from ``signin_buttons.dart``
Apple       Icon button present in UI — ``onPressed`` is currently ``null`` (not yet implemented)
=========== ============================================================

Navigation
~~~~~~~~~~

=========== ============================================================
Action      Destination
=========== ============================================================
Login success   ``HomePage`` via ``Navigator.pushReplacement``
Sign up link    ``/register`` route via ``Navigator.popAndPushNamed``
Close button    Previous screen via ``Navigator.pop``
=========== ============================================================

UI Structure
~~~~~~~~~~~~

The layout uses a ``Row`` with three ``Expanded`` children:

- **flex: 1** — left margin spacer
- **flex: 8** — main content column (logo, fields, buttons)
- **flex: 1** — right column containing the close (X) button

Known Issues / Notes
~~~~~~~~~~~~~~~~~~~~

- Apple sign-in button is visible but not yet functional (``onPressed: null``).
- ``TextEditingController`` instances are created inside ``build()`` and are
  not disposed — consider moving to a ``StatefulWidget`` or using
  ``useTextEditingController`` with Flutter Hooks to avoid potential memory leaks.
