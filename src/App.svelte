<script lang="ts">
  import { onMount } from "svelte";

  // Player (atributos do jogador)
  let player = {
    top: 0, // Posição inicial no eixo Y
    left: 0, // Posição inicial no eixo X
    width: 50,
    height: 50,

    right: function() {
      return this.left + this.width
    },

    bottom: function() {
      return this.top + this.height;
    }
  };

  // Lista de obstáculos
  let obstacles = [
    { top: getRandomY(), left: getRandomX(), width: 100, height: 100 },
    { top: getRandomY(), left: getRandomX(), width: 100, height: 100 },
    { top: getRandomY(), left: getRandomX(), width: 100, height: 100 }
  ];

  function getRandomX() {
    return Math.min((Math.floor(Math.random() * 120) * 10), 1100)
  }

  function getRandomY() {
    return Math.min((Math.floor(Math.random() * 70) * 10), 550)
  }

  // Função para verificar colisão com qualquer obstáculo
  function checkCollision() {
    return obstacles.some(obstacle => {
      const playerRight = player.left + player.width;
      const playerBottom = player.top + player.height;

      const obstacleRight = obstacle.left + obstacle.width;
      const obstacleBottom = obstacle.top + obstacle.height;

      return (
        player.left < obstacleRight &&
        player.right() > obstacle.left &&
        player.top < obstacleBottom &&
        player.bottom() > obstacle.top
      );
    });
  }

  // Função para mover o player
  function movePlayer(direction: string) {
    const step = 10; // Quantidade de pixels por movimento
    const previousPosition = { ...player }; // Armazena a posição antes de mover

    switch (direction) {
      case 'up':
        player.top = Math.max(player.top - step, 0);
        break;
      case 'down':
        player.top = Math.min(player.top + step, 700 - player.height);
        break;
      case 'left':
        player.left = Math.max(player.left - step, 0);
        break;
      case 'right':
        player.left = Math.min(player.left + step, 1200 - player.width);
        break;
    }

    // Voltar para a posição anterior se colidir
    if (checkCollision()) {
      player = { ...previousPosition };
    }
  }

  // Captura eventos do teclado
  function handleKeydown(event: KeyboardEvent) {
    switch (event.key) {
      case 'ArrowUp':
        movePlayer('up');
        break;
      case 'ArrowDown':
        movePlayer('down');
        break;
      case 'ArrowLeft':
        movePlayer('left');
        break;
      case 'ArrowRight':
        movePlayer('right');
        break;
    }
  }

  // Adiciona o evento ao carregar o componente
  onMount(() => {
    window.addEventListener('keydown', handleKeydown);
    return () => {
      window.removeEventListener('keydown', handleKeydown);
    };
  });
</script>

<div class="game border border-zinc-50 mt-20 relative">
  <!-- Player -->
  <div
    class={`player border border-indigo-900 bg-pink-500`}
    style="
      position: absolute;
      top: {player.top}px;
      left: {player.left}px;
      width: {player.width}px;
      height: {player.height}px;
    "
  ></div>

  <!-- Obstáculos -->
  {#each obstacles as obstacle}
    <div
      class="obstacle border border-pink-900 bg-blue-800"
      style="
        position: absolute;
        top: {obstacle.top}px;
        left: {obstacle.left}px;
        width: {obstacle.width}px;
        height: {obstacle.height}px;
      "
    >
      {obstacle.left}, {obstacle.top}
  </div>
  {/each}
</div>

<div class="data flex flex-col">
  <span>player left: {player.left}</span>
  <span>player top: {player.top}</span>


</div>

<style>
  .game {
    height: 700px;
    width: 1200px;
    position: relative;
    overflow: hidden;
  }

</style>
