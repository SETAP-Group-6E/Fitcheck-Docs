Wardrobe Page
=============

User Manual
-----------

Overview
~~~~~~~~

The Wardrobe page is the central hub for managing your clothing items and
outfits in **Fitcheck.app**. It displays your personal digital wardrobe in a
searchable, filterable grid, and provides weather-based outfit recommendations
based on current conditions.

Accessing the Wardrobe Page
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Tap **Wardrobe** in the floating navigation bar at the bottom of the screen.
- You must be logged in to access this page.

Page Layout
~~~~~~~~~~~

The page contains two tabs:

=========== ===================================================
Tab         Description
=========== ===================================================
Items       Displays all clothing items in a responsive grid
Outfits     Displays saved outfits as a scrollable list
=========== ===================================================

At the top of the page:

- A **back arrow** to return to the previous screen
- A **search bar** to find items or outfits by name
- A **filter icon** to filter by wear type or layer category
- A **weather banner** showing temperature, condition, and recommended tags

Weather Recommendations
~~~~~~~~~~~~~~~~~~~~~~~

When the page loads, it fetches the current weather and displays a banner. For example::

   Weather 22C (Clear) | Recommended: warm, clear

Tags are generated based on conditions:

=========== ===========
Condition   Tag
=========== ===========
25°C+       ``warm``
15°C–24°C   ``mild``
Below 15°C  ``cold``
Rain/Drizzle ``rain``
Snow        ``snow``
Clear sky   ``clear``
=========== ===========

Searching
~~~~~~~~~

Type in the search bar to filter results in real time:

- **Items tab**: searches by name and wear type
- **Outfits tab**: searches by name and description

Results update automatically after a short pause as you type.

Filtering
~~~~~~~~~

Tap the **filter icon** to open the Filter panel. Filter by:

- **Wear Type** — e.g. Casual, Formal, Sport (shows item count per type)
- **Layer Category** — e.g. Base, Mid, Outer (shows item count per category)

Select checkboxes and tap **Done** to apply. Filters combine with search.

Adding a Clothing Item
~~~~~~~~~~~~~~~~~~~~~~

1. On the **Items** tab, tap the **+** tile (dashed box, top-left of grid).
2. Fill in the item details in the form that appears.
3. Save — the item appears in the grid immediately.

Editing a Clothing Item
~~~~~~~~~~~~~~~~~~~~~~~

1. Tap any item tile in the grid.
2. The item editor opens with existing details pre-filled.
3. Make changes and save.

Deleting a Clothing Item
~~~~~~~~~~~~~~~~~~~~~~~~

1. Tap the **red bin icon** on the top-right of any item tile.
2. Confirm deletion in the dialog that appears.

.. warning::
   Deleted items are permanently removed and cannot be recovered.

Adding an Outfit
~~~~~~~~~~~~~~~~

Outfits are created via the **floating navigation bar** at the bottom of the
screen, not from within the Outfits tab directly.

Editing an Outfit
~~~~~~~~~~~~~~~~~

1. Switch to the **Outfits** tab.
2. Tap any outfit card to open the outfit editor.
3. Make changes and save.

Deleting an Outfit
~~~~~~~~~~~~~~~~~~

1. Switch to the **Outfits** tab.
2. Tap the **red bin icon** on the bottom-right of the outfit card.
3. Confirm deletion when prompted.

.. warning::
   Deleted outfits are permanently removed and cannot be recovered.

Outfit Cards
~~~~~~~~~~~~

Each outfit card displays:

- **Name** of the outfit
- **Description** (if provided)
- A gold **Owned** badge if all items in the outfit are owned
- A horizontal scrollable strip of clothing items in the outfit

Error States
~~~~~~~~~~~~

================================= =====================================
Situation                         Message Shown
================================= =====================================
Items fail to load                ``Could not load items``
Outfits fail to load              ``Could not load outfits``
No outfits saved yet              ``No outfits yet``
Weather API key missing           Toast: ``Weather API key missing``
Weather fetch fails               Toast: ``Weather fetch failed``
================================= =====================================

----

Technical Documentation
-----------------------

Component
~~~~~~~~~

**File:** ``lib/Presentation/App/app_pages/wardrobe/wardrobe_page.dart``

**Class:** ``WardrobePage`` (``StatefulWidget``)

**Framework:** Flutter

**Backend:** Supabase via ``SupabaseWardrobeRepository``

Dependencies
~~~~~~~~~~~~

.. code-block:: dart

   import 'package:fitcheck/Data/repositories/supabase_wardrobe_repository.dart';
   import 'package:fitcheck/Data/services/weather_service.dart';
   import 'package:fitcheck/Presentation/App/app_pages/wardrobe/widgets/create_item.dart';
   import 'package:fitcheck/Presentation/App/app_pages/wardrobe/widgets/create_outfit.dart';
   import 'package:fitcheck/Presentation/App/app_style/widgets/floating_nav_bar.dart';

State Fields
~~~~~~~~~~~~

========================================= ===============================================
Field                                     Purpose
========================================= ===============================================
``_wardrobeRepository``                   Instance of ``SupabaseWardrobeRepository``
``_items``                                List of clothing items fetched from Supabase
``_outfits``                              List of outfits fetched from Supabase
``_isLoading`` / ``_isLoadingOutfits``    Loading state flags for items and outfits
``_error`` / ``_outfitsError``            Error strings for items and outfits
``_showOutfits``                          Toggles between Items and Outfits tab
``_searchController``                     Controller for the search bar input
``_searchQuery``                          Debounced lowercase search string
``_selectedWearTypes``                    Set of active wear type filters
``_selectedLayerCategories``              Set of active layer category filters
``_weatherService``                       Instance of ``WeatherService``
``_currentWeather``                       Map holding current weather data
``_recommendedTags``                      List of weather-derived clothing tags
========================================= ===============================================

Initialisation
~~~~~~~~~~~~~~

On ``initState()``, the following are called concurrently:

.. code-block:: dart

   _loadItems();
   _loadOutfits();
   _loadWeatherAndRecommend();

Data Loading
~~~~~~~~~~~~

=========================================== =======================================
Method                                      Description
=========================================== =======================================
``_loadItems()``                            Fetches clothing items from Supabase
``_loadOutfits()``                          Fetches outfits from Supabase
``_loadWeatherAndRecommend()``              Fetches weather via ``WeatherService`` and derives tags
=========================================== =======================================

Search and Filtering
~~~~~~~~~~~~~~~~~~~~

Search is debounced by 300ms using a ``Timer``. Filtering is applied via:

- ``_filteredItems()`` — filters ``_items`` by search query, wear type, and layer category
- ``_filteredOutfits()`` — filters ``_outfits`` by search query and wear type

Weather Tag Logic
~~~~~~~~~~~~~~~~~

Tags are derived from the ``WeatherService`` response inside
``_recommendTagsFromWeather()``:

.. code-block:: dart

   if (temp >= 25) tags.add('warm');
   else if (temp >= 15) tags.add('mild');
   else tags.add('cold');
   if (cond.contains('rain') || cond.contains('drizzle')) tags.add('rain');
   if (cond.contains('snow')) tags.add('snow');
   if (cond.contains('clear')) tags.add('clear');

Weather is currently hardcoded to London coordinates (51.5074, -0.1278).

Delete Flow
~~~~~~~~~~~

Both ``_deleteItem()`` and ``_deleteOutfit()`` follow an optimistic delete pattern:

1. Show confirmation dialog.
2. Immediately remove item from local list and call ``setState()``.
3. Call the repository delete method.
4. On success: reload the full list from Supabase.
5. On failure: re-insert the removed item at its original index and show error toast.

Sub-Widgets
~~~~~~~~~~~

=========================================== =======================================
Widget                                      Description
=========================================== =======================================
``_WardrobeItemsGrid``                      Stateless grid of item tiles; first tile is always the + add button
``_WardrobeOutfitsList``                    Stateless list of outfit cards with horizontal item strip
``CreateItem``                              Modal for adding or editing a clothing item
``CreateOutfitModal``                       Modal for adding or editing an outfit
``FloatingNavbar``                          Bottom navigation bar; triggers ``_loadOutfits`` on outfit creation
=========================================== =======================================

Known Issues / Notes
~~~~~~~~~~~~~~~~~~~~

- Weather coordinates are hardcoded to London. Future versions should use
  the device's actual GPS location.
- The ``OPENWEATHER_API_KEY`` must be set in the ``.env`` file or weather
  recommendations will not load.
