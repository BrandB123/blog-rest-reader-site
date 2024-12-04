<script lang="ts">
    import { onMount } from "svelte";
    import { goto } from "$app/navigation";
  
    let { data } = $props();
    let post = $state({});
    let comments = $state([]);
  
    async function getPost(postId: number){
        try{
            const response = await fetch(
                `${import.meta.env.VITE_API_URL}api/posts/${postId}/`, {
                headers: {
                    "Content-Type": "application/json",                }
            });
        
            if (response.status === 401){
                goto('/log-in');
            }
        
            if (!response.ok){
                throw new Error("Response Status: ", response.status);
            }
        
            const json = await response.json();
            console.log("JSON: ", json)
            return json.post[0];

        } catch(err){
            console.error(err);
        }
    }

    async function getComments(){}

    async function addComment(){}

    async function deleteComment(){}
  
    onMount(async () => {
        post = await getPost(Number(data.postId))
        comments = [];
        console.log(post)
        // title = post.title;
        // message = post.message;
        // published = post.publishedStatus;
    })
  
</script>

{#await post}
<p class="text-center mt-8">
    Loading posts...
</p>

{:then post}
    <div
    class="border border-black rounded-xl my-4 mx-8 p-4 bg-neutral-200 relative">
        <h1 
            class="text-4xl font-bold pl-4 mt-4">
            {post.title}
        </h1>
        <p 
            class="text-lg p-5 ml-4">
            {post.message}
        </p>
    </div>

{:catch error}
    <p class="text-center mt-8">
        Error loading posts. Please try again later
    </p>
{/await}

{#await comments}
<p class="text-center mt-8">
    Loading Comments...
</p>

{:then comments}
    <div
    class="border border-black rounded-xl my-4 mx-8 p-4 bg-neutral-200 relative">
        <h2
            class="text-xl p-4 m-2">
            Comments:
        </h2>
        
    </div>
{:catch error}
    <p class="text-center mt-8">
        Error loading posts. Please try again later
    </p>
{/await}

