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
  let fieldState
  let fieldApi

  $: dataContext = {
    data: fieldState?.value || [],
  }

  const handleChange = e => {
    const changed = fieldApi.setValue(e.detail)

    // workaround
    window['csvData'] = e.detail
    console.log('🔥 ~ window', window['csvData'])

    if (onChange && changed) {
      onChange({ value: e.detail })
    }
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
