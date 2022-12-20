<script>
  let cueBall = { x: 50, y: 50, color: "white" };
  let objectBall = { x: 450, y: 50, color: "yellow" };
  let pockets = [
    { x: 25, y: 25 },
    { x: 475, y: 25 },
    { x: 25, y: 475 },
    { x: 475, y: 475 },
  ];
  let cueAngle = 0;

  function shootCueBall() {
    // Calculate the direction and velocity of the cue ball based on the angle of the cue stick
    let direction = {
      x: Math.cos(cueAngle * (Math.PI / 180)),
      y: Math.sin(cueAngle * (Math.PI / 180)),
    };
    let velocity = 10;

    // Animate the movement of the cue ball
    let interval = setInterval(() => {
      // Check if the cue ball has hit a wall
      if (cueBall.x < 20 || cueBall.x > 480 || cueBall.y < 20 || cueBall.y > 480) {
        direction.x = -direction.x;
        direction.y = -direction.y;
      }

      cueBall.x += direction.x * velocity;
      cueBall.y += direction.y * velocity;

      // Check if the cue ball has hit the object ball
      let distance = Math.sqrt(Math.pow(cueBall.x - objectBall.x, 2) + Math.pow(cueBall.y - objectBall.y, 2));
      if (distance < 20) {
        // Calculate the new direction and velocity of the object ball based on the angle of the collision
        let angle = Math.atan2(objectBall.y - cueBall.y, objectBall.x - cueBall.x);
        let newDirection = {
          x: Math.cos(angle),
          y: Math.sin(angle),
        };
        let newVelocity = velocity * 0.9;

        // Animate the movement of the object ball
        let objectInterval = setInterval(() => {
          // Check if the object ball has hit a wall
          if (objectBall.x < 20 || objectBall.x > 480 || objectBall.y < 20 || objectBall.y > 480) {
            newDirection.x = -newDirection.x;
            newDirection.y = -newDirection.y;
          }

          objectBall.x += newDirection.x * newVelocity;
          objectBall.y += newDirection.y * newVelocity;

          // Check if the object ball has fallen into a pocket
          for (let pocket of pockets) {
            let pocketDistance = Math.sqrt(Math.pow(objectBall.x - pocket.x, 2) + Math.pow(objectBall.y - pocket.y, 2));
            if (pocketDistance < 20) {
              clearInterval(objectInterval);
              objectBall.x = pocket.x;
              objectBall.y = pocket.y;
              break;
            }
          }
        }, 50);
      }

      // Stop the animation when the cue ball has stopped moving
      if (velocity <= 0) {
        clearInterval(interval);
      }
      velocity -= 0.1;
    }, 50);
  }
</script>

<div class="container">
  <!-- Cue ball -->
  <div class="ball" style="left: {cueBall.x}px; top: {cueBall.y}px; background-color: {cueBall.color};" />
  <!-- Object ball -->
  <div class="ball" style="left: {objectBall.x}px; top: {objectBall.y}px; background-color: {objectBall.color};" />
  <!-- Cue stick -->
  <div class="cue" style="left: {cueBall.x + 10}px; top: {cueBall.y - 50}px; transform: rotate({cueAngle}deg);" />
  <!-- Pockets -->
  {#each pockets as pocket}
    <div class="pocket" style="left: {pocket.x}px; top: {pocket.y}px;" />
  {/each}
  <!-- Angle input -->
  <input type="range" bind:value={cueAngle} min="0" max="360" step="1" />
  <button on:click={shootCueBall}>Shoot</button>
</div>

<style>
  .container {
    width: 500px;
    height: 500px;
    border: 1px solid black;
    background-color: #4cb254;
    position: relative;
  }

  .ball {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    position: absolute;
  }

  .cue {
    width: 2px;
    height: 100px;
    background-color: black;
    position: absolute;
  }

  .pocket {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background-color: black;
    position: absolute;
  }
</style>
