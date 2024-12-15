<script lang="ts">
    import { onMount } from "svelte";
    import { goto } from "$app/navigation";

    type Post = {
        id: number;
        author_id: number;
        title: string;
        message: string;
        published: boolean;
        timestamp: Date;
    }

    let posts: Post[];


    async function getPosts(){
        try {
            const response = await fetch(`${import.meta.env.VITE_API_URL}api/posts/`, {
                headers: {
                    "Content-Type" : "application/json",
                }
            })

            if (!response.ok){
                throw new Error(`Response Status: ${response.status}`)
            }

            const json = await response.json();

            return json.posts

        } catch(err){
            console.error(err);
        }
    }


    onMount(async () => {
        if (!localStorage.getItem("jwtAuth")){
            goto("/log-in")
        }
        posts = await getPosts();
    });

</script>

<h1 class="text-4xl pl-4 mt-4">Posts</h1>

{#await posts}
<p class="text-center mt-8">
    Loading posts...
</p>

{:then posts}
    {#each posts as post}
        {#if post.published}
            <div
            class="border border-black rounded-xl my-4 mx-8 pt-4 pb-6 px-4 bg-neutral-200 relative">
                <h4 class="font-bold text-xl">
                    {post.title}
                </h4>
                <p class="text-sm">
                    {post.message}
                </p>
                <a
                href="/{post.id}/"
                class="mx-2 p-1 text-xs absolute right-2 bottom-1 hover:font-bold">
                    comments
                </a>
            </div>
        {/if}
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