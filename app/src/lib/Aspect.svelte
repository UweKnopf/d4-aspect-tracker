<script lang="ts">
  import AddOwned from './AddOwned.svelte'
  import ListOwned from './ListOwned.svelte'
  import { createEventDispatcher } from 'svelte'

  const dispatch = createEventDispatcher()

  export let aspect

  function formatText(text) {
    const regex = /(x?){([^}]+)}(%?)/g
    const matches = text.matchAll(regex)

    let replace = text

    for (const match of matches) {
      const [keyword, x, range, percent] = match
      const stats = `<span class="text-blue-500">${x}[${range?.replace(
        '/',
        '-'
      )}]${percent}</span>`
      replace = replace.replace(keyword, stats)
    }

    return replace
  }

  function handleAspectUpdated() {
    ownedAspects = JSON.parse(localStorage.getItem(aspect.name)) || []
    dispatch('aspectUpdated')
  }

  $: ownedAspects = JSON.parse(localStorage.getItem(aspect.name)) || []
</script>

<div class="max-w-sm mb-10 border rounded-lg">
  <div style="padding:10px">
    <h3 class="text-lg font-medium mb-2 text-amber-600">
      {aspect.name}, {aspect.category}
      {aspect.in_codex ? '(Codex)' : ''}
    </h3>
    <p class="text-base mb-4">{@html formatText(aspect.desc)}</p>

    {#if ownedAspects.length > 0}
      <ListOwned
        aspectName={aspect.name}
        {ownedAspects}
        on:aspectUpdated={handleAspectUpdated}
      />
    {/if}
    <AddOwned
      aspectName={aspect.name}
      {ownedAspects}
      on:aspectUpdated={handleAspectUpdated}
    />
  </div>
</div>
