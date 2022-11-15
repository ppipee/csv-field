<script>
  import Dropzone from 'svelte-file-dropzone'
  import Papa from 'papaparse'
  import { createEventDispatcher } from 'svelte'

  export let dragZoneText
  export let isParsed

  let file = {}

  const dispatch = createEventDispatcher()

  const parseCsv = file =>
    new Promise(resolve => {
      Papa.parse(file, {
        header: true,
        skipEmptyLines: true,
        complete: result => {
          resolve(result.data)
        },
      })
    })

  const onFileChange = async e => {
    const { acceptedFiles } = e.detail

    if (acceptedFiles.length === 0) {
      file = null
      return
    }

    file = acceptedFiles[0]

    const data = await parseCsv(file)

    isParsed = true

    dispatch('change', data)
  }

  const removeFile = () => {
    file = {}
    isParsed = false
  }
</script>

{#if !isParsed}
  <Dropzone on:drop={onFileChange} multiple="false">{dragZoneText}</Dropzone>
{:else}
  <div class="gallery">
    <div class="title">
      <div class="filename">{file.name}</div>
      {#if file.size}
        <div class="filesize">
          {`${file.size / 1024} KB`}
        </div>
      {/if}
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <div class="delete-button" on:click={removeFile}>X</div>
    </div>
  </div>
{/if}

<style>
  .gallery {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: stretch;
    background-color: var(--spectrum-global-color-gray-50);
    color: var(--spectrum-alias-text-color);
    font-size: var(--spectrum-alias-item-text-size-m);
    box-sizing: border-box;
    border: var(--spectrum-alias-border-size-thin)
      var(--spectrum-alias-border-color) solid;
    border-radius: var(--spectrum-alias-border-radius-regular);
    padding: 10px;
    margin-bottom: 10px;
    position: relative;
  }

  .title {
    display: flex;
    align-items: center;
  }

  .filename {
    flex: 1 1 auto;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    width: 0;
    margin-right: 10px;
  }

  .delete-button {
    transition: all 0.3s;
    margin-left: 10px;
    display: flex;
  }
</style>
