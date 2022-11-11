<script>
  import { getContext } from 'svelte'
  import CsvField from './components/CsvField.svelte'
  import { createEventDispatcher } from 'svelte'

  export let label = ' '
  export let dragZoneText = ''
  export let hideImportButton = false
  export let importButtonLabel = ''

  const dispatch = createEventDispatcher()
  const stateStore = getContext('state')
  console.log('🔥 ~ stateStore', stateStore)

  let data = []
  let isParsed = false

  const { styleable } = getContext('sdk')
  const component = getContext('component')

  const handleChange = e => {
    const data = e.detail

    dispatch('change', data)
    stateStore?.actions.setValue('csvData', data)
  }
</script>

<div use:styleable={$component.styles}>
  <CsvField
    {label}
    {dragZoneText}
    on:change={handleChange}
    bind:data
    bind:isParsed
  />
</div>
