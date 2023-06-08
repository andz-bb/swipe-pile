<script>
  import { getContext } from "svelte";
  import { writable } from "svelte/store";
  import { Swipe } from "svelte-swipe";
  import Card from "./Card.svelte";

  // Fetch component props from context
  const { styleable } = getContext("sdk");
  const component = getContext("component");

  export let dataProvider;
  export let imageColumn;
  export let isSwipedColumn;
  export let onSwipeLeft;
  export let onSwipeRight;

  const index = writable(0);

  $: loading = dataProvider?.loading ?? false;
  $: rows = dataProvider?.rows || [];

  $: card = rows && rows[$index];

  const swipeCard = (direction) => {
    // Update the swiped status of the current card
    card[isSwipedColumn] = true;
    // dataProvider.updateRow(card);

    // Run the corresponding event
    if (typeof onSwipeLeft === "function" && direction === "left") {
      onSwipeLeft({ row: card });
    } else if (typeof onSwipeRight === "function") {
      onSwipeRight({ row: card });
    }

    // Go to the next card
    if ($index < rows.length) {
      index.update((n) => n + 1);
    }
  };
</script>

<div class="swipe-pile" use:styleable={$component.styles}>
  {#if rows.length > 0}
    {#if $index < rows.length}
      <Swipe
        on:swipeLeft={() => swipeCard("left")}
        on:swipeRight={() => swipeCard("right")}
      >
        <Card imageUrl={card[imageColumn]} />
      </Swipe>
      <button on:click={() => swipeCard("left")}>Swipe Left</button>
      <button on:click={() => swipeCard("right")}>Swipe Right</button>
    {:else}
      <div class="well-done">Nice work</div>
    {/if}
  {:else}
    <div class="placeholder">No data available</div>
  {/if}
</div>

<style>
  .swipe-pile {
    position: relative;
    width: 100%;
  }
  .placeholder,
  .well-done {
    color: var(--spectrum-global-color-gray-600);
  }
  .well-done {
    font-size: 2em;
    text-align: center;
    padding: 1em;
  }
</style>
