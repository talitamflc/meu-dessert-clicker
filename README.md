Dessert Clicker app
=====================

Code for Android Basics with Compose Codelab.

Introduction
------------

Dessert Clicker is a game about making desserts.

Press the button, make a dessert, earn the big bucks.

You use this app in the course to explore the Android lifecycle and log messages to
the Android console (Logcat).

Pre-requisites
--------------

You need to know:
- How to open, build, and run apps with Android Studio.
- What an activity is, and how to create one in your app.
- What the activity's onCreate() method does, and the kind of operations
  that are performed in that method.


Getting Started
---------------

1. Download and run the app.


My Solution
---------------

Resume: I created a ViewModel and UiState. In MainActivity I just call those.

If you want to check the Android Solution, 
look here: https://github.com/google-developer-training/basic-android-kotlin-compose-training-dessert-clicker


Here are some comments to you know what happen:
---------------

1. DessertClickerViewModel.kt

- line 9: class DessertClickerViewModel : ViewModel()
  Creates the class extending ViewModel.

- DessertClickerViewModel
  It centralizes the screen state and the main logic of the game.

- line 11: private val _uiState = MutableStateFlow(DessertUiState())
  A StateFlow can be exposed from the DessertUiState so that the composables can listen for UI state updates and make the screen state
  survive configuration changes.
  It needs to use private because in Kotlin it is default public.

- _uiState
  Internal mutable state controlled by the ViewModel.

- line 13: val uiState: StateFlow<DessertUiState> = _uiState.asStateFlow()
  Now _uiState is accessible and editable only within the DessertClickerViewModel.
  The UI can read its value using the read-only property uiState.

- uiState
  Read-only version observed by the interface UI.

- asStateFlow()
  The asStateFlow() makes this mutable state flow a read-only state flow.

- line 31: init{}
  This init part is for showing the first state values of the list, so when using this, it is not necessary to use currentDessertPrice = 5 in
  DessertUiState.

- line 36: determineDessertToShow()
  Decides which dessert should be shown based on quantity sold.
  The list of desserts is sorted by startProductionAmount. As you sell more desserts, you'll start producing more expensive desserts as
  determined by startProductionAmount.
  We know to break as soon as we see a dessert whose startProductionAmount is greater than the amount sold.

- line 52
  It was necessary to create this function to move the clickable logic to the ViewModel.

- onDessertClicked()
  Reads the current state, calculates the new values, and updates the state in an immutable way.

- val currentState
  Current state of the UI, before the click.

2. MainActivity.kt

- line 159: fun DessertClickerApp()
  Connects the UI with the ViewModel, looks at the uiState, and passes the current data of the screen.

- dessertClickerViewModel: DessertClickerViewModel = viewModel()
  Gets the ViewModel instance used by the composable.

- line 163: collectAsState()
  Transforms the ViewModel's StateFlow into observable state for Compose.
  The collectAsState() function collects values from this StateFlow and represents its latest value via State.

- line 189: DessertClickerScreen() called
  Displays the current values of the image, revenue, and sold with the ViewModel.

- line 193: parameter onDessertClicked
- onDessertClicked = { dessertClickerViewModel.onDessertClicked() }
  Event sent by the UI when an image of the dessert is clicked.

- line 230: fun DessertClickerScreen()
  It is necessary to use parameter onDessertClicked: () -> Unit to use in DessertClickerScreen() called in line 193.

- line 256
  Calls onDessertClicked from the ViewModel in clickable Image.

- line 270: fun TransactionInfo()
  Gets the information of the desserts sold and total revenue sold.

- line 292: fun RevenueInfo()
  Shows the total revenue.

- line 312: fun DessertSoldInfo()
  Shows how many desserts were sold.

3. DessertUiState.kt

- DessertUiState
  Created this to update UiState.

- state variables
  The variables need to start with the values from the start, based on dessertList.

- @DrawableRes val currentDessertImageId: Int = R.drawable.cupcake
  Defines the initial image shown on the screen.

- val currentDessertPrice: Int = 0
  If init is not used in the ViewModel, put 5 in place of the initial value.

4. build.gradle.kts(module:app)

- line 66: val lifecycleVersion = "2.5.1"
  If you need create viewmodel, take this variable to call viewmodel in dependencies
- line 78: implementation("androidx.lifecycle:lifecycle-viewmodel-compose:${lifecycleVersion}")