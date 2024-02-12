<script>
    import { appStatus } from "../store.ts"
    import Dropzone from "svelte-file-dropzone"

    let files = {
    accepted: [],
    rejected: [],
    };

    function handleFilesSelect(e) {
        const { acceptedFiles, fileRejections } = e.detail;
        files.accepted = [...files.accepted, ...acceptedFiles]
        files.rejected = [...files.rejected, ...fileRejections]
    }
</script>
<Dropzone accept="application/pdf"  multiple={false}  on:drop={handleFilesSelect} > Arrastra y suelta tu PDF </Dropzone>
<ol>
        {#each files.accepted as item}
        <li>{item.name}</li>
    {/each}
    </ol>

