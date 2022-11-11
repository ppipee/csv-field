<script>
  import { getContext, onDestroy } from 'svelte'
  import CsvField from './components/CsvField.svelte'
  import { createEventDispatcher } from 'svelte'

  export let field
  export let label = ''
  export let dragZoneText = ''
  export let hideImportButton = false
  export let importButtonLabel = ''

  const { styleable, Provider } = getContext('sdk')
  const component = getContext('component')
  const formContext = getContext('form')
  const formStepContext = getContext('form-step')

  const dispatch = createEventDispatcher()

  let isParsed = false
  let fieldState
  let fieldApi
  let data = fieldState?.value?.[field]

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

  $: dataContext = {
    data: data || [],
  }

  const handleChange = e => {
    console.log('🔥 ~ data', e.detail)

    const changed = fieldApi.setValue(e.detail)

    if (changed) {
      dispatch('change', e.detail)
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
