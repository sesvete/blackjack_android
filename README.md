# Blackjack android app

Newest app release is available under Releases:
https://github.com/sesvete/blackjack_android/releases

Written in Java, using Android dev studio

Cards are acquired through the deck of cards api:
https://www.deckofcardsapi.com/

The app uses the following libraries:
1) glide - image loading and caching library
2) volley - HTTP library for sending API requests
3) gson - for parsing JSON

The app has three activities

## Login activity

login into an existing account
The layout file uses the constraint layout

Existing accounts are displayed in a list. A recyclerview is used for the list.
The list displays local accounts with their points.
All user information is saved in a SQLite database.

The database has the following structure:
USER_ID INTEGER PRIMARY KEY AUTOINCREMENT,
USER_NAME TEXT,
USER_POINTS INTEGER

Long click on the list entry, deletes the selected entry.

if we don't have an account, we can create a new one through the new user activity

Clicking on an existing account leads us to the game activity. User ID of the selected account is passed through the intent extra.

## New user activity

responsible for creating new accounts
The layout file uses the constraint layout

Before the account is created, the app check if an account with the inputted name already exists.
On account creation we are returned to the login activity, where the list is updated with the created account.

## Game activity

The activity where the game is played
The layout file uses relative layout.
All interaction with the database is done through the user id.

Top of the app displays the user point total, below is the space for both players hands. At the bottom of the app is the place for the buttons.
The hands are displayed in form of a list - a card view widget is used.
The cards are displayed in a grid up to 4 horizontally.

If the player presses STOP we return to the login screen.

When we start the game the following procedure is initiated:
1) all variables are reset to default values (empty hand, total sum of hands is 0 etc.)
2) we shuffle and get a new deck - for simplicity sake we get a new deck each time we start a new game (shuffleDeck)
3) the player and dealer are each dealt 2 cards. Dealer's second card is hidden
4) the buttons are changed from START/STOP to HIT/HOLD - their functions are also changed accordingly
5) The value of the player hand is checked. If the player has natural Blackjack we proceed to game resolution

If the player presses hold:
1) Dealer's second card is revealed
2) Dealer's total is checked - if it is lower than the player's and lower than 18, dealer draws a card - this is repeated until either of the statements is false
3) game proceeds to game resolutions where the winner is determined
4) points in the database are updated
5) the buttons are changed from HIT/HOLD to START/STOP - their functions are also changed accordingly

If the player presses hit:
1) Player draws a card
2) Player's card total is checked (if it's greater or equal to 21 the game automatically proceeds to game resolution)

The game proceeds to game resolution either if the player presses hold or if the players total car value >= 21 
On game resolution:
1) Dealer's second card is revealed
2) If needed dealer draws additional cards (look hold button)
3) the winner is determined
4) points in the database are updated
5) the buttons are changed from HIT/HOLD to START/STOP - their functions are also changed accordingly

## Required methods

### Database helper
addToDB(User user) - adds an item to the database
getList() - gets an array list of the database items
checkDB(String userName) - checks for existing entry in the database
getPointsFromDB(int id) - retrieves points value of the user from the database
savePointsToDB(int points, int id) - updates points value of the user
deleteFromDB(int id) - deletes user from the database

### Game activity
shuffleDeck(String shuffleUrl, Context context, ShuffleCallback callback)
drawCard(String deckID, String numberDrawn, ArrayList<Card> hand, boolean isPlayer, Context context, DrawCardCallback callback)

A callback is used so that the request can be done asynchronously.

updateCardSum(String cardValue, int totalSum) - updates the total card value of either the player or dealer
updatePoints(boolean playerWon, Context context, int id, TextView txtTotalPoints) - updates the player points in the database
gameResolution(@NonNull Button btnStart, @NonNull Button btnStop, Context context, int id, TextView txtTotalPoints)

