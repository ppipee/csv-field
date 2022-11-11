<script>
  import { getContext } from 'svelte'
  import CsvField from './components/CsvField.svelte'
  import { createEventDispatcher } from 'svelte'
  import Field from '@budibase/bbui/src/Form/Field.svelte'

  export let label = ''
  export let dragZoneText = ''
  export let hideImportButton = false
  export let importButtonLabel = ''
  export let onChange = () => {}

  const dispatch = createEventDispatcher()
  const stateStore = getContext('state')
  console.log('🔥 ~ stateStore', stateStore)

  let data = []
  let isParsed = false

  const { styleable } = getContext('sdk')
  const component = getContext('component')

  const handleChange = e => {
    const data = e.detail
    console.log('🔥 ~ data', data)

    onChange(e.detail)
    dispatch('change', { value: data })
    stateStore?.actions.setValue('csvData', data)
  }
</script>

<div use:styleable={$component.styles}>
  <Field {label} labelPosition="above">
    <CsvField {dragZoneText} on:change={handleChange} bind:data bind:isParsed />
  </Field>
</div>
