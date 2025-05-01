# Pacman
Game
const levels = [
  // Level 1: Easy
  [
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
    1,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,1,
    1,0,1,1,1,1,0,1,0,1,0,1,1,1,1,0,1,1,0,1,
    1,0,1,0,0,0,0,1,0,1,0,1,0,0,0,0,1,0,0,1,
    1,0,1,0,1,1,0,1,0,1,0,1,0,1,1,0,1,0,1,1,
    1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1
  ],
  // Level 2: Narrower paths
  [
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
    1,0,0,1,0,0,0,1,0,1,0,0,0,1,0,0,1,0,0,1,
    1,0,1,1,0,1,0,1,0,1,0,1,0,1,0,1,1,0,1,1,
    1,0,1,0,0,1,0,0,0,1,0,1,0,0,0,1,0,0,0,1,
    1,0,1,0,1,1,1,1,1,1,1,1,1,1,0,1,0,1,0,1,
    1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1
  ],
  // Level 3: More walls, tighter corners
  [
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
    1,0,0,1,0,0,0,0,1,1,1,0,0,0,0,1,0,0,0,1,
    1,1,0,1,1,1,1,0,0,0,0,0,1,1,1,1,0,1,1,1,
    1,0,0,0,0,1,1,1,1,0,1,1,1,1,0,0,0,1,0,1,
    1,0,1,1,0,0,0,0,0,0,0,0,0,0,0,1,1,0,0,1,
    1,0,0,0,0,1,1,1,1,1,1,1,1,1,0,0,0,0,0,1,
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1
  ],
  // Level 4: Add ghost
  [
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
    1,0,0,0,0,1,0,0,1,1,1,0,0,1,0,0,0,0,0,1,
    1,1,1,1,0,1,1,0,0,0,0,0,1,1,0,1,1,1,1,1,
    1,0,0,0,0,0,0,0,1,1,1,0,0,0,0,0,0,0,0,1,
    1,0,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,0,1,
    1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1
  ],
  // Level 5: Final maze — very dense
  [
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
    1,0,0,1,0,1,0,0,0,1,0,0,0,1,0,1,0,0,0,1,
    1,0,1,1,0,1,1,1,0,1,0,1,1,1,0,1,1,1,0,1,
    1,0,1,0,0,0,0,1,0,0,0,1,0,0,0,0,0,1,0,1,
    1,0,1,0,1,1,0,1,1,1,1,1,0,1,1,0,1,1,0,1,
    1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,
    1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1
  ]
];
let currentLevel = 0;
let pacmanIndex;
let ghostIndex;
let ghostTimer;
let score = 0;
function loadLevel(levelIndex) {
  board.innerHTML = "";
  cells.length = 0;

  const layout = levels[levelIndex];
  layout.forEach((type, i) => {
    const cell = document.createElement("div");
    cell.classList.add("cell");

    if (type === 1) cell.classList.add("wall");
    else if (type === 0) cell.classList.add("dot");

    board.appendChild(cell);
    cells.push(cell);
  });

  pacmanIndex = layout.indexOf(0) + width; // start near top
  cells[pacmanIndex].classList.add("pacman");

  if (levelIndex >= 3) {
    ghostIndex = layout.lastIndexOf(0) - width;
    cells[ghostIndex].classList.add("ghost");
    ghostTimer = setInterval(moveGhost, 500);
  }
}
function movePacman(e) {
  cells[pacmanIndex].classList.remove("pacman");
  let nextIndex = pacmanIndex;

  switch (e.key) {
    case "ArrowLeft":
      if (pacmanIndex % width !== 0) nextIndex -= 1;
      break;
    case "ArrowRight":
      if (pacmanIndex % width < width - 1) nextIndex += 1;
      break;
    case "ArrowUp":
      if (pacmanIndex - width >= 0) nextIndex -= width;
      break;
    case "ArrowDown":
      if (pacmanIndex + width < cells.length) nextIndex += width;
      break;
  }

  if (!cells[nextIndex].classList.contains("wall")) {
    pacmanIndex = nextIndex;

    if (cells[pacmanIndex].classList.contains("dot")) {
      cells[pacmanIndex].classList.remove("dot");
      score++;
      scoreDisplay.textContent = score;

      if (!cells.some(c => c.classList.contains("dot"))) {
        // Clear ghost timer
        if (ghostTimer) clearInterval(ghostTimer);

        currentLevel++;
        if (currentLevel < levels.length) {
          setTimeout(() => {
            alert(`Level ${currentLevel} complete!`);
            loadLevel(currentLevel);
          }, 500);
        } else {
          setTimeout(() => {
            alert("You Win!");
            document.removeEventListener("keydown", movePacman);
          }, 500);
        }
      }
    }
  }

  cells[pacmanIndex].classList.add("pacman");

  if (ghostIndex === pacmanIndex) {
    gameOver();
  }
}
function moveGhost() {
  const directions = [-1, 1, -width, width];
  let dir = directions[Math.floor(Math.random() * directions.length)];
  let next = ghostIndex + dir;

  if (!cells[next].classList.contains("wall")) {
    cells[ghostIndex].classList.remove("ghost");
    ghostIndex = next;
    cells[ghostIndex].classList.add("ghost");

    if (ghostIndex === pacmanIndex) gameOver();
  }
}

function gameOver() {
  alert("Game Over!");
  document.removeEventListener("keydown", movePacman);
  if (ghostTimer) clearInterval(ghostTimer);
}
loadLevel(currentLevel);
document.addEventListener("keydown", movePacman);
