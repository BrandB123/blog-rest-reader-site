<script lang="ts">
    import { onMount } from "svelte";
    import { goto } from "$app/navigation";

    let posts;

    async function getPosts(){
        try {
            const response = await fetch(`${import.meta.env.VITE_API_URL}api/posts/`, {
                headers: {
                    "Content-Type" : "application/json",
                }
            })

            if (!response.ok){
                throw new Error("Response Status: ", response.status)
            }

            const json = await response.json();

            return json.posts

        } catch(err){
            console.error(err);
        }
    }


    onMount(async () => {
        posts = getPosts();
    });

</script>

<h1 class="text-4xl pl-4 mt-4">Posts</h1>

{#await posts}
<p class="text-center mt-8">
    Loading posts...
</p>

{:then posts}
    {#each posts as post}
        <div
        class="border border-black rounded-xl my-4 mx-8 p-4 bg-neutral-200 relative">
            <h4 class="font-bold text-xl">
                {post.title}
            </h4>
            <p class="text-sm">
                {post.message}
            </p>
            <a
            href="/{post.id}/"
            class="m-2 p-1 text-xs absolute right-2 bottom-0 hover:font-bold">
                comments
            </a>
        </div>
    {:else}
        <p class="text-center mt-8">
            No blog posts currently.
        </p>
    {/each}
{:catch error}
    <p class="text-center mt-8">
        Error loading posts. Please try again later
    </p>
{/await}