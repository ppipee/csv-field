<script>
  import { getContext, onDestroy } from 'svelte'
  import CsvField from './components/CsvField.svelte'

  export let field
  export let label = ''
  export let dragZoneText = ''
  export let hideImportButton = false
  export let importButtonLabel = ''
  export let onChange

  const { styleable, Provider } = getContext('sdk')
  const component = getContext('component')
  const formContext = getContext('form')
  const formStepContext = getContext('form-step')

  let isParsed = false
  let fieldState
  let fieldApi

  const formApi = formContext?.formApi
  $: formStep = $formStepContext ?? 1
  $: formField = formApi?.registerField(
    field,
    'array',
    [],
    false,
    null,
    formStep
  )

  $: unsubscribe = formField?.subscribe(value => {
    fieldApi = value?.fieldApi
    fieldState = value?.fieldState
  })

  $: fieldApi?.setValue(data)

  $: dataContext = {
    data: fieldState?.value || [],
  }

  const handleChange = e => {
    console.log('🔥 ~ data', data)

    const changed = fieldApi.setValue(e.detail)

    if (onChange && changed) {
      onChange({ value: e.detail })
    }
  }

  onDestroy(() => {
    fieldApi?.deregister()
    unsubscribe?.()
  })
</script>

<div use:styleable={$component.styles}>
  <Provider data={dataContext}>
    <CsvField
      {label}
      {dragZoneText}
      on:change={handleChange}
      bind:isParsed
      {fieldState}
    />
    <slot />
  </Provider>
</div>
