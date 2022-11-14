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
  let ref = { isChanged: false }
  let data = fieldState?.value?.[field]
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

  $: dataContext = {
    data: data || [],
  }

  // workaround, because onChange event cannot pass value yet.
  $: onValueChange(fieldState)
  console.log('🔥 ~ fieldState?.value', fieldState)

  const handleChange = e => {
    const changed = fieldApi.setValue(e.detail)
    console.log('🔥 ~ changed-out', changed, e.detail, onChange, fieldApi)

    if (changed) {
      console.log('🔥 ~ changed', changed)
      ref.isChanged = true
    }
  }

  const onValueChange = fieldState => {
    console.log('🔥 ~ data', fieldState?.value, ref.isChanged)
    if (ref.isChanged) {
      onChange?.(fieldState)
      ref.isChanged = false
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
