# Tetris AI Move Classifier

This project implements a Tetris gameplay simulator combined with a machine learning pipeline to classify Tetris moves as **good** or **bad** based on their impact on the board state. The goal is to use board heuristics and simulation data to train a model capable of evaluating the quality of Tetris piece placements automatically.

---

## Project Motivation

Tetris is a classic puzzle game where the player strategically places falling tetromino pieces to complete horizontal lines and clear them. The quality of each piece placement can vary widely — some moves clear lines or set up better future placements ("good moves"), while others create holes or uneven surfaces ("bad moves"). 

This project aims to:

- Simulate thousands of Tetris games with an AI that searches for the best moves using heuristics.
- Collect detailed data on board states and moves.
- Train a machine learning model to classify moves into good or bad based on this data.
- Provide insights and tools for AI-driven Tetris gameplay and move evaluation.

---

## How It Works

### Tetris Board Simulation

The core class `TetrisBoard` encapsulates the Tetris game logic:

- **Board Representation:** A 20-row by 10-column grid stores the current placement of blocks as a 2D NumPy array.
- **Piece Shapes:** Tetrominoes (I, O, T, S, Z, J, L) are represented as binary matrices.
- **Collision Checking:** Before placing a piece, the board checks if it fits without overlapping existing blocks or going out of bounds.
- **Piece Placement:** When a valid position is found, the piece's blocks are added to the board.
- **Line Clearing:** Completed horizontal lines are removed, and the rows above shift down. Scoring is updated accordingly.
- **Heuristic Evaluation:** The board is evaluated by calculating key features related to the board's shape:
  - **Aggregate Height:** Sum of heights of all columns.
  - **Bumpiness:** Sum of height differences between adjacent columns.
  - **Holes:** Empty spaces beneath blocks, detrimental to gameplay.
  - **Max Height:** Highest column on the board.
  - **Depth Above Holes:** Total depth of blocks above holes.

### Best Move Simulation

For each incoming piece:

- The AI generates all unique rotations.
- For each rotation, it tries placing the piece in every possible column.
- For each valid placement, it simulates the board state after placement and line clearing.
- A heuristic scoring function evaluates the board state, combining:
  - Reward for cleared lines (weighted heavily)
  - Penalties for aggregate height, holes, bumpiness, max height, and depth above holes
  - A small bonus for filling the bottom row nearly completely (to encourage compact stacking)
- The move with the highest heuristic score is chosen as the "best move."

### Data Collection and Labeling

- Moves are logged during simulation of multiple games.
- Each move is labeled:
  - **Good move:** clears one or more lines.
  - **Bad move:** clears zero lines.
- Board features and move statistics are recorded alongside the label.

### Machine Learning Model

- A **Random Forest classifier** is trained on the logged data.
- Input features include board heuristics and the number of pieces dropped so far.
- The model predicts whether a move is good or bad based on the input features.
- The dataset is split into training and test sets.
- Model performance is evaluated with metrics such as accuracy, precision, recall, F1 score, and ROC AUC.
- Visualizations include ROC curve and confusion matrix heatmap.

---

## Installation

Requires Python 3.7+ and the following libraries:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn

