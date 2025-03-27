import {
    Chess
} from "https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.13.4/chess.js";

const Game = new Chess();

let Stockfish = new Worker('stockfish.wasm.js');
Stockfish.addEventListener('message', handleStockfishMessage);
let lastEval = 0;
let bestMove = [];
let LastBestMove = [];
let currentEval;
function handleStockfishMessage(event) {
    const message = event.data;
    console.log(message); // Log the raw Stockfish output

    if (message.startsWith("info")) {
        // Extract and log the score from the info message
        const scoreMatch = message.match(/score (cp|mate) (-?\d+)/);
        if (scoreMatch) {
            const scoreType = scoreMatch[1]; // 'cp' for centipawns, 'mate' for mate-in-N
            const scoreValue = parseInt(scoreMatch[2], 10);

            if (scoreType === "cp") {
                currentEval = scoreValue;
            }
        }
    }

    if (message.startsWith("bestmove")) {
    const bestMoveMatch = message.match(/bestmove (\S+)/);
    if (bestMoveMatch) {
        const bestMove = bestMoveMatch[1];
        const source = bestMove.slice(0, 2);
        const target = bestMove.slice(2, 4);
        highlightLastBest(source, target);
    }
}
}

const Options = {
    position: "start",
    draggable: true,
    onDragStart: onDragStart,
    onDrop: onDrop,
    onSnapEnd: onSnapEnd,
    size: Math.min(window.innerHeight, window.innerHeight) - 20
};

const Board = Chessboard("board", Options);

function onDragStart(source, piece) {
    if (Game.game_over()) {
        return false;
    }

    if ((Game.turn() === 'w' && piece.search(/^b/) !== -1) || (Game.turn() === 'b' && piece.search(/^w/) !== -1)) {
        return false;
    }
}

function onDrop(source, target) {
    const move = Game.move({
        from: source,
        to: target,
        promotion: 'q'
    });

    if (move === null) {
        return 'snapback';
    }

    updateStatus();
    updateStockfish();
}

function onSnapEnd() {
    Board.position(Game.fen());
}

function updateStatus() {
    // Calculate the evaluation change in centipawns
    let evalChange = (currentEval - lastEval); // Convert to centipawns
    console.log(evalChange)

    // Classify the move based on the evaluation change
    if (evalChange <= 0) {
        console.log("best");
    } else if (evalChange > 0 && evalChange <= 10) {
        console.log("excellent");
    } else if (evalChange > 10 && evalChange <= 20) {
        console.log("good");
    } else if (evalChange > 20 && evalChange <= 50) {
        console.log("inaccuracy");
    } else if (evalChange > 50 && evalChange <= 150) {
        console.log("mistake");
    } else if (evalChange > 150) {
        console.log("blunder");
    }
    
    lastEval = currentEval;
    
}

function resizeBoard() {
    const newSize = Math.min(window.innerWidth, window.innerHeight) - 20;
    Board.resize(newSize); // Resize the board dynamically
}

resizeBoard();

setTimeout(function() {resizeBoard()}, 5000);

function highlightLastBest(source, target) {

    const to = document.querySelectorAll(`[data-square=${target}]`);
    const from = document.querySelectorAll(`[data-square=${source}]`);
    console.log(from);
    console.log(to);
    LastBestMove = [source, target];
}

function updateStockfish() {
    Stockfish.postMessage("position fen " + Game.fen());
    Stockfish.postMessage("go depth 15");
}
