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
  let formState
  console.log('🔥 ~ formState', formState)

  $: dataContext = {
    data: formState?.[field] || [],
  }

  $: onUpdate(formState?.[field])

  const handleChange = e => {
    const dataChanged = fieldApi.setValue(e.detail)

    if (onChange && dataChanged) {
      changed = true
    }
  }

  const onUpdate = formValue => {
    if (changed) {
      console.log('🔥 ~ formValue', formValue)
      onChange({ value: formValue })
      changed = false
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
  bind:formState
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
