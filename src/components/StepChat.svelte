<script>
    import { Label, Input, Spinner } from 'flowbite-svelte';
    import {appStatusInfo, setAppStatusError} from '../store.ts';
    const {url,pages,id} = $appStatusInfo;

    let answer = '';
    let loading = false;

    const numOfImagesToShow = Math.min(pages,4)
    const images = Array.from({ length: numOfImagesToShow }, (_,i) => {
        const page = i + 1
        return url
        .replace('/upload/', `/upload/w_1200,h_650,c_fill,pg_${page}/`)
        .replace('.pdf','.jpg')
    })

    const handleSubmit = async (event) => {
    const question = event.target['question'].value
    try{
        const res = await fetch("/api/ask", {
            method: "POST",
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                id,
                question,
            }),
        })

        if (res.ok) {
            loading = false
            console.error("Error asking question")
            return
        }

        const { answer: apiAnswer } = await res.json()
        answer = apiAnswer
    }
    catch (e) {
        setAppStatusError()
    }finally{
        loading = false
    }
}
</script>
<div class="grid grid-cols-4 gap-2">
    {#each images as image }
        <img class="w-full h-auto rounded " src={image} alt="PDF page" >
    {/each}
</div>

<form class="mt-8" on:submit={handleSubmit}>
    <Label for="question" class="block m-3 text-white ">Deja aqui tu pregunta</Label>
    <Input id="question" class="focus:ring-red-600" required  placeholder="¿De que trata este documento?">
    </Input>
</form>

{#if loading}
    <div class="flex justify-center items-center flex-col gap-y-2">
        <Spinner />
        <p class="text-red-500">Esperando respuesta...</p>
    </div>
{/if}

{#if answer}
    <p> Respuesta: </p>
    <p class="m-3 text-white">{answer}</p>
{/if}
```