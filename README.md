#RULES OF THE GAME

Setup
- Use rectangular domino tiles marked with 0-6 spots on each end.
- Each tile combination appears once in the set.
- Each player receives 7 tiles. The starting player is chosen randomly.

Gameplay
- The starting player places any tile to begin the chain.
- Players take turns adding a tile to either end of the chain, matching spots.
- Doubles (same number on both ends) are placed perpendicular to the chain.
- If a player cannot play, they draw tiles until they can play or no tiles remain. If no tiles are left and they cannot play, they skip their turn.
- Players score when the sum of the spots on the chain ends is divisible by five, earning points equal to that sum. Doubles count both ends for scoring.

Round End
- A round ends when a player plays their last tile, winning the round and scoring the sum of the opponent's remaining spots, rounded to the nearest five.
- If no tiles remain and no moves are possible, the player with fewer spots wins the round and scores the difference between the opponent's and their own remaining spots, rounded to the nearest five.
- The round winner starts the next round.

Winning the Game
- The game continues until a player reaches 100 points.


#MODEL CONSTRUCTION AND TRAINING

Introduction
Winning at dominoes is not just about scoring points; it also involves blocking your opponent, avoiding blocks yourself, and preventing the opponent from scoring. This complexity is heightened by the limited information available to each player: they only know their own tiles and the ones already placed on the table. The challenge, then, is to develop a model that can navigate this intricate decision-making environment effectively.

Initial Setup
To begin, I coded a playground to facilitate gameplay. This included essential mechanics such as shuffling the tiles, distributing them to players, making moves, drawing tiles, and tracking points, round wins, and game wins. Additionally, I implemented a "random player" that makes random legal moves to serve as a baseline opponent.

Reward Structure
Next, I designed a reward structure to promote good moves. Players earn rewards for scoring points, winning rounds (as a small reward for all moves contributing to winning the round), forcing the opponent to draw, and maintaining a diverse set of spots to reduce the likelihood of drawing. Conversely, players receive negative rewards if the opponent earns points or wins the round (as a small punishment for all moves contributing to losing the round). 

Although this reward structure is not perfectly correlated with winning games, it encapsulates the key aspects of good play. I found that giving rewards only for winning games, even with a near-one discount factor, did not yield models as well-trained as the one with the outlined guidelines.

Model Architecture
I constructed a fully connected neural network with ELU activation functions. The architecture consists of an input layer for game observations, followed by two dense layers with 100 neurons each. I included a legal action mask to ensure only valid moves are considered, and the output layer uses a softmax function to provide move probabilities. Despite testing networks with more hidden layers, this shallow network performed best, although further testing could explore longer training.

Training Procedure
As the network parameters are initialized randomly, it should initially win approximately 50% of games against the dummy "random player." I trained the network using a policy gradient approach with a 10% dropout rate for regularization. Model updates were based on the normalized gradients of moves made in four games, with a tiny learning rate of 0.0025 to allow the network to learn effectively.

Initially, I trained the network by playing against the "random player." Once the network reached a certain efficiency, I had it play against itself. These two steps were repeated multiple times. Training against itself allows the network to learn how to play against a strong player making rational moves, while training against the "random player" helps escape local minima. Repeating these training steps multiple times produced a strong final algorithm, which on average wins 82.3% of games against the "random player."

Analysis and Future Improvements
An analysis of the games lost by the model revealed that these losses were often caused by chance events, such as being blocked and forced to draw. This analysis also suggested further improvements to the reward structure, such as teaching the algorithm to prioritize blocking the opponent over earning points if the opponent has few dominoes left. Additional rules of thumb could also be implemented, such as placing a domino on the same end of the chain as the opponent to increase the likelihood that the opponent draws.
