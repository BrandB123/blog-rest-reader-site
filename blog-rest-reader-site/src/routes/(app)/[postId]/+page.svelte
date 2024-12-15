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

    type Comment = {
        id: number;
        message: string;
        name: string
    }

  
    let { data } = $props();
    let post = $state({}) as Post;
    let comments = $state([]) as Comment[];
    let commentMessage = $state("");
  
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
                throw new Error(`Response Status: ${response.status}`);
            }
        
            const json = await response.json();
            console.log("JSON: ", json)
            return json.post[0];

        } catch(err){
            console.error(err);
        }
    }

    async function getComments(){
        try {
            const response = await fetch(`${import.meta.env.VITE_API_URL}api/posts/${data.postId}/comments`, {
                headers: {
                    "Content-Type": "application/json",
                }
            })

            if (!response.ok){
                throw new Error(`Response Status: ${response.status}`)
            }

            const json = await response.json();
            return json.comments;
            

        } catch(err) {
            console.error(err);
        }
    }

    async function addComment(comment: string){
        try{
            const response = await fetch(`${import.meta.env.VITE_API_URL}api/posts/${data.postId}/comments`, {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    "Authorization": `Bearer ${localStorage.getItem("jwtAuth")}`
                },
                body: JSON.stringify({
                    message: comment
                })
            })

            // HANDLE SPECIFIC ERRORS HERE

            if (!response.ok){
                throw new Error(`Response Status: ${response.status}`);
            }

            comments = await getComments();

        } catch(err) {
            console.error(err);
        }
    }
  
    onMount(async () => {
        post = await getPost(Number(data.postId))
        comments = await getComments();
        console.log(post)
        console.log(comments)
    })

</script>

{#await post}
    <p class="text-center mt-8">
        Loading posts...
    </p>

{:then post}
    <div class="border border-black rounded-xl my-4 mx-8 p-4 bg-neutral-200 relative">
        <h1 
            class="text-xl lg:text-4xl font-bold pl-2 lg:pl-4 lg:mt-4 ">
            {post.title}
        </h1>
        
        <p 
            class="text-md lg:text-lg lg:p-5 ml-4">
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
    <div class="border border-black rounded-xl my-4 mx-8 p-4 bg-neutral-200 relative">
        <form action="/{data.postId}" class=" mx-2 lg:mx-8 w-11/12 relative">
	        <input 
                type="text" 
                placeholder="Add a comment" 
                bind:value={commentMessage}
                class="w-full pl-2 pr-20 py-3 rounded-xl bg-neutral-100">
	        <button 
                type="submit" 
                class="bg-blue-600 text-neutral-100 px-2 py-1 rounded-2xl absolute top-2 right-4"
                onclick={async (event) => {
                    event.preventDefault()
                    addComment(commentMessage);
                    commentMessage = "";
	            }}>
	            Send
	        </button>
	    </form>
       
        <h2 class="text-xl font-medium px-4 m-2 border-b-2 border-solid border-neutral-400 ">
            Comments:
        </h2>


        {#each comments as comment}
            <div class=" rounded-2xl my-4 mx-4 lg:mx-8 p-4 lg:w-3/4 bg-neutral-100 relative">
                <p class="text-xs lg:text-sm">
                    {comment.name}
                </p>
                <p class="text-md lg:text-xl ml-1 lg:ml-4">
                    {comment.message}
                </p>
            </div> 
        {:else}
            <p class="text-center mt-8">
                There are currently no comments on this post.
            </p>
        {/each}
        
    </div>
{:catch error}
    <p class="text-center mt-8">
        Error loading comments.
    </p>
{/await}

