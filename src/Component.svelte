<script>
  import { getContext } from 'svelte'
  import CsvField from './components/CsvField.svelte'
  import Field from './components/Field.svelte'

  export let field
  export let label = ''
  export let dragZoneText = ''
  export let hideImportButton = false
  export let importButtonLabel = ''
  export let onChange
  export let disabled = false

  const { Provider } = getContext('sdk')

  let isParsed = false
  let changed = false
  let fieldState
  let fieldApi

  $: dataContext = {
    data: fieldState?.value || [],
  }

  $: changed && onUpdate(fieldState)

  const handleChange = e => {
    const dataChanged = fieldApi.setValue(e.detail)

    if (onChange && dataChanged) {
      changed = true
    }
  }

  const onUpdate = fieldState => {
    console.log('🔥 ~ fieldState.value', fieldState.value)
    onChange({ value: fieldState.value })
    changed = false
  }
</script>

<Field
  {label}
  {field}
  {disabled}
  type="array"
  bind:fieldState
  bind:fieldApi
  defaultValue={[]}
>
  <Provider data={dataContext}>
    <CsvField
      {dragZoneText}
      on:change={handleChange}
      bind:isParsed
      {fieldState}
    />
    <slot />
  </Provider>
</Field>
