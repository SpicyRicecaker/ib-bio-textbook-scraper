<script lang="ts">
  let pg = 1;
  let svg = '';

  const adjust = async (nPg: number) => {
    try {
      const stream = await fetch(`textbook/p${pg}.svg`);
      svg = await stream.text();
    } catch (e) {
      console.log(e);
    }
  };

  $: {
    adjust(pg);
  }

  const handleKeyDown = (e: KeyboardEvent) => {
    switch (e.code) {
      case 'ArrowRight': {
        pg += 1;
        break;
      }
      case 'ArrowLeft': {
        pg -= 1;
        break;
      }
      default: {
        break;
      }
    }
  };
</script>

<svelte:window on:keydown={(e) => handleKeyDown(e)} />

<main>
  <div class="page-input">
    <input bind:value={pg} type="text" />
  </div>
  <div class="svg">{@html svg}</div>
</main>

<style lang="scss">
  :global(body, html) {
    margin: 0;
    padding: 0;
  }

  main {
    display: grid;
  }

  .page-input {
    align-self: center;
    justify-self: center;
  }

  .svg {
    margin: 0 auto;
    width: 75vh;
  }

  main {
    overflow: hidden;
  }
</style>
