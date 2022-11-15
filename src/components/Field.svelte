<script>
  import Placeholder from './Placeholder.svelte'
  import { getContext, onDestroy } from 'svelte'

  export let label
  export let field
  export let fieldState
  export let fieldApi
  export let defaultValue
  export let type
  export let disabled = false
  export let validation

  // Get contexts
  const formContext = getContext('form')
  console.log('🔥 ~ formContext', formContext)
  const formStepContext = getContext('form-step')
  const { styleable, builderStore } = getContext('sdk')
  const component = getContext('component')

  formContext?.formState?.subscribe(value => {
    console.log('🔥 ~ value', value)
  })

  // Register field with form
  const formApi = formContext?.formApi
  $: formStep = formStepContext ? $formStepContext || 1 : 1
  $: formField = formApi?.registerField(
    field,
    type,
    defaultValue,
    disabled,
    validation,
    formStep
  )

  let labelNode
  $: $component.editing && labelNode?.focus()

  $: unsubscribe = formField?.subscribe(value => {
    fieldState = value?.fieldState
    fieldApi = value?.fieldApi
  })

  const updateLabel = e => {
    builderStore.actions.updateProp('label', e.target.textContent)
  }

  onDestroy(() => {
    fieldApi?.deregister()
    unsubscribe?.()
  })
</script>

<div class="container" use:styleable={$component.styles}>
  {#key $component.editing}
    <label
      bind:this={labelNode}
      contenteditable={$component.editing}
      on:blur={$component.editing ? updateLabel : null}
      class:hidden={!label}
      for={fieldState?.fieldId}
      class={`spectrum-FieldLabel spectrum-FieldLabel--sizeM spectrum-Form-itemLabel`}
    >
      {label || ' '}
    </label>
  {/key}
  <div class="spectrum-Form-itemField">
    {#if !formContext}
      <Placeholder text="Form components need to be wrapped in a form" />
    {:else if !fieldState}
      <Placeholder />
    {:else}
      <slot />
      {#if fieldState.error}
        <div class="error">{fieldState.error}</div>
      {/if}
    {/if}
  </div>
</div>

<style>
  .container {
    display: flex;
    flex-direction: column;
  }

  label {
    white-space: nowrap;
  }
  label.hidden {
    padding: 0;
  }
  .spectrum-Form-itemField {
    position: relative;
    width: 100%;
  }
  .error {
    color: var(
      --spectrum-semantic-negative-color-default,
      var(--spectrum-global-color-red-500)
    );
    font-size: var(--spectrum-global-dimension-font-size-75);
    margin-top: var(--spectrum-global-dimension-size-75);
  }
</style>
