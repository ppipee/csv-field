<script>
  import { getContext, onDestroy } from 'svelte'
  import CsvField from './components/CsvField.svelte'
  import Field from '@budibase/bbui/src/Form/Field.svelte'

  export let field
  export let label = ''
  export let dragZoneText = ''
  export let hideImportButton = false
  export let importButtonLabel = ''
  export let onChange

  const { styleable, Provider } = getContext('sdk')
  const component = getContext('component')
  // const formContext = getContext('form')
  // const formStepContext = getContext('form-step')

  let fieldState
  let fieldApi
  let isParsed = false

  // const formApi = formContext?.formApi
  // $: formStep = $formStepContext ?? 1
  // $: formField = formApi?.registerField(
  //   field,
  //   'array',
  //   [],
  //   false,
  //   null,
  //   formStep
  // )

  // $: unsubscribe = formField?.subscribe(value => {
  //   fieldApi = value?.fieldApi
  // })

  // $: fieldApi?.setValue(data)

  $: dataContext = {
    data,
  }

  const handleChange = e => {
    const changed = fieldApi.setValue(e.detail)

    if (onChange && changed) {
      onChange({ value: e.detail })
    }
  }

  // onDestroy(() => {
  //   fieldApi?.deregister()
  //   unsubscribe?.()
  // })
</script>

<div use:styleable={$component.styles}>
  <Field
    {label}
    {field}
    defaultValue={[]}
    type="array"
    bind:fieldState
    bind:fieldApi
  >
    <Provider data={dataContext}>
      <CsvField
        {label}
        {dragZoneText}
        on:change={handleChange}
        data={fieldState.value}
        bind:isParsed
      />
      <slot />
    </Provider>
  </Field>
</div>
