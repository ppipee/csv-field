<script>
  import { getContext, onDestroy } from 'svelte'
  import CsvField from './components/CsvField.svelte'
  import { createEventDispatcher } from 'svelte'

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

  const dispatch = createEventDispatcher()

  let data = []
  let isParsed = false

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

  let fieldApi

  $: unsubscribe = formField?.subscribe(value => {
    fieldApi = value?.fieldApi
  })

  $: fieldApi?.setValue(data)

  $: dataContext = {
    data,
  }

  const handleChange = e => {
    console.log('🔥 ~ data', data)

    dispatch('change', data)
    const changed = fieldApi.setValue(e.detail)

    if (onChange && changed) {
      console.log('🔥 ~ changed', changed)
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
      bind:data
      bind:isParsed
    />
    <slot />
  </Provider>
</div>
