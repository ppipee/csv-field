<script>
  import { getContext } from 'svelte'
  import CsvField from './components/CsvField.svelte'
  import { createEventDispatcher } from 'svelte'

  export let label = ''
  export let dragZoneText = ''
  export let hideImportButton = false
  export let importButtonLabel = ''
  export let onChange = () => {}

  const dispatch = createEventDispatcher()

  const { styleable, Provider } = getContext('sdk')
  const component = getContext('component')

  let data = []
  let isParsed = false

  $: dataContext = {
    data,
  }

  const handleChange = e => {
    const data = e.detail
    console.log('🔥 ~ data', data)

    onChange(e.detail)
    dispatch('change', { value: data })
  }
</script>

<div use:styleable={$component.styles}>
  <Provider data={dataContext}>
    <CsvField
      {label}
      {dragZoneText}
      on:change={handleChange}
      bind:data
      bind:isParsed
    />
  </Provider>
</div>
