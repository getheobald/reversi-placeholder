# Reversi
This repo is a placeholder for this project because the code is private at the professor's request to protect intellectual property. Please reach out at gracelyntheobald@gmail.com to view.
## OVERVIEW
This is a codebase for playing a game of Reversi, or Othello, on a hexagonal board rather than the traditional square. 
The code supports both human vs human and human vs computer play, as well as supervised computer vs computer play. 

It is playable both in textual form in an IDE and through clicking via a GUI in a .jar file. 
The code is designed to be modular and extendable for custom functionalities and rules alterations. 

The game is playable by someone with minimal technical experience, and provides a fun and challenging diversion for anyone who likes a little strategy game!

### QUICK START 
To run using the reversi.jar file, download the jar and navigate to it in the terminal, then run "java -jar reversi.jar ___ ___" where ___ represents one of "human", "easy", "medium", or "hard". Human will allow a player to play the game through the GUI, and the other three will create AI players of varying difficulty levels which can match up against either players or other AI. 

In IntelliJ, to display and interact with the board, navigate to the Reversi file and run the main() method. 
This will create a new Swing window in which the reversi board will be displayed.

You can use your mouse to select and deselect tiles, hit ENTER to place a disc in a selected tile, press SPACE to pass your turn, and press Q to quit. 
If you're ever not sure of whether you're able to make a move, pressing H will let you know if you have moves available!
You will need to acknowledge that it's the AI's turn each time it runs, as a way of slowing down the pace of the game when AI is involved - it felt too snappy. 

### KEY COMPONENTS
**Model** contains the implementation and documentation of the model. 
The model holds the logic for running a game of Reversi - making the board, players taking turns, and performing operations on the board such as flipping tiles. 
It's organized into two interfaces - PlayerActions and ReversiModel -  which are both implemented by a model class, BasicReversi.
PlayerActions is extended by ReversiModel in order to ensure that a ReversiModel always has access to the moves required to play the game.

**Controller** contains the controller implementation as well as the interface and classes which enable players (both human and AI) to be created.
Alongside the controller come two Listener interfaces, ModelEventListener and ViewEventListener. 
These interfaces are what enable communication between our model, controllers, and views. 
The ReversiController class implements both of these.  

**Strategy** contains the implementation of the strategies that the AI players use. 
Individual strategies take in and output sorted lists of Coordinates that represent potential moves, and can be chained together using CompositeStrategies. 
The final results of the CompositeStrategy (or the individual strategy) can be submitted to a Player, which understands that the first item in the list is the most preferred move for the strategy it's employing. 

**View** contains the gui and text packages, which represent a GUI visual representation and a textual representation of a game of Reversi.

GUI View
- The GUI is the primary way for a player to interact with a game of Reversi via Java Swing. 
- It consists of a Panel within a Frame, which the player can click and interact with. Hexagonal tiles are arranged according to a specified board size and tile radius, and discs can be placed within tiles. 
- To interact with the board, players use their mouse to select and deselect tiles and their keyboard to move in selected tiles, pass, and quit the game.

Textual View
- Given any game state, it is able to produce a rudimentary image of the board as a string OR as an appendable. 
- Given the existence of a working GUI this is pretty much depreciated, but can still be interesting to get a sense of how the board looks in a simple format.

### KEY SUBCOMPONENTS
Within the model, the most important subcomponents are the board and Tile, Coordinate, and PlayerColor classes.

The PlayerColor enum represents the players involved in the game and is the basis for how much of the game is run.
It contains two values, BLACK and WHITE, and is used to track turn-taking using its ordinal index values and determines the contents of tiles.

Coordinates are positional representations of Tiles on a hexagonal board, using the axial coordinate method. 
They contain a q and r value, which together can be used to conceptually represent the third value s required to have an effective hexagonal mapping system.

Tiles are the spaces on the board, which can be empty or hold discs in them. 
There is no actual disc object - instead, the Tile tracks the state of the disc within it in order to reduce code complication. 
These Tiles also each hold an axial coordinate to track their position on the board.

Tiles all share some public methods which are used by the model to manipulate the board.<br>
The placeDisc(PlayerColor) method changes the contents of the Tile to reflect a Tile being put down.<br>
The Flip() method changes the contents of the Tile from white to black, and vice versa.<br>
The isEmpty() method returns true if the Tile has no disc in it, and false if it has a disc. <br>
The getContents() method returns the color of the disc currently held in the Tile.<br>
Finally, the getCoordinate() method returns the Coordinate location of the Tile.<br>

The board is a 2D array of Tiles, with null placeholder values in the top left and bottom right corners to create a hexagonal structure. 
The Tiles' r value is the row it occupies in the array, and the q value is its position in its respective inner array. 
This system of holding coordinate values of Tiles allows for easy and efficient access to the board's fields, simplifying the operations required to run a game of Reversi.

The model accesses and changes the board with a number of public methods. <br>
From the PlayerActions interface, move(Coordinate) and pass() represent the player's options when playing the game. <br>
Move places a tile on the board and handles the consequences of that move (Tiles flipping), while pass simply skips the current player's turn.<br>
GetCurrentPlayer() returns the color of the current player. <br>
IsGameOver() returns a boolean based on the current state of the game - if it's ongoing, false, if it's over, true.<br>
GetCurrentWinner() returns the player with the highest score. <br>
GetPlayerScore(PlayerColor) returns the score (number of tiles on the board) of the given color.<br>
GetTileAt(Coordinate) returns the board space of the given coordinate. <br>
GetBoardSize is a getter for the private final boardSize variable which we use for a number of our calculations when looking at the Tile array.

In the controller, the most important assets are the Pair class, which allows for easy communication between a player and its controller, and the Listener interfaces which enable communication between the controller and the model/view. 

In the GUI view, the most important subcomponents are ViewableTiles, the custom Path2D classes Hexagon2D and Circle2D, as well as the Frame->Panel structure. 

ViewableTiles are GUI representations of the Tiles held in the model. When one is constructed, it creates a hexagon and a circle using Hexagon2D and Circle2D (see below), and holds them internally to act as its border and the border of its disc, if it ever gets one.
The ViewableTile knows how to draw itself, and always draws the hexagon before the circle, which means that there is no possibility of a disc being covered accidentally. 
ViewableTiles also hold information about their state, such as their logical coordinate and their physical coordinate, which makes them easy to interface with for the rest of the view. 

Hexagon2D and Circle2D are simple function objects which create versions of their respective shapes for ViewableTiles to hold. 
Both of them are drawn from the center of the shape rather than the top left, as is standard, because the physical coordinates of our ViewableTile objects are also held at the center. 
This makes drawing these custom objects easy for the ViewableTile!

### SOURCE ORGANIZATION
The code is held in the cs3500.reversi folder, which is split into src (source code of the model) and test (a test directory for validating classes and methods). 
Src has four parts: controller, model, strategy, and view. 

Controller contains the interfaces for Player and both Listeners, as well as the implementation of all four kinds of Player, Pair and MoveType, and the Controller itself.

Model contains the PlayerActions and ReversiModel interface, both of which dictate the methods in the BasicReversi class. 
Additionally, interfaces and implementations of Tile, Coordinate, and PlayerColor are held in this directory. 

Strategy contains the implementation of our AI strategies, as well as the HumanPlayer class, which pass information from the view to the controller.

View contains two packages: gui and text.
<br>GUI contains the ViewPanel, ViewableReversiTile, and PanelEventListener interface.
There are some supporting classes with no interfaces, such as Circle2D and Hexagon2D, which are used to draw shapes in the panel, and then implementations of the aforementioned interfaces: ViewableTile, ReversiFrame, and ReversiPanel. 
<br>Text contains the TextualView interface and ReversiTextualView, the implementation of the view. 

The test directory contains separate test classes for each class in the src folder.

## CHANGES FOR PART 2: 
Task: explain what functionality was missing, and why, and how you chose to add it.

BasicReversi
- Added the isMoveLegal and currentPlayerHasLegalMoves methods to allow the view and controller to be able to externally check move legality. 
Initially we were worried about using public methods in other public methods, and didn't want to duplicate code, so we didn't make public legality checks - but the view needed them, so here we are.
The currentPlayerHasLegalMoves method is as of right now unused, but as it may be useful down the line we're keeping it implemented but inactive.
- Added an isEmpty check to getValidRows for edge cases - a testing case for the view made us realized that we didn't check that the contents of a tile were null when finding valid rows, only if the tile itself was null. We have since remedied that.
- Added board copying functionality and a third constructor that takes in a board. Right now we're not using it for anything, but there are a number of cases in which it may prove useful to have.
- Added the getMoveScore method to easily check the score of a potential move, allowing for far easier implementation of our strategies and opening up pathways to interesting enhancements later down the line.

ReversiModel
- We renamed our two interfaces and changed which one implemented the other.
Initially we had the read-only model implement the move interface, because we felt that the getters were "closer" to the model. 
Now, though, since we want to be able to make a read-only and an active model, we switched the order.
- Improved documentation.

Coordinate
- Added the toString method for testing purposes. 
- Added getters for q and r, which are now private fields. They were public before for ease of checking, and since they were final we assumed it was fine to have them be public, but in all honestly it was just an oversight.
- Explained coordinate system in more detail in the javadoc.

Other
- Deleted packagePrivateTests class, as we were not using it.
- Added ReadOnlyReversiModel and changed textual view to take a read-only model instead of a normal ReversiModel.
- Deleted PlayerActions interface, because it was replaced by ReversiModel once ReversiModel was replaced by ReadOnlyReversiModel.

ReversiTile
- Improved documentation.

Tile
- Improved documentation.

### EXTRA CREDIT:
- We implemented two of the three extra credit strategies, AvoidCornerAdjacentStrategy and CornerCaptureStrategy. You can check them out in the strategy package and see the tests showing their difference from our base maximum capture strategy in test.StrategyTests.

### GUI DESIGN CHOICES
<b>How the board is created:</b>
- The setTilePositions method creates and adds a board of ViewableTiles to a hashmap of tiles. 
Each ViewableTile has a hexagon which is determined by the values passed into its constructor through this method, and knows how and where to draw itself.
Thus, once this method is called, the board can be drawn simply by telling each tile to draw itself.
- The row for loops moves from 0 to negative half of board size (which is a variable representing the number of tiles in the center row), in order to represent half a board - the top row is 0 to half of the board size, the next row is -1 to half the board size, and so on until the center row which should be the full board size.
- There are two separate for loops in the row loop, one to draw the top half of the board (including the center row) and another to draw the bottom half (without the center row).
- Inside it, index is based on row, and moves from the value of row to half the size of the board. This will always generate the correct amount of tiles for that row.
- There are two tile additions here, one locked behind a for loop - the former is for drawing the top half of the board from the top down, and the second is for drawing from the bottom up. 
The latter does not draw the center row, hence the if statement.
- Based on the horizontal center of the board, we offset by the correct amount based on the index, the tile width, and the row number.
- Then, based on the vertical center of the board, offset by the correct amount based on the row number. As we don't need to do the offset calculations, this is a lot simpler than calculating x.
- The radius is simple to pass to the ViewableTile, but the q and r values are less so. 
- Since q is offset to the right in the top half of the axial array, the range of values starts at its smallest at the top and increases as we go down. Since index gets more negative over time, halfBoard-index does that.
- r starts at 0 and goes up, whereas row starts at 0 and goes down. This means that getting the inverse of row is equivalent to getting the r-value for a top-half tile.
- For the bottom rows, a few things need to change. We do the same horizontal and radius calculations as before, but the rest is different.
- We have a slightly different y-calculation - it adds the value rather than subtracting it to place the row further down the frame, as (0,0) is in the top left.
- Since q is offset to the left in the bottom half of the axial array instead of the right as in the top half, the range of values must stay consistent on the left and increase as we go up. Index and row are always going to be the same on the first run of the inner for loop, but as it continues index will increase and the offset from row will stay the same. Thus, q is equal to index + the absolute value of row.
- r has to start at boardSize-1 and go down - as the row loop continues, it will decrease over time, so adding it allows us to build from the bottom up. 
- When each tile is created, it is added to the tile list hashmap.
- And there we are! A completed board held in a hashmap of new viewable tiles ready to display a game of Reversi.

<b>How we interact with the board as users:</b>
- Clicking a tile selects it. When a tile is selected, you can deselect it by clicking it again, clicking another tile (which then selects that tile instead), or clicking outside of the board on the background.
- When you have a tile selected, pressing RETURN places a tile there. At the moment only black tiles can be placed using this functionality, but a controller will be able to pass in colors to ensure the discs are the correct color.
- At any time, pressing SPACE passes the turn. 
- At any time, pressing Q quits the game.
- Additionally, for testing purposes (these will be removed once we have rules implementations), W and B place white and black tiles respectively in order to easily visually set up interesting board scenarios.

<b>Other:</b>
- We hard code a specific radius value right now, but the goal is that a user will be able to pass in a desired radius size through command line arguments in order to customize the size of the board.
This means that the board size will be highly customizable, and makes our calculations easier!

## CHANGES FOR PART 3

Interface changes
- Renamed the ViewPanel interface to the Emitter interface. Previously, it contained one method, addPanelListener(), which we changed to addListener(). This is so that we can now use this as a generic interface for any class that has listeners it wants to notify of events.
- Similarly, renamed PanelEventListener to ViewEventListener. For the previous assignment, we had a PanelEventListener and a ViewEventListener, implemented by the view and the controller (in theory), respectively. However, now that we are sure the panel and the view are passing along exactly the same information, we know that the controller and the view can both be ViewEventListeners and there's no need to distinguish the two.
- ReversiFrame now implements Emitter so that it is able to emit events to the controller.
- ReversiFrame now implements ModelEventListener (see below) so that the model is able to tell it when a turn has been made so that the view can display the updated game state appropriately.

New interfaces
- Created a TurnTaker interface which is now implemented by the model, BasicReversi. It is the more specific model version of the Emitter interface. Similar to Emitter, it contains addListener(), so the model can add listeners, but it also has updateTurn() to notify listeners when a turn has ben taken.
- Created a ModelEventListener interface. Since the model emits different kinds of events than the view, the controller needs different methods to be able to listen. ModelEventListener contains the methods initializeGame() and updateTurn(), which must be implemented by all listeners of the model, which is how the model is able to notify them.
- Created ReversiView which is now implemented by ReversiFrame, the class for the view/window. It contains one simple method, displayPopup(), which displays a popup window. This gives other classes the ability to send a message they want displayed, via the view.

BasicReversi (model)
- Added a simple getter method, getBoard(), which uses the private helper copyBoard() to make a copy of the board field and return it. This is used in ReversiPanel to get the state of the board whenever it needs to be drawn. This method was also added to the model interface, ReversiModel.
- Changed the getCurrentWinner() method to return integers instead of PlayerColors (or null). This separates the meaning a bit by having integers arbitrarily represent colors, but it also makes it cleaner by eliminating null and the need for null checks. 0 represents black, 1 represents white, and 2 represents a tie. This makes it substantially easier for the controller to determine the current winner.

ReversiPanel
- paintComponent()
  - Every time repaint() or paintComponent() is called, the entire board is redrawn. We use getBoard() from the model to get the current state of the game and loop through each tile to draw it (and the disc on it, if one exists). In part 2, we simply iterated through the tileList field in the panel and had each tile draw itself, but the view wasn't updating correctly once we had two views operating. It is more foolproof to get the information directly from the model.
- keyPressed()
    - Added an option to press the h key as a "help" button to check if you have legal moves. Gives the user more options and makes testing easier.
    - Added images that correspond with each type of window/message.
    - Instead of calling notifyMoveMade() in the if statements, we separate its return value into an integer to make the code cleaner.
- Other
  - Changed the name of notifyMoveMadeAndCheckValidity() to notifyMoveMade()

Panel and View Listener Fields
- Changed the structure of listeners to both the panel and the view. Since these objects will only ever have one listener (the panel only emits to the window/view that holds it, and the window/view only emits to its controller), it doesn't really make sense to use a list of listeners, which we had in part 2. Originally, we changed this structure to keep a list of listeners and simply only ever use the first element in the list, but that is bad design. Instead, we changed both classes' fields to an Optional<ViewEventListener>, which is initially empty and is then set once the view adds itself as a listener to the panel/the controller adds itself as a listener to the view. This way, there is clearly only one listener object.

### USER INTERACTION DESIGN CHOICES
- Each player gets their own game ended dialog because we want each user to be notified when the game is over.
- We customized our popups!
  - We gave different types of messages different types of buttons. For example, the "It's your turn" popup only gives the user an "Ok" button, because all they have to do is acknowledge that they've seen the message. Attemping to quit the game, however, shows a popup with "Yes" and "No" options so that the user can choose to either go ahead with quitting or go back to the game. This gives the user more options and control over game play.
  - We added custom icons. Each message type has a different icon depending on the message content. This was to make gameplay more fun and interesting!
