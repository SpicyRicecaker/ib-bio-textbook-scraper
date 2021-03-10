<script lang="ts">
  let pg: number = 1;
  let svg = '';
  let ticking: boolean = false;
  const TEXTBOOK_LEN = 729;

  // class textbook {
  //   pg: number;
  //   private BOOK_LEN = 729;
  //   constructor() {}
  // }

  const adjust = async (_newPg: number) => {
    try {
      const stream = await fetch(`textbook/p${pg}.svg`);
      svg = await stream.text();
    } catch (e) {}
  };

  const decrement = () => (pg - 1 < 1 ? pg : pg--);
  const increment = () => (pg + 1 > TEXTBOOK_LEN ? pg : pg++);

  $: {
    adjust(pg);
  }

  const handleKeyDown = (e: KeyboardEvent) => {
    switch (e.code) {
      case 'ArrowRight': {
        increment();
        break;
      }
      case 'ArrowLeft': {
        decrement();
        break;
      }
      default: {
        break;
      }
    }
  };

  const handleWheel = (deltaY: number) => {
    // If we're scrolling down
    if (deltaY > 0) {
      increment();
    }
    // If we're scrolling up
    else {
      decrement();
    }
  };

  const throttleWheel = (e: WheelEvent) => {
    if (!ticking) {
      window.requestAnimationFrame(() => {
        handleWheel(e.deltaY);
        ticking = false;
      });

      ticking = true;
    }
  };
</script>

<svelte:window on:keydown={(e) => handleKeyDown(e)} on:wheel={throttleWheel} />

<main>
  <div class="page-input">
    <div>
      p. <input
        type="number"
        bind:value={pg}
        on:scroll={(e) => e.preventDefault()}
      />
      <span class="comment">// scroll or use &#8592; and &#8594;</span>
    </div>
  </div>
  <div class="svg">{@html svg}</div>
</main>

<style lang="scss">
  :global(body, html) {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
  }

  main {
    width: 100%;
    height: 100%;
    display: grid;
    grid-template-rows:
      auto
      minmax(0, 1fr);
  }

  .page-input {
    align-self: stretch;
    justify-self: stretch;
    display: grid;
    & > * {
      justify-self: center;
      & > input {
        border: none;
        border-bottom: 1px solid #2b2b2b;
        border-radius: 0%;
        outline: none;

        // disable input
        -moz-appearance: textfield;
        &::-webkit-outer-spin-button,
        &::-webkit-inner-spin-button {
          -webkit-appearance: none;
          margin: 0;
        }
      }
    }
  }
  .svg {
    display: grid;
    height: 100%;
    max-height: 100%;
    width: 100%;
    max-width: 100%;

    justify-self: end;
    align-self: center;
    & > :global(svg, img) {
      display: block;
      width: auto;
      height: 100%;
      max-width: 100%;
      max-height: 100%;
      object-fit: contain;

      align-self: center;
      justify-self: center;
    }
  }

  main {
    overflow: hidden;
  }

  .comment {
    color: lightgrey;
  }
</style>
